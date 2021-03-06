在开始正式的爬虫工作之前，务必需要对进程与线程有一定的了解。在后期，对爬虫的效率有一定的要求之后，需要构造分布式爬虫。

# 多进程

1. 使用os模块中的fork构造进程；
fork()方法只适用于Linux/Unix系统，是一个非常常见的系统调用的方法。<br>
forn()方法调用一次，返回两次。该方法调用时，操作系统将当前进程即父进程进行了复制，即子进程，这两个进程完全相同。在父进程中，返回的是子进程的ID；在子进程中，返回的是永远是0。
测试程序fork_process.py

2. multiprocessing模块创建多进程
fork()方法虽然方便，但是最大的缺陷在于其只能运行在Linux/Unix系统上，在Windows系统上可以使用multiprocessing模块提供的Process类。
测试程序multiprocessing_process.py
* 通过Process类创建进程
Process类创建实例化对象，通过调用构造方法创建新进程。
start()方法启动新创建的进程；
join()方法的作用是在多进程执行时，其他进程必须等到调用join()方法的进程结束之后（或者超出规定的时间）才能继续执行；
* 继承Process类创建进程
run()方法在使用继承类创建新进程的时候需要用到，包含新进程需要执行的代码；

3. 进程池
在multiprocessing模块中，Pool类可以提供指定数量的进程供用户调用，默认大小是CPU的核数。在初始化Pool时，可以指定一个最大进程数，当有新的请求提交到Pool中，如果进程池还没满的话，那么就创建一个新的进程来执行该请求；如果池中的进程数已达到最大进程数，那么该请求就只能等待，直到进程池中有进程结束，然后创建新的进程来处理该请求。
测试程序multiprocessing_pool.py

4. 进程间通信
对于多进程而言，进程间的通信是十分关键的操作。
* Queue通信方式
Queue通信方式用于在多个进程之间实现通信。
测试程序multiprocessing_queue.py
* Pipe通信方式
Pipe常用来在两个进程之间进行通信，其中两个进程分别位于管道的两端。
测试程序multiprocessing_pipe.py

# 多线程
线程比进程单位更小，线程是一个基本的CPU执行单元。线程必须在某个进程中执行，一个进程可包含多个线程，但是只能有一个主线程。在多线程中，共享同个地址空间、打开的文件等资源；在多进程中，共享物理内存、、磁盘、打印机等资源。其中，线程按照作用不同可分为主线程、子线程、守护线程（后台线程）以及前台线程等。
在Python标准库中，提供的模块有thread和threading。其中，thread是低级模块，threading是高级模块，对thread进行了封装。

1. 使用threading模块创建多线程
同创建进程一样，threading模块中也提供两种方法创建多线程。分别是传入函数创建Thread实例和直接从threading.Thread中继承并创建线程类。
测试程序threading_demo.py

2. 线程同步
在多线程中，线程同步是很为重要的。这是为了防止多个线程同时对同一个数据进行了修改。在Thread对象中，Lock和RLock可以实现简单的线程同步，通过将数据操作放入acquire和release操作中。Lock和RLock的主要区别在于RLock可以多次执行acquire操作，但是有多少个acquire操作之后就需要有多少个release操作，如果对Loak执行两次acquire操作则会发生死锁。
测试程序threading_synchronization.py

需要注意的是，在使用CPython解释器的时候，会存在全局解释器锁GIL的概念。该锁主要是为了在不同线程同时访问一数据时，对数据提供保护机制。这隐含着说明任何python程序，在任何时候都只有一个线程在执行，而不管处理器的数量。正是因为这把锁的存在，所以对于IO密集型的任务，使用多线程更快，而对于CPU密集型的任务，可以通过使用多进程代替多线程，避开GIL。而在CPython解释器中之所以不移除GIL，主要是移除之后解释器的执行效率会更低，同时使用GIL的话会使得初学者的门槛更低。

# 协程
协程（coroutine），又称微线程，纤程，一种用户级的轻量级线程。对于协程来说，拥有自己的寄存器上下文和栈，在协程调用切换时，将寄存器上下文和栈保存到其他地方，在之后切换回来的时候，恢复之前保存的寄存器上下文和栈。也就是说协程能够保留上一次调用的状态。与线程的不同在于线程是系统级别的，由操作系统调度；协程是程序级别的，由程序员在程序中根据需要自行调度。
* 使用yield实现协程
测试程序coroutine_yield.py
* 使用greenlet实现协程
测试程序coroutine_greenlet.py
* 使用gevent实现协程
测试程序coroutine_gevent.py和coroutine_gevent_pool.py

# 分布式进程
分布式进程是将Process进程分布到多台机器上，充分利用多台机器的性能完成复杂的任务。
测试程序distributed_manager.py和distributed_worker.py
