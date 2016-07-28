半同步 / 半反应堆线程池的实现


这里我们实现了一个半同步 / 半反应堆并发模式的线程池，代码清单见https://github.com/weiweikaikai/threadpool.git
中的 threadpool.h 。 该线程池使用一个工作队列完全解除了主线程和工作线程的耦合关系：主线程往工作队列中插入任务，工作线程通过竞争来取得任务并执行它。不过，如果要将该线 程池应用到实际服务器程序中，那么我们必须保证所有客户请求都是无状态的，因为同一个连接上的不同请求可能会由不同的线程处理。
这里值得一提的是，在 C++ 程序中使用 pthread_create 函数时，该函数的第 3 个参数必须指向一个静态函数。而要在一个静态函数中使用类的动态成员（包括成员函数和成员变量），则只能通过如下两种方式实现：
通过类的静态对象来调用。
将类的对象作为参数(this 指针传递过去)传递给该静态函数，然后在静态函数中引入这个对象，并调用其动态方法。
代码清单 threadpool.h 使用的第二种方式：将线程参数设置为 this 指针，然后在 worker 函数中获取该指针并调用其动态方法 run 。
用线程实现的简单 Web 服务器


这里我们用前面的线程池来实现一个并发的 Web 服务器
http_conn 类

         首先，我们需要准备线程池的模板参数类，用以封装对逻辑任务的处理，这个类是 http_conn ，代码清单见头文件 https://github.com/weiweikaikai/threadpool.git   中的 http_conn.h ， http_conn.cpp 和 locker.h 。
main 函数

         定义好任务之后， main 函数就变得很简单了，它只需要负责 I/O 读写，如代码清单 https://github.com/weiweikaikai/threadpool.git   中的 main.cpp 。