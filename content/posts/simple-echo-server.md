+++

title = "A simple echo service"
date = "2016-10-29T02:31:59-07:00"
Description = "A simple container with an golang based echo server that prints headers"
Tags = ["container", "echo"]

+++

For some testing, I needed a simple HTTP server which would print headers that my client is
sending (and some headers added by my proxy). I had written a simple HTTP server in python which did exactly what
I wanted, and added it to python container from dockerhub.

<!--more-->

It was ridiculously large at ~260MB. Even the slim variants of python containers were ~75MB.

So, thought to write the same in Go (for fun, and result of single static binary), and just add it to busybox container.
Result was a tiny ~5MB container as busybox base itself is tiny (<1MB) set of tools, and nothing else. One can even use
[scratch](https://docs.docker.com/engine/userguide/eng-image/baseimages/#/creating-a-simple-base-image-using-scratch)
instead of busybox as basecontainer when the binary is not dependent on anything like this go binary.

Here is the [code](https://gist.github.com/karteek/c766536200f368bd884d776d8431c6c6) I wrote for this which is self
explanatory.

{{% gist c766536200f368bd884d776d8431c6c6 %}}

Lesson I learned from this experience is that its good to use busybox as base for adding `go` based utils.
[Others](https://github.com/kubernetes/kops/search?utf8=%E2%9C%93&q=BASEIMAGE) seem to be doing same for the simpler
utilities.
