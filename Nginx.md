# Nginx

## Nginx基本数据结构

### string

```c
typedef struct {
    size_t len;
    u_char *data;
} ngx_str_t;

ngx_strcmp();
ngx_strncmp();
ngx_strstr();
ngx_strlen();
ngx_strchr();
ngx_memcmp();
ngx_memset();
ngx_memcpy();
ngx_memmove();

ngx_memzero();
ngx_explicit_memzero();
ngx_cpymem();
ngx_movemem();
ngx_strlchr();


ngx_tolower();
ngx_toupper();
ngx_strlow();
ngx_strcasecmp();
ngx_strncasecmp();


ngx_string(text);
ngx_null_string
ngx_str_set(str, text);
ngx_str_null(str);
```

### Time

```c
typedef struct {
    time_t sec;
    ngx_uint_t msec;
    ngx_int_f gmtoff;
} ngx_time_t;


ngx_cached_err_log_time;
ngx_cached_http_log_time;
ngx_cached_http_time;
ngx_cached_http_log_iso8601;
```

### Array

```c
typedef struct {
    void &elts;
    ngx_uint_t nelts;
    size_t size;
    ngx_uint_t nalloc;
    ngx_poll_t *pool;
} ngx_array_t;


ngx_array_create(pool, n, size); // 创建数组
ngx_array_init(array, pool, n, size); // 初始化数组

// e.g.
ngx_array_t *a, b;
a = ngx_array_create(pool, 10, sizeof(ngx_str_t));
ngx_array_init(&b, pool, 10, sizeof(ngx_str_t));


ngx_array_push(a);
ngx_array_push_n(a, n);
```

### List

```c
typedef struct ngx_list_part_s ngx_list_part_t;
struct ngx_list_part_s {
    void *elts;
    ngx_uint_t nelts;
    ngx_list_part_t *next;
};


typedef struct {
    ngx_list_part_t *last;
    ngx_list_part_t part;
    size_t size;
    ngx_uint_t nalloc;
    ngx_pool_t *pool;
} ngx_list_t;


ngx_list_init(list, pool, n, size);
ngx_list_create(pool, n, size);
ngx_list_push(list);
```

### Queue

```c
typedef struct ngx_queue_s  ngx_queue_t;

struct ngx_queue_s {
    ngx_queue_t  *prev;
    ngx_queue_t  *next;
};


ngx_queue_insert_head(h, x), ngx_queue_insert_tail(h, x);
ngx_queue_remove(x);
ngx_queue_split(h, q, n);
ngx_queue_add(h, n);
ngx_queue_head(h), ngx_queue_last(h);
ngx_queue_sentinel(h);
ngx_queue_data(q, type, link);
```

### 红黑树

```c
typedef struct {
    ngx_rbtree_t       rbtree;
    ngx_rbtree_node_t  sentinel;

    /* custom per-tree data here */
} my_tree_t;

typedef struct {
    ngx_rbtree_node_t  rbnode;

    /* custom per-node data */
    foo_t              val;
} my_node_t;
```

### Hash

## 内存管理数据结构

### Heap

### Pool

通过pool池分配内存，当对象销毁时，内存自动释放。这能提供更良好的分配性能，并使内存控制更见简单。

池将内部连续的内存块分配给对象，一旦一个块满了，就会分配一个新的块，并将其添加到池内存块列表这。当请求的分配大于一个块时，请求会被转发到系统分配程序中，并且返回的指针被存储在池中以便进一步释放。

池通常绑定一个特定的对象（如Http请求），当对象生命周期结束时就会被销毁。

### Shared memory

Nginx使用共享内存在进程之间共享公共数据，每个共享区域必须具有唯一的名称，如果已经存在相同名称和标记的共享区域，则重用现有的共享区域

共享区域内部，被划分为slab pool

### Logging

### Cycle

### Buffer

Nginx对于输入输出提供了缓冲区类型`ngx_buf_t`。通常，它用于保存要写入目标或从源读取的数据。缓冲区可以引用内存或文件中的数据，缓冲区的内存是单独分配的。

## 网络数据结构

### connection

## 事件对象

### Event

### I/O events

### Timmer events

### Posted events

### Event loop

除了nginx主进程，所有nginx进程都在执行I/O事件 循环。Nginx事件循环在`ngx_process_events_and_timers()`函数中实现，该函数被重复调用，直到进程退出。

### Processes

Nginx有几种不同的进程

- NGX_PROCESS_MASTER
  主进程读取 NGINX 配置，创建循环，并启动和控制子进程。它不执行任何 I/O，只对信号作出响应。它的 Cycle 函数是 `ngx _ master _ process _ cle ()`。

- NGX_PROCESS_WORKER
  worker进程，它处理客户端连接。它由主进程启动，并对其信号和通道命令作出响应。它的循环函数是 ngx _ worker _ process _ cle ()。可以有多个 worker 进程，这是由 worker _ process 指令配置的。

- NGX_PROCESS_SINGLE
  单个进程，它只存在于 master _ process off 模式下，并且是在该模式下运行的唯一进程。它创建循环(如主进程所做的)并处理客户端连接(如worker进程所做的)。它的循环函数是 ngx _ single _ process _ cle ()。

- NGX_PROCESS_HELPER
  帮助进程，目前有两种类型: 缓存管理器和缓存加载器。两者的 Cycle 函数都是 ngx _ cache _ manager _ process _ cle ()。

### Threads

提供将任务分配到一个单独线程的方法，这个线程任务不会阻塞worker进程。例如，nginx可以使用线程执行文件I/O。线程接口是用于处理客户端连接的现有异步方法的帮助器，但不是替代品。

```c
typedef struct ngx_thread_task_s ngx_thread_task_t;

struct ngx_thread_task_s {
    ngx_thread_task_t   *next;
    ngx_uint_t           id;
    void                *ctx;
    void               (*handler)(void *data, ngx_log_t *log);
    ngx_event_t          event;
};
```

使用`pthreads`解决线程同步问题

> TODO

Nginx实现了线程池策略，而不是为每个任务创建一个新线程，可以为不同目的配置多个线程池（例如，在不同磁盘上执行I/O）。每个线程池都是在启动时创建，并且包含处理任务队列的有限数量的线程。当任务完成后，将调用预定义的完成处理程序。

```cpp
struct ngx_thread_pool_s {
    ngx_thread_mutex_t        mtx;
    ngx_thread_pool_queue_t   queue;
    ngx_int_t                 waiting;
    ngx_thread_cond_t         cond;

    ngx_log_t                *log;

    ngx_str_t                 name;
    ngx_uint_t                threads;
    ngx_int_t                 max_queue;

    u_char                   *file;
    ngx_uint_t                line;
}
```

线程池里有一个互斥量mtx，维护一个线程池队列，

```cpp
static ngx_int_t ngx_thread_pool_init(ngx_thread_pool_t *tp, ngx_log_t *log,
    ngx_pool_t *pool); // 线程池初始化
static void ngx_thread_pool_destroy(ngx_thread_pool_t *tp); // 线程池摧毁
```

线程池初始化过程干了什么：

- 初始化线程池队列

- 创建互斥量

- 关联日志对象

- 

- 线程属性初始化

- 设置线程detach状态

- 创建线程

```c
static ngx_int_t ngx_thread_task_post(ngx_thread_pool_t *tp, ngx_thread_task_t *task)
```

创建线程任务过程：

- 检测task的事件状态是否激活

- 获取线程池的互斥锁

- 如果等待线程数大于队列最大值，则释放锁返回错误

- 激活task事件状态

- 向线程池队列里插入task，等待值++

- 释放锁

- 打印日志

```c
static void * ngx_thread_pool_cycle(void *data)
```

线程池处理线程过程：

- 获取线程池锁

- 等待值--，从队列出队一个task

- 释放锁

- 处理task

- 处理完成的task入队ngx_thread_pool_done
