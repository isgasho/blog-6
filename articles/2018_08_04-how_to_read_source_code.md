# 如何阅读源代码

今天我们来简单的谈谈应该如何去阅读源代码。阅读源代码是每个程序员都要做的事情，毕竟成为master的先前一步，
就是 `follow the master`。

## 学会如何使用

要想知道一个东西怎么实现的，我们首先要有一个大概的认知，例如，是什么，大概是怎么用的。所以我们需要先去熟悉这个项目，通常
我们可以这样做：

- 读文档

阅读已有的，优秀的文档，可以加快了解整个项目的使用，坑和架构，这样可以快速建立起自己对该项目的认知。

- 写demo

如果阅读的是一个库的代码呢？我们可以引用这个库，写一些简单的demo，这也是为了加快我们建立对项目的认知。

- 熟悉如何使用

接下来要做的事情就是多写demo，多使用，当使用该项目到达一定程度的时候，就可以开始阅读源代码，而阅读源代码本身又可以促进
对项目的熟悉程度。但是，在什么时候应当开始阅读源代码，因人而异，并没有一个通用的度，而且会因为自身经验的积累而变化。
我个人建议当可以写出demo并且知道demo的每一行都在做什么的时候，就可以开始一边阅读源代码了，如果发现看不懂，则可以继续加深
对项目使用的熟悉程度，然后再次尝试。

> 通常注意，项目越大，开始阅读源代码所面临的难度其实会越高，一定要有信心。

接下来我们主要聊聊一些常用的阅读源代码的技巧。

## 从main函数入手

你的程序是怎么运行的？一定是从某个入口点(entrypoint)开始执行。例如 Python 可能是 `if __name__ == "__main__"` 或者 `python`
后面接的那个脚本名，例如 Go 通常是 `main` 函数，C亦然。如果是阅读一个已有的项目而非库，那么我们可以从入口函数开始跟踪代码，
看具体都做了一些什么，如果是一个库，那么我们可以从我们的demo的入口处开始跟踪源代码。

但是注意，从上而下和从下而上这两种阅读源代码的方法论，并没有哪一种更好，从上而下是从概貌到细节，从下而上是从实现细节到
概况。通常来说，都是结合两者，但是我个人通常以自上而下为主，了解了概貌之后，才会去了解具体的实现。

## 打断点

为了跟踪代码具体执行步骤，我们可以利用调试器，单步跟踪，这样我们可以看到每一步的执行。我个人并不是特别喜欢这种方式，因为
单步跟踪通常来说都会比较的细节化。例如阅读Go的runtime时，就可以用这种手法，在我们自己的main函数处打一个断点，然后单步跟踪
这样就可以看到runtime是如何进行初始化的。

## 打印调用栈

打印调用栈这是我最喜欢用的方式，因为我主要使用Go和Python，而这两门语言对打印调用栈这个操作都比较友好。通过调用栈，我们就可以
一眼看出函数是怎么从main函数到最后调用到栈顶的。例如阅读Flask的代码时，我们就可以在某个handler出直接把调用栈打印出来，可以
借助日志库，也可以借助调试器打印，然后我们就可以很轻易的看出程序都是如何从 `app.run` 一直到具体的handler的。

## 打印日志

有时候我们还可以借助日志来观察程序，根据日志可以大概看出程序都做了什么，根据日志的粒度可以从概貌和细节上都有不同程度的
了解。通常日志都会分成 `DEBUG`, `INFO`, `WARN`, `ERROR`, `FATAL` 五个级别。通常对于分布式应用，这是一种比较好用的调试和
了解执行顺序的方式。

## 合理使用跳转

跳转？编辑器用户可享受不到这种福利。那可不行，Vim作为强大的编辑器，可以通过YouCompleteMe或者是微软贡献的Languange Server
Protocol来进行定义跳转。

- Vim

    - LSP: http://langserver.org/
    - YCM: https://github.com/Valloric/YouCompleteMe

## 阅读较为经典的代码(比较老旧的提交)

一个软件随着不断地演化，新功能的增加，bug的修复，通常会变的比较晦涩难懂，所以我们可以尝试从比较早的提交里开始阅读，比如
`tag 0.1`, `tag 1.0`, 最开始的10个提交之类的。比较早的提交通常是最简单的版本，但是同一个项目，万变不离其宗，这也是有利于
我们理解项目的。
