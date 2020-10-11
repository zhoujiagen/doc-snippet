# Concurrency


## Java 7 `java.util.concurrent`

### 接口

|<p style="width: 260px;">接口</p>|说明|
|:---|:---|
| `BlockingDeque<E>`	| A Deque that additionally supports blocking operations that wait for the deque to become non-empty when retrieving an element, and wait for space to become available in the deque when storing an element. <br>阻塞的双端队列.|
| `BlockingQueue<E>`	| A Queue that additionally supports operations that wait for the queue to become non-empty when retrieving an element, and wait for space to become available in the queue when storing an element. <br>阻塞的队列.|
| `Callable<V>`	| A task that returns a result and may throw an exception. <br>返回结果或抛出异常的任务. |
| `CompletionService<V>`	| A service that decouples the production of new asynchronous tasks from the consumption of the results of completed tasks. <br> 将生成新的异步任务与消费已完成任务的结果解耦的服务. |
| `ConcurrentMap<K,V>`	| A Map providing additional atomic putIfAbsent, remove, and replace methods. 提供了原子的`putIfAbsent`、`remove`和`replace`方法的映射. |
| `ConcurrentNavigableMap<K,V>`	| A ConcurrentMap supporting NavigableMap operations, and recursively so for its navigable sub-maps. <br>支持`NavigableMap`操作的`ConcurrentMap`: <br> `public interface ConcurrentNavigableMap<K,V> extends ConcurrentMap<K,V>, NavigableMap<K,V>`<br> `public interface NavigableMap<K,V> extends SortedMap<K,V>`<br> |
| `Delayed`	| A mix-in style interface for marking objects that should be acted upon after a given delay. <br>一个混入风格的接口, 用于标记在指定延迟后应该被操作的对象.|
| `Executor`	| An object that executes submitted Runnable tasks. <br>执行已提交的`Runnable`任务的对象.|
| `ExecutorService`	| An Executor that provides methods to manage termination and methods that can produce a Future for tracking progress of one or more asynchronous tasks. <br> 一个`Executor`, 提供了管理终止和为跟踪一个或多个异步任务的进展而生成`Future`的方法.|
| `ForkJoinPool.`<br>`ForkJoinWorkerThreadFactory`	| Factory for creating new ForkJoinWorkerThreads. <br>创建`ForkJoinWorkerThread`的工厂.|
| `ForkJoinPool.`<br>`ManagedBlocker`	| Interface for extending managed parallelism for tasks running in ForkJoinPools. <br>用于扩展在`ForkJoinPool`中运行的任务的受管的并行度的接口.|
| `Future<V>`	| A Future represents the result of an asynchronous computation. <br>表示异步计算的结果.|
| `RejectedExecutionHandler`	| A handler for tasks that cannot be executed by a ThreadPoolExecutor. <br>任务不能被`ThreadPoolExecutor`的处理器.|
| `RunnableFuture<V>`	| A Future that is Runnable. <br>是`Runnable`的`Future`.|
| `RunnableScheduledFuture<V>`	| A ScheduledFuture that is Runnable. <br>是`Runnable`的`ScheduledFuture`.|
| `ScheduledExecutorService`	| An ExecutorService that can schedule commands to run after a given delay, or to execute periodically. <br>可以调度命令在给定延迟后或周期性执行的`ExecutorService`.|
| `ScheduledFuture<V>`	| A delayed result-bearing action that can be cancelled. <br>可以被取消的, 被延迟的携带结果的动作.|
| `ThreadFactory`	| An object that creates new threads on demand. <br>按需创建新线程的对象.|
| `TransferQueue<E>`	| A BlockingQueue in which producers may wait for consumers to receive elements. <br>一个`BlockingQueue`, 生产者可以等待消费者接收元素.|


### 类

|<p style="width: 260px;">类</p>|说明|
|:---|:---|
| `AbstractExecutorService`	| Provides default implementations of ExecutorService execution methods. <br>. 提供`ExecutorService`执行方法的默认实现.|
| `ArrayBlockingQueue<E>`	| A bounded blocking queue backed by an array. <br>一个数组后端的有界阻塞队列.|
| `ConcurrentHashMap<K,V>`	| A hash table supporting full concurrency of retrievals and adjustable expected concurrency for updates. <br>支持完整的并发更新时检索和调整哈希表.|
| `ConcurrentLinkedDeque<E>`	| An unbounded concurrent deque based on linked nodes. <br>基于链接节点的无界并发双端队列.|
| `ConcurrentLinkedQueue<E>`	| An unbounded thread-safe queue based on linked nodes. <br>基于链接节点的无界线程安全的队列.|
| `ConcurrentSkipListMap<K,V>`	| A scalable concurrent ConcurrentNavigableMap implementation. <br>一个可扩展的并发`ConcurrentNavigableMap`的实现.|
| `ConcurrentSkipListSet<E>`	| A scalable concurrent NavigableSet implementation based on a ConcurrentSkipListMap. <br>一个基于`ConcurrentSkipListMap`的可扩展的并发`NavigableSet`的实现.|
| `CopyOnWriteArrayList<E>`	| A thread-safe variant of ArrayList in which all mutative operations (add, set, and so on) are implemented by making a fresh copy of the underlying array. <br>一个线程安全的`ArrayList`变体, 所有修改操作(`add`、`set`等)是通过执行底层数组的新拷贝实现的.|
| `CopyOnWriteArraySet<E>`	| A Set that uses an internal CopyOnWriteArrayList for all of its operations. <br>一个`Set`, 内部使用`CopyOnWriteArrayList`实现所有操作.|
| `CountDownLatch`	| A synchronization aid that allows one or more threads to wait until a set of operations being performed in other threads completes. <br>一个同步工具, 允许一个或多个线程等待直到一组在其它线程中执行的操作完成.|
| `CyclicBarrier`	| A synchronization aid that allows a set of threads to all wait for each other to reach a common barrier point. <br>一个同步工具, 允许一组线程均互相等待以到达一个通用的栅栏点.|
| `DelayQueue<E extends Delayed>`	| An unbounded blocking queue of Delayed elements, in which an element can only be taken when its delay has expired. <br>一个元素为`Delayed`的无界阻塞队列, 只有当元素的延迟已过时, 该元素才可被取出.|
| `Exchanger<V>`	| A synchronization point at which threads can pair and swap elements within pairs. <br>一个同步点, 线程可以组对, 且在对中交换元素.|
| `ExecutorCompletionService<V>`	| A CompletionService that uses a supplied Executor to execute tasks. <br>一个`CompletionService`, 使用提供的`Executor`执行任务.|
| `Executors`	| Factory and utility methods for Executor, ExecutorService, ScheduledExecutorService, ThreadFactory, and Callable classes defined in this package. <br>这个包中`Executor`、`ExecutorService`、`ScheduledExecutorService`、`ThreadFactory`、`Callable`类的工厂和工具方法.|
| `ForkJoinPool`	| An ExecutorService for running ForkJoinTasks. <br>运行`ForkJoinTask`的`ExecutorService`.|
| `ForkJoinTask<V>`	| Abstract base class for tasks that run within a ForkJoinPool. <br>在`ForkJoinPool`中运行的任务的抽象基类.|
| `ForkJoinWorkerThread`	| A thread managed by a ForkJoinPool, which executes ForkJoinTasks. <br>由`ForkJoinPool`管理的线程, 在其中执行`ForkJoinTask`.|
| `FutureTask<V>`	| A cancellable asynchronous computation. <br>一个可被取消的异步计算.|
| `LinkedBlockingDeque<E>`	| An optionally-bounded blocking deque based on linked nodes. <br>一个基于链接节点的可选有界的阻塞双端队列.|
| `LinkedBlockingQueue<E>`	| An optionally-bounded blocking queue based on linked nodes. <br>一个基于链接节点的可选有界的阻塞队列.|
| `LinkedTransferQueue<E>`	| An unbounded TransferQueue based on linked nodes. <br>一个基于链接节点的无界`TransferQueue`.|
| `Phaser`	| A reusable synchronization barrier, similar in functionality to CyclicBarrier and CountDownLatch but supporting more flexible usage. <br>一个可重用的同步栅栏, 与`CyclicBarrier`和`CountDownLatch`功能类似, 但更灵活.|
| `PriorityBlockingQueue<E>`	| An unbounded blocking queue that uses the same ordering rules as class PriorityQueue and supplies blocking retrieval operations. <br>一个无界阻塞队列, 使用与`PriorityQueue`相同的排序规则, 支持阻塞的检索操作.|
| `RecursiveAction`	| A recursive resultless ForkJoinTask. <br>一个递归的无结果的`ForkJoinTask`.|
| `RecursiveTask<V>`	| A recursive result-bearing ForkJoinTask. <br>一个递归的携带结果的`ForkJoinTask`.|
| `ScheduledThreadPoolExecutor`	| A ThreadPoolExecutor that can additionally schedule commands to run after a given delay, or to execute periodically. <br>一个`ThreadPoolExecutor`, 额外提供了在执行延迟后调度命令或周期性执行命令.|
| `Semaphore`	| A counting semaphore. <br>一个计数信号量.|
| `SynchronousQueue<E>`	| A blocking queue in which each insert operation must wait for a corresponding remove operation by another thread, and vice versa. <br>一个阻塞队列, 每个插入操作必须等待对应的另一个线程中的删除操作, 反之亦然.|
| `ThreadLocalRandom`	| A random number generator isolated to the current thread. <br>当前线程中隔离的随机数生成器.|
| `ThreadPoolExecutor`	| An ExecutorService that executes each submitted task using one of possibly several pooled threads, normally configured using Executors factory methods. <br>一个`ExecutorService`, 使用池化线程(通常使用`Executors`工厂方法配置)中的一个执行已提交的任务.|
| `ThreadPoolExecutor.`<br>`AbortPolicy`	| A handler for rejected tasks that throws a RejectedExecutionException. <br>抛出`RejectedExecutionException`的被拒绝任务的处理器.|
| `ThreadPoolExecutor.`<br>`CallerRunsPolicy`	| A handler for rejected tasks that runs the rejected task directly in the calling thread of the execute method, unless the executor has been shut down, in which case the task is discarded. <br>被拒绝任务的处理器, 直接在执行方法的调用线程中运行被拒绝的任务, 除非执行者已关闭, 这时该任务被丢弃.|
| `ThreadPoolExecutor.`<br>`DiscardOldestPolicy`	| A handler for rejected tasks that discards the oldest unhandled request and then retries execute, unless the executor is shut down, in which case the task is discarded. <br>被拒绝任务的处理器, 丢弃最旧的未处理请求, 然后重试执行, 除非执行者已关闭, 这时该任务被丢弃.|
| `ThreadPoolExecutor.`<br>`DiscardPolicy`	| A handler for rejected tasks that silently discards the rejected task. <br>被拒绝任务的处理器, 直接丢弃被拒绝的任务.|

### 枚举

|<p style="width: 260px;">枚举</p>|说明|
|:---|:---|
| `TimeUnit`	| A TimeUnit represents time durations at a given unit of granularity and provides utility methods to convert across units, and to perform timing and delay operations in these units. <br>不同粒度的时间长度的枚举工具类.|

### 异常

|<p style="width: 260px;">异常</p>|说明|
|:---|:---|
| `BrokenBarrierException`	| Exception thrown when a thread tries to wait upon a barrier that is in a broken state, or which enters the broken state while the thread is waiting. <br>当线程尝试在已处于破坏状态的栅栏上等待, 或者线程等待时变为了破坏状态时, 抛出.|
| `CancellationException`	| Exception indicating that the result of a value-producing task, such as a FutureTask, cannot be retrieved because the task was cancelled. <br>指示产生值的任务的结果, 例如`FutureTask`, 因为任务被取消而无法获取.|
| `ExecutionException`	| Exception thrown when attempting to retrieve the result of a task that aborted by throwing an exception. <br>当尝试获取因抛出异常而终止的任务的结果时, 抛出.|
| `RejectedExecutionException`	| Exception thrown by an Executor when a task cannot be accepted for execution. <br>当`Executor`无法接收任务执行时, 抛出.|
| `TimeoutException`	| Exception thrown when a blocking operation times out. <br>当阻塞操作超时时, 抛出.|


### java.util.concurrent.atomic

|<p style="width: 260px;">类</p>|说明|
|:---|:---|
| `AtomicBoolean`	| A boolean value that may be updated atomically. <br>可被原子更新的布尔值.|
| `AtomicInteger`	| An int value that may be updated atomically. <br>可被原子更新的整数值.|
| `AtomicIntegerArray`	| An int array in which elements may be updated atomically. <br>元素可被原子更新的整数数组.|
| `AtomicIntegerFieldUpdater<T>`	| A reflection-based utility that enables atomic updates to designated volatile int fields of designated classes. <br>一个基于反射的工具, 允许原子的更新指定类中指定的`volatile`整数字段.|
| `AtomicLong`	| A long value that may be updated atomically. <br>可被原子更新的长整数值.|
| `AtomicLongArray`	| A long array in which elements may be updated atomically. <br>元素可被原子更新的长整数数组.|
| `AtomicLongFieldUpdater<T>`	| A reflection-based utility that enables atomic updates to designated volatile long fields of designated classes. <br>一个基于反射的工具, 允许原子的更新指定类中指定的`volatile`长整数字段.|
| `AtomicMarkableReference<V>`	| An AtomicMarkableReference maintains an object reference along with a mark bit, that can be updated atomically. <br>用一个标记位维护一个对象引用, 这个引用可被原子的更新.|
| `AtomicReference<V>`	| An object reference that may be updated atomically. <br>可被原子更新的对象引用.|
| `AtomicReferenceArray<E>`	| An array of object references in which elements may be updated atomically. <br>元素可被原子更新的对象引用数组.|
| `AtomicReferenceFieldUpdater<T,V>`	| A reflection-based utility that enables atomic updates to designated volatile reference fields of designated classes. <br>一个基于反射的工具, 允许原子的更新指定类中指定的`volatile`引用字段.|
| `AtomicStampedReference<V>`	| An AtomicStampedReference maintains an object reference along with an integer "stamp", that can be updated atomically. <br>用一个整数标志维护一个对象引用, 这个引用可被原子的更新.|


### java.util.concurrent.locks


#### 接口

|<p style="width: 260px;">接口</p>|说明|
|:---|:---|
| `Condition`	| Condition factors out the Object monitor methods (wait, notify and notifyAll) into distinct objects to give the effect of having multiple wait-sets per object, by combining them with the use of arbitrary Lock implementations. <br>将`Object`的监视器方法(`wait`、`notify`、`notifyAll`)泛化到不同对象上, 通过与任意`Lock`实现的使用结合在一起, 提供了每个对象上有多个等待集的效果.|
| `Lock`	| Lock implementations provide more extensive locking operations than can be obtained using synchronized methods and statements. <br>提供了使用同步方法和语句外更为基础的锁操作.|
| `ReadWriteLock`	| A ReadWriteLock maintains a pair of associated locks, one for read-only operations and one for writing. <br>维护了一个相关的锁对, 一个用于只读操作, 一个用于写操作.|

#### 类

|<p style="width: 260px;">类</p>|说明|
|:---|:---|
| `AbstractOwnableSynchronizer`	| A synchronizer that may be exclusively owned by a thread. <br>一个可被一个线程互斥拥有的同步器.|
| `AbstractQueuedLongSynchronizer`	| A version of AbstractQueuedSynchronizer in which synchronization state is maintained as a long. <br>一个`AbstractQueuedSynchronizer`, 其中同步状态用`long`维护.|
| `AbstractQueuedSynchronizer`	| Provides a framework for implementing blocking locks and related synchronizers (semaphores, events, etc) that rely on first-in-first-out (FIFO) wait queues. <br>提供了一个框架, 以实现依赖于FIFO等待队列的、阻塞的锁和相关的同步器(信号量、事件等).|
| `LockSupport`	| Basic thread blocking primitives for creating locks and other synchronization classes. <br>创建锁和其它同步类的基本线程阻塞原语.|
| `ReentrantLock`	| A reentrant mutual exclusion Lock with the same basic behavior and semantics as the implicit monitor lock accessed using synchronized methods and statements, but with extended capabilities. <br>一个可重入的互斥`Lock`, 有与使用同步方法和语句访问的隐式监视器锁相同的行为和语义, 但有扩展的能力.|
| `ReentrantReadWriteLock`	| An implementation of ReadWriteLock supporting similar semantics to ReentrantLock. <br>支持与`ReentrantLock`类似的语义的`ReadWriteLock`的实现.|
| `ReentrantReadWriteLock.ReadLock`	| The lock returned by method ReentrantReadWriteLock.readLock(). <br>`ReentrantReadWriteLock.readLock()`方法返回的锁.|
| `ReentrantReadWriteLock.WriteLock`	| The lock returned by method ReentrantReadWriteLock.writeLock(). <br>`ReentrantReadWriteLock.writeLock()`方法返回的锁.|
