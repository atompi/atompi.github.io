---
title: 使用 os / exec 在 Go 中执行高级命令
date: 2019/12/01
tags: Go
description: Some old articles may have been deprecated.
---

## 0. Overview

本文翻译自 [Advanced command execution in Go with os/exec](https://blog.kowalczyk.info/article/wOYk/advanced-command-execution-in-go-with-osexec.html) 。

文中所有代码都可以在原作者开源项目 [go-cookbook](https://github.com/kjk/go-cookbook) 的 [advanced-exec](https://github.com/kjk/go-cookbook/blob/master/advanced-exec) 目录中获取。

Go对执行外部程序具有出色的支持。让我们从头开始。

在我们的示例中，我们将运行 `ls -lah` 命令，因为它会产生输出。 Windows 上没有 `ls` ，因此您可以将其更改为其他命令，例如 `tasklist`。

## 1. 运行命令

``` plain
cmd := exec.Command("ls", "-lah")
if runtime.GOOS == "windows" {
	cmd = exec.Command("tasklist")
}
err := cmd.Run()
if err != nil {
	log.Fatalf("cmd.Run() failed with %s\n", err)
}
```
完整代码： [advanced-exec/01-simple-exec-v1.go](https://github.com/kjk/go-cookbook/blob/master/advanced-exec/01-simple-exec-v1.go)

如果运行它，似乎什么也不会发生。不用担心，该命令实际上已被执行。如果我们在 `shell` 中运行 `ls -lah` ， `shell` 会将 `stdout` 和 `stderr` 输出到控制台，以便我们可以看到它。而我们在通过 Go 标准库函数执行程序时，默认情况下 `stdout` 和 `stderr` 被丢弃。

## 2. 运行命令并显示输出

为例看到输出，我们可以将正在执行的程序的输出（ `cmd.Stdout` 和 `cmd.Stderr` ）发送到 `os.Stdout` 和 `os.Stderr` ，代码如下：

``` plain
cmd := exec.Command("ls", "-lah")
cmd.Stdout = os.Stdout
cmd.Stderr = os.Stderr
err := cmd.Run()
if err != nil {
	log.Fatalf("cmd.Run() failed with %s\n", err)
}
```
完整代码： [advanced-exec/01-simple-exec-v2.go](https://github.com/kjk/go-cookbook/blob/master/advanced-exec/01-simple-exec-v2.go)

`cmd.Stdout` 和 `cmd.Stderr` 被声明为 `io.Writer` 接口，因此我们可以将它们设置为实现 `Write()` 方法的任何类型，例如 `os.File` 或内存中的缓冲区 `bytes.Buffer` 。`io.Reader` 和 `io.Writer` 是非常简单的但非常强大的且抽象的。

## 3. 运行命令并捕获输出

上面的示例使我们可以看到输出，但是有时我们想要捕获输出并进行分析，代码如下：

``` plain
func main() {
	cmd := exec.Command("ls", "-lah")
	out, err := cmd.CombinedOutput()
	if err != nil {
		log.Fatalf("cmd.Run() failed with %s\n", err)
	}
	fmt.Printf("combined out:\n%s\n", string(out))
}
```
完整代码 [advanced-exec/01-simple-exec-v3.go](https://github.com/kjk/go-cookbook/blob/master/advanced-exec/01-simple-exec-v3.go)

`CombinedOutput` 运行命令并返回包含 `stdout` 和 `stderr` 的返回值。

### 3.1 CombinedOutput 的实现原理

Go 的好处在于它是开源的，因此我们可以窥视给定的功能是如何实现的，同时标准库中的大多数代码都很简单。以下是 `CombinedOutput` 的实现方式：

``` plain
func (c *Cmd) CombinedOutput() ([]byte, error) {
	if c.Stdout != nil {
		return nil, errors.New("exec: Stdout already set")
	}
	if c.Stderr != nil {
		return nil, errors.New("exec: Stderr already set")
	}
	var b bytes.Buffer
	c.Stdout = &amp;b
	c.Stderr = &amp;b
	err := c.Run()
	return b.Bytes(), err
}
```
可以看出，这几乎和我们的第二个示例一样简单。我们没有将 `cmd.Stdout` 和 `cmd.Stderr` 设置为标准输出，而是将它们设置为单个内存缓冲区。程序完成后，我们将所有写入该缓冲区的内容返回。

因此，**不要害怕细读标准库的代码。**

## 4. 分别捕获 stdout 和 stderr

如果要执行相同操作但又分别捕获 `stdout` 和 `stderr` ，该怎么办？

``` plain
func main() {
	cmd := exec.Command("ls", "-lah")
	var stdout, stderr bytes.Buffer
	cmd.Stdout = &amp;stdout
	cmd.Stderr = &amp;stderr
	err := cmd.Run()
	if err != nil {
		log.Fatalf("cmd.Run() failed with %s\n", err)
	}
	outStr, errStr := string(stdout.Bytes()), string(stderr.Bytes())
	fmt.Printf("out:\n%s\nerr:\n%s\n", outStr, errStr)
}
```
完整代码： [advanced-exec/02-capture-stdout-stderr.go](https://github.com/kjk/go-cookbook/blob/master/advanced-exec/02-capture-stdout-stderr.go)

## 5. 捕获输出同时显示执行过程中的输出

对于有以下需求的程序：

- 命令需要很长时间才能完成
- 除了捕获 `stdout` / `stderr` 之外，还需要在控制台上看到它的执行过程。

此时我们需要在捕获输出的同时捕获执行过程。这里涉及到的东西有点多，但并不难理解。

### 5.1 捕获输出同时显示执行过程中的输出 #1

首先，一个帮助程序功能可以从读取器复制到写入器，还可以捕获复制的数据：

``` plain
func copyAndCapture(w io.Writer, r io.Reader) ([]byte, error) {
	var out []byte
	buf := make([]byte, 1024, 1024)
	for {
		n, err := r.Read(buf[:])
		if n &gt; 0 {
			d := buf[:n]
			out = append(out, d...)
			_, err := w.Write(d)
			if err != nil {
				return out, err
			}
		}
		if err != nil {
			// Read returns io.EOF at the end of file, which is not an error for us
			if err == io.EOF {
				err = nil
			}
			return out, err
		}
	}
}
```
处理 `Read` 中的错误非常微妙。 `io.EOF` 错误意味着我们已经读取了所有内容。这实际上不是错误，因此我们将 `io.EOF` 设置为 `nil` 。

最终代码如下：

``` plain
func main() {
	cmd := exec.Command("ls", "-lah")

	var stdout, stderr []byte
	var errStdout, errStderr error
	stdoutIn, _ := cmd.StdoutPipe()
	stderrIn, _ := cmd.StderrPipe()
	err := cmd.Start()
	if err != nil {
		log.Fatalf("cmd.Start() failed with '%s'\n", err)
	}

	// cmd.Wait() should be called only after we finish reading
	// from stdoutIn and stderrIn.
	// wg ensures that we finish
	var wg sync.WaitGroup
	wg.Add(1)
	go func() {
		stdout, errStdout = copyAndCapture(os.Stdout, stdoutIn)
		wg.Done()
	}()

	stderr, errStderr = copyAndCapture(os.Stderr, stderrIn)

	wg.Wait()

	err = cmd.Wait()
	if err != nil {
		log.Fatalf("cmd.Run() failed with %s\n", err)
	}
	if errStdout != nil || errStderr != nil {
		log.Fatal("failed to capture stdout or stderr\n")
	}
	outStr, errStr := string(stdout), string(stderr)
	fmt.Printf("\nout:\n%s\nerr:\n%s\n", outStr, errStr)
}
```
完整代码： [advanced-exec/03-live-progress-and-capture-v1.go](https://github.com/kjk/go-cookbook/blob/master/advanced-exec/03-live-progress-and-capture-v1.go)

我们有两个输出要复制。为避免序列化它们，我们将在 `goroutine` 中读取其中一个。

正如 `StdoutPipe` 的文档警告，在程序完成时 `Wait` 将关闭管道。如果我们还没有读完，可能会导致输出丢失。为防止这种情况，我们使用 `sync.WaitGroup` 来确保在调用 `cmd.Wait` 之前 `gorutine` 处理 `os.Stdout` 的操作完成了读取。

我鼓励你阅读 `cmd.StdoutPipe` 的实现。尽管代码很短，但是会让你感到惊讶。

### 5.2 捕获输出同时显示执行过程中的输出 ＃2

以前的解决方案有效，但 `copyAndCapture` 看起来像我们在重新实现 `io.Copy` 。由于 Go 使用了接口，因此我们可以重复使用 `io.Copy` 。

我们将编写实现 `io.Writer` 接口的 `CapturingPassThroughWriter` 结构。它将捕获写入其中的所有内容，并将其写入底层的 `io.Writer` 。

``` plain
// CapturingPassThroughWriter is a writer that remembers
// data written to it and passes it to w
type CapturingPassThroughWriter struct {
	buf bytes.Buffer
	w io.Writer
}

// NewCapturingPassThroughWriter creates new CapturingPassThroughWriter
func NewCapturingPassThroughWriter(w io.Writer) *CapturingPassThroughWriter {
	return &amp;CapturingPassThroughWriter{
		w: w,
	}
}

func (w *CapturingPassThroughWriter) Write(d []byte) (int, error) {
	w.buf.Write(d)
	return w.w.Write(d)
}

// Bytes returns bytes written to the writer
func (w *CapturingPassThroughWriter) Bytes() []byte {
	return w.buf.Bytes()
}

func main() {
	cmd := exec.Command("ls", "-lah")

		var errStdout, errStderr error
		stdoutIn, _ := cmd.StdoutPipe()
		stderrIn, _ := cmd.StderrPipe()
		stdout := NewCapturingPassThroughWriter(os.Stdout)
		stderr := NewCapturingPassThroughWriter(os.Stderr)
		err := cmd.Start()
		if err != nil {
			log.Fatalf("cmd.Start() failed with '%s'\n", err)
		}

		var wg sync.WaitGroup
		wg.Add(1)

		go func() {
			_, errStdout = io.Copy(stdout, stdoutIn)
			wg.Done()
		}()

		_, errStderr = io.Copy(stderr, stderrIn)
		wg.Wait()

	err = cmd.Wait()
	if err != nil {
		log.Fatalf("cmd.Run() failed with %s\n", err)
	}
	if errStdout != nil || errStderr != nil {
		log.Fatalf("failed to capture stdout or stderr\n")
	}
	outStr, errStr := string(stdout.Bytes()), string(stderr.Bytes())
	fmt.Printf("\nout:\n%s\nerr:\n%s\n", outStr, errStr)
}
```
完整代码： [advanced-exec/03-live-progress-and-capture-v2.go](https://github.com/kjk/go-cookbook/blob/master/advanced-exec/03-live-progress-and-capture-v2.go)

### 5.3 捕获输出同时显示执行过程中的输出 ＃3

事实上， Go 的标准库实现了 [`io.MultiWriter`](https://golang.org/pkg/io/#MultiWriter) ，它是 `CapturingPassThroughWriter` 的通用版本。让我们用它代替：

``` plain
func main() {
	cmd := exec.Command("ls", "-lah")

		var stdoutBuf, stderrBuf bytes.Buffer
	stdoutIn, _ := cmd.StdoutPipe()
	stderrIn, _ := cmd.StderrPipe()

	var errStdout, errStderr error
	stdout := io.MultiWriter(os.Stdout, &amp;stdoutBuf)
	stderr := io.MultiWriter(os.Stderr, &amp;stderrBuf)
	err := cmd.Start()
	if err != nil {
		log.Fatalf("cmd.Start() failed with '%s'\n", err)
	}

		var wg sync.WaitGroup
		wg.Add(1)

		go func() {
			_, errStdout = io.Copy(stdout, stdoutIn)
			wg.Done()
		}()

		_, errStderr = io.Copy(stderr, stderrIn)
		wg.Wait()

	err = cmd.Wait()
	if err != nil {
		log.Fatalf("cmd.Run() failed with %s\n", err)
	}
	if errStdout != nil || errStderr != nil {
		log.Fatal("failed to capture stdout or stderr\n")
	}
	outStr, errStr := string(stdoutBuf.Bytes()), string(stderrBuf.Bytes())
	fmt.Printf("\nout:\n%s\nerr:\n%s\n", outStr, errStr)

}
```
完整代码： [advanced-exec/03-live-progress-and-capture-v3.go](https://github.com/kjk/go-cookbook/blob/master/advanced-exec/03-live-progress-and-capture-v3.go)

能够自己编写代码是件好事，但更好地了解标准库则更好！

## 6. 写入程序的标准输入（ stdin ）

我们知道如何读取程序的 `stdout` ，但我们也可以写入其 `stdin` 。

目前没有 Go 标准库可以执行 `bzip2` 压缩（标准库中仅提供解压缩功能）。我们可以通过以下方式使用 `bzip2` 进行压缩：（将数据写入临时文件）调用 `bzip2 -c $ {file_in}` 并捕获其 `stdout`。

如果我们不创建临时文件就更好了。大多数压缩程序接受数据以在 `stdin` 上进行压缩 / 解压缩。要在命令行上执行此操作，我们将使用以下命令： `bzip2 -c &lt;$ {file_in}&gt; $ {file_out}` 。

下面就是使用 Go 实现这一操作：

``` plain
// compress data using bzip2 without creating temporary files
func bzipCompress(d []byte) ([]byte, error) {
	var out bytes.Buffer
	// -c : compress
	// -9 : select the highest level of compresion
	cmd := exec.Command("bzip2", "-c", "-9")
	cmd.Stdin = bytes.NewBuffer(d)
	cmd.Stdout = &amp;out
	err := cmd.Run()
	if err != nil {
		return nil, err
	}
	return out.Bytes(), nil
}
```
完整代码： [advanced-exec/06-feed-stdin.go](https://github.com/kjk/go-cookbook/blob/master/advanced-exec/06-feed-stdin.go)

我们还可以调用 `cmd.StdinPipe()` ，它返回 `io.WriteCloser` 。它比较复杂，但是可以更好地控制我们的代码量。

## 7. 改变执行程序的环境

有关 Go 中环境变量的注意事项：

- `os.Environ()` 返回一个 `[]string` ，其中每个字符串的形式为 `FOO=bar` ，其中 `FOO` 是环境变量的名称， `bar` 是值
- `os.Getenv("FOO")` 返回环境变量 `FOO` 的值

有时您需要修改执行程序的环境。 Go 通过设置 `exec.Cmd` 的 `Env` 成员来支持这一点。 `cmd.Env` 与 `os.Environ()` 的格式相同。如果未设置 `Env` ，则进程将继承调用进程的环境。

如果你不想从头开始构建一个全新的环境，而是传递当前流程环境的修改版本。可以使用以下添加新变量的方法：

``` plain
cmd := exec.Command("programToExecute")

additionalEnv := "FOO=bar"
newEnv := append(os.Environ(), additionalEnv))
cmd.Env = newEnv

out, err := cmd.CombinedOutput()
if err != nil {
	log.Fatalf("cmd.Run() failed with %s\n", err)
}
fmt.Printf("%s", out)
```
完整代码： [advanced-exec/05-change-environment.go](https://github.com/kjk/go-cookbook/blob/master/advanced-exec/05-change-environment.go)

如果要删除环境或确保不设置两次相同的变量，那么就不这么简单了。幸好软件包 [`shurcooL/go/osutil`](https://godoc.org/github.com/shurcooL/go/osutil#Environ) 提供了一种操作环境变量的简便方法。

## 8. 提前检查要执行的程序是否可被执行

想象一下，你编写了一个需要很长时间才能运行的程序。最后，你调用可执行文件 `foo` 执行基本任务，此时如果不存在 `foo` 可执行文件，则调用将失败，前功尽弃。

因此，最好在开始的时侯就检测到缺少可执行文件 `foo` ，并在出现描述性错误消息时尽早结束执行。可以使用 `exec.LookPath` 做到这一点：

``` plain
func checkLsExists() {
	path, err := exec.LookPath("ls")
	if err != nil {
		fmt.Printf("didn't find 'ls' executable\n")
	} else {
		fmt.Printf("'ls' executable is in '%s'\n", path)
	}
}
```
完整代码： [advanced-exec/04-check-exe-exists.go](https://github.com/kjk/go-cookbook/blob/master/advanced-exec/04-check-exe-exists.go)

检查程序是否存在的另一种方法是尝试在无操作模式下执行它（例如，许多程序都支持 `--help` 选项）。
