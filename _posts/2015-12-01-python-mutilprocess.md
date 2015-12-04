---
layout: post
title: Python多进程操作
category: program
tags: [python]
---

python中的多线程其实并不是真正的多线程，如果想充分利用多核CPU的资源，在python中大部分情况需要适用多进程。Python提供了非常好用的多进程包multiprocessing，只需要定义一个函数，python会完成其他所有事情。借助这个包，可以轻松完成从单进程到并发执行的转换。multiprocessing支持子进程、通信和共享数据、执行不同的形式的同步，提供了Process、Queue、Pipe、Lock等组件。

#单进程
```
from multiprocessing import Process
import os

# 子进程要执行的代码
def run_proc(name):
    print 'Run child process %s (%s)...' % (name, os.getpid())

if __name__=='__main__':
    print 'Parent process %s.' % os.getpid()
    p = Process(target=run_proc, args=('test',))
    print 'Process will start.'
    p.start()
    p.join()
    print 'Process end.'
```

#进程池
```

from multiprocessing import Pool
import os,time,random

def long_time_task(name):
	print 'Run task %s (%s)...' %(name,os.getpid())
	start = time.time()
	time.sleep(random.random()*3)
	end = time.time()
	print 'Task %s runs %0.2f seconds.' %(name,(end-start))

if __name__ == '__main__':
	print 'Parent process %s.' % os.getpid()
	p = Pool()
	for i in range(5):
		p.apply_async(long_time_task,args=(i,))
	print 'Waiting for all subprocesses done...'
	p.close()
	p.join()
	print 'All subprocesses done.'

```

注意：`p = Pool()`默认可同时执行的进程数量是按照机器的cpu来分配的，同时我们可以指定`p=Pool(4)`

#封装成类

```
import multiprocessing
import time

class ClockProcess(multiprocessing.Process):
    def __init__(self, interval):
        multiprocessing.Process.__init__(self)
        self.interval = interval

    def run(self):
        n = 5
        while n > 0:
            print("the time is {0}".format(time.ctime()))
            time.sleep(self.interval)
            n -= 1

if __name__ == '__main__':
    p = ClockProcess(3)
    p.start()

```

#关于daemon使用

##不使用daemon

````
import multiprocessing
import time

def worker(interval):
    print("work start:{0}".format(time.ctime()));
    time.sleep(interval)
    print("work end:{0}".format(time.ctime()));

if __name__ == '__main__':
    p = multiprocessing.Process(target = worker, args = (3,))
    p.start()
    print "end!"

```

输出结果是：

```
end!
work start:Thu Dec  3 18:47:39 2015
work end:Thu Dec  3 18:47:42 2015

```

##使用daemon

```
import multiprocessing
import time

def worker(interval):
    print("work start:{0}".format(time.ctime()));
    time.sleep(interval)
    print("work end:{0}".format(time.ctime()));

if __name__ == '__main__':
    p = multiprocessing.Process(target = worker, args = (3,))
    p.daemon = True
    p.start()
    print "end!"

```

输出结果是：

```
end!

```