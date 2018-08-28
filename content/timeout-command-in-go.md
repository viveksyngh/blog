---
title: "Timeout Command in Go"
date: 2018-08-28T16:47:16+05:30
categories: ["golang", "exec", "timeout", "process"]
author: "Vivek Singh"
---

Sometimes in our applications, we need to invoke external processes or commands (e.g CURL, Ping, SSH etc.) to perform some tasks. We can use [`os/exec`](https://golang.org/pkg/os/exec/) Go package to invoke external processes. Most of the time we want to invoke these commands with timeouts. 

In this blog post, I am going to talk about the different ways in which we can invoke a command with a timeout.

## Timeout with timer
In this method, we will use a timer for the timeout. We start a timer with a given timeout period and pass a function to kill the command if the timer expires. Then after, we invoke the command and check if the timer was expired or it completed within given timeout period.

<script src="https://gist.github.com/viveksyngh/b34c295984a9888d2badcc42f3d7d65c.js"></script>

## Timeout with goroutine and channel
In the second method, we will use a `goroutine` and a `channel`. We start the command with [`cmd.Start()`](https://golang.org/pkg/os/exec/#Cmd.Start) and launch a goroutine which calls [`cmd.Wait()`](https://golang.org/pkg/os/exec/#Cmd.Wait) method and sends the output to a channel. In the main method, we have a `select` statement which has two cases one with timer for timeout and other to receive from the channel.

<script src="https://gist.github.com/viveksyngh/df64846d520d86b64453809e2176dbd5.js"></script>


## Timeout with context
The third method is to use background context with the timeout. Prior to Go 1.7 [`context`](https://golang.org/pkg/context/) package was not part of the standard library. In Go 1.7 it was included into the standard library. In this approach, we create a context with timeout and execute the command in that context, if it time taken by the external command exceeded the timeout it gives `context.DeadlineExceeded` error.

<script src="https://gist.github.com/viveksyngh/98287c04adc0d0536b56273bccad97cf.js"></script>

Here is the code with example to invoke the external command.

<script src="https://gist.github.com/viveksyngh/87221bf44b2b37b0d667c644655fb10d.js"></script>

Thanks for reading. If found any error or want to suggest something, please leave a comment below.