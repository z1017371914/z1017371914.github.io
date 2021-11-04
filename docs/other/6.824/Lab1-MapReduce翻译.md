

## 原文链接

[6.824Lab1.MapReduce](http://nil.csail.mit.edu/6.824/2020/labs/lab-mr.html)

看完就赶快做Lab🤯

## 前言

在这个实验中你将要构造一个MapReduce系统。你要实现一个worker程序调用Map和Reduce函数的应用，并处理读写文件；而一个master程序要分发任务给workers，以及应对执行任务失败的worker。你应该构造的东西和这篇[论文](http://static.googleusercontent.com/media/research.google.com/en//archive/mapreduce-osdi04.pdf)类似，所以请先通读这篇论文。

## 合作政策

你应该完全由你本人来写6.824的实验代码，除了我们事先给你的代码。你不能看其他任何人的解决方案，也不能看很多年前的解决方法。你可以和其他人讨论做法，但不能看或者copy其他人的代码。这些规则制定的原因是我们认为通过你自己独立实现你的lab，能最大程度的让你学到东西。

请不要将你的代码公开或者给其他学生。github的仓库是默认公开的，除非你将仓库设为私有，否则请不要把代码放上去。[MIT’s Github](https://github.mit.edu/)或许对你来说会不错，但确保仓库是private的。

## 软件

实现这个lab你需要使用go。go的官网有很多tutorial。我们的lab使用的go版本是1.13，所以你最好也要用1.13的版本。你可以通过命令行键入go version来查看版本。

我们建议你在你自己的机器上运行lab，所以你可以使用一些工具，比如文本编辑器等。或者，你也可以在Athena上运行lab代码。

## 开始

用git来进行版本控制。从github取下lab的代码

```livescript
$ git clone git://g.csail.mit.edu/6.824-golabs-2020 6.824
$ cd 6.824
$ ls
```

我们给你提供了一个简单的顺序的mapreduce实现，在`src/main/mrsequential.go`.它只在一个进程里执行一次maps和reduces。我们也给你提供了敌对MapReduce应用：在`mrapps/wc.go`的单词统计，以及在`mrapps/indexer.go`的一个文本索引。你可以依次运行下列这些指令来运行单词统计

```livescript
$ cd ~/6.824
$ cd src/main
$ go build -buildmode=plugin ../mrapps/wc.go
$ rm mr-out*
$ go run mrsequential.go wc.so pg*.txt
$ more mr-out-0
```



`mrsequential.go`将它的输出文件保存在`mr-out-0`、输入是`pg-xxx.txt`

你可以从`mrsequential.go`中借鉴代码，也应该看看`mrapps/wc.go`的代码，了解MapReduce应用的代码是啥样的。

## 你的任务

你的任务是实现一个分布式的MapReduce，有两个程序组成，master程序和worker程序。master只会有一个，而worker则会有很多个在并行的执行。在真正的系统中，worker应该运行在很多个机器上，但在这个lab中你会在单机上全部运行他们。worker会通过`RPC`和master进行通信。每个worker都会向master要一个任务，从一个甚至多个文件中读取任务的输入，执行任务，将任务的输出写到一个或者多个文件。master应该注意如果有某个worker在很长时间内（在这lab中，用10s作为判定）不能完成它的任务时，应该将它的任务给其他不用的worker去执行。

我们给你提供了一些简单的代码让你去开始。main这个例子在`main/mrmaster.go`和`main/mrworker.go`，不要修改这两个文件，你应该把你实现的代码写在`mr/master.go`和`mr/worker.go`，以及`mr/rpc.go`

接下来是如何通过MapReduce应用来运行你的单词统计。首先，确保插件已经built了。

```livescript
$ go build -buildmode=plugin ../mrapps/wc.go
```

在main目录中，运行master

```livescript
$ rm mr-out*
$ go run mrmaster.go pg-*.txt
```

`mrmaster.go`的参数`pg-*.txt`是输入的文件，每一个文件对应一个“分裂”,也是一个Map任务的输入。

在一个或多个其他的窗口，运行一些workers

```livescript
$ go run mrworker.go wc.so
```

当worker和master结束时，查看输出文件，在`mr-out-*`。当你完成了lab时，这些已经排了序的输出文件应该和下面的顺序输出差不多：

```livescript
$ cat mr-out-* | sort | more
A 509
ABOUT 2
ACT 8
...
```

我们给你提供了一个测试脚本`main/test-mr.sh`,这个测试脚本会检查当给定`pg-xxx.txt`作为输入通过MapReduce应用，来执行单词统计和索引产生的输出。测试脚本也会检查你实现的Map和Reduce是否并行的，并且你的代码应该能恢复那些宕机的worker执行的任务。

如果你现在就执行测试脚本，他就会一直挂起，永不停止。

```livescript
$ cd ~/6.824/src/main
$ sh test-mr.sh
```

你可以在已经完成的`mr/master.go`中更改`ret := false`变成`true`，这样master就会马上存在，然后执行以下指令。

```livescript
$ sh ./test-mr.sh
*** Starting wc test.
sort: No such file or directory
cmp: EOF on mr-wc-all
--- wc output is not the same as mr-correct-wc.txt
--- wc test: FAIL
```

这个测试脚本预期看到名为`mr-out-X`的输出文件，每个对应一个reduce任务。而空的`mr/master.go`和`mr/worker.go`不会产生结果，所以测试是失败的。

当你完成了代码，测试脚本的输出应该是这样的。

```livescript
$ sh ./test-mr.sh
*** Starting wc test.
--- wc test: PASS
*** Starting indexer test.
--- indexer test: PASS
*** Starting map parallelism test.
--- map parallelism test: PASS
*** Starting reduce parallelism test.
--- reduce parallelism test: PASS
*** Starting crash test.
--- crash test: PASS
*** PASSED ALL TESTS
$
```

你也可以从GO RPC包中看一些errors，就像这样

```livescript
2019/12/16 13:27:09 rpc.Register: method "Done" has 1 input parameters; needs exactly three
```

---

一些规则：

· 当`nReduce`是`main/mrmaster.go`的参数，Map阶段应该除中间生成的key到一个桶中，从而为nReduce的任务。

· worker应该将第X个reduce的任务放到文件`mr-out-X`中

· 一个`mr-out-X`文件应该包含Reduce函数输出的每一行。行应该保证是Go中的格式：`%v %v`，分别对应key和value。可以看看`main/mrsequential.go`，查看对应行注释中"this is the correct format".如果你的代码偏离太多这样的格式，测试代码是会失败的。

· 你可以调整`mr/worker.go`, `mr/master.go`, and `mr/rpc.go`.你可以暂时的调整其他文件来进行测试，但确保你的代码能通过原始的版本运行，我们会通过原始的版本进行测试。

·worker应该将中间Map的输出放到当前目录中的文件，也就是你的worker不久能当作Reduce任务输出的那样去读取他们的地方

· `main/mrmaster.go`期望`mr/master.go`实现一个`Done()`方法，该方法在MapReduce任务分成的时候返回True。在那时，`mrmaster.go`应该退出

· 当任务完全完成时，worker程序应该退出。一个简单的方法去实现：使用`call()`的返回值，如果worker无法和master进行沟通，他会假设master因为任务已经结束然后离开，这是worker也可以终止了。这取决于你的设计，你也可以设计一个有用的，master分发给worker“请worker退出”的伪任务。

## 提示

· 开始的一种方法是，调整`mr/worker.go`中的`Worker()`，发送一个RPC给master来请求一个任务。然后调整master应答一个尚未开始进行map任务的文件名。然后调整worker读取这个文件，并调用应用的Map函数,就像在`mrsequential.go`一样。

· 应用的Map和Reduce函数从后缀为`.so`的文件，使用GO插件包，在运行时加载。

·如果你修改了任何`mr/`目录下的东西，你可能需要重新build所有你用的MapReduce插件。就比如`go build -buildmode=plugin ../mrapps/wc.go`

·  这个lab依赖workrer共享一个文件系统。当所有的worker运行在一个机子时是直截了当的，但如果workers运行在不同的机器上，需要一个全局的文件系统，比如GFS。

· 中间文件合理的命名格式`mr-X-Y`,X是Map任务的任务号，Y是reduce任务的任务号

· worker的map任务号需要存储到中间key-value键值对文件中，以便于当进行reduce任务时能正确的读回。一个可行的方式是使用Go的`encoding/json`包。将键值对写入到一个Json文件：

```go
enc := json.NewEncoder(file)
  for _, kv := ... {
    err := enc.Encode(&kv)
```

这么读文件

```go
dec := json.NewDecoder(file)
  for {
    var kv KeyValue
    if err := dec.Decode(&kv); err != nil {
      break
    }
    kva = append(kva, kv)
  }
```

· worker的map部分，你可以使用`worker.go`中的`ihash`函数来给一个reduce任务一个key

· 你可以从`mrsequential.go`中copy一些读Map输入文件的代码，实现在Map和Reduce之间排序中间键值对，以及排序Reduce输出文件的代码。

· 作为RPC服务提供者的master，应该是并发的。因此不要忘了对共享数据上锁。

· 通过`go build -race`和`go run -race`来使用Go的竞态检测器。`test-mr.sh`有一些关于你怎么使用竞态检测器来跑测试程序的注释。

· worker有时是需要等待一段时间的，例如reduce在最后一个map结束任务前不能开始。一个可行的方式是worker定期的询问master来分配任务，在每次请求之间使用`time.Sleep()`进行休眠；另一种可行的方式就是master中相关的RPC处理者必须有个循环来进行等待，要么选择`time.Sleep()`，要么选择`sync.Cond`. Go会在自己的线程中为每个RPC运行handler，所以事实上一个handler在等待时不会阻止master处理其他的RPC。

· master不能可靠的识别出宕机的worker，还存活但是已经因为某些原因阻塞或者无法运行(stall的含义不知道咋翻译)的worker，正在执行任务但是执行时间非常慢近似等于没有的worker。你能做的就是让master等待一些时间，然后放弃并重新分配相同的任务给其他worker。在这个lab中，master需等待10s。在这之后master应该假设worker已经消亡(当然，即使未消亡也应该这么任务)

· 为了测试损坏恢复，你可以使用`mrapps/crash.go`此应用插件。它会随机的存在于Map和Reduce函数中。

· 为了确保实现没人观察也能不将部分文件写入到已经crashed的worker中，MapReduce的论文提到了一种欺骗的方法：使用一个暂时的文件，一旦它被完成的写入时自动重命名它。你可以使用`ioutil.TempFile`来创建一个临时的文件，使用`os.Rename`来自动给他重命名。

· `test-mr.sh`在子目录`mr-tmp`中跑了所有的程序，所以如果某些东西出错了或者你想看中间或者输出文件，可以看看这。

## 提交步骤

在确认前，请再跑依次`test-mr.sh`测试脚本。

巴拉巴拉，剩下的是MIT大佬的提交方法，就不写了。

