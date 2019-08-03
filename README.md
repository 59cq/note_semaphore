# note_semaphore
学习笔记：操作系统-信号量
### 信号量

内核维护的计数量，用于管理多进程之间共享资源

例如,由个变量n表示资源的数量,当有进程想要独占一个资源时,n的值要-1(或多个),如果n的值等于0(不够减),则进程阻塞,直到n的值可以减再被唤醒,当资源使用完毕后n的值要+1(或多个)

- `int semget(key_t key, int nsems, int semflg);`
  - 创建/获取信号量
  - key IPC键值
  - nsems 信号量的数
  - semflg 
    - 0 获取信号量
    - IPC_CREAT 创建信号量(不存在则创建,存在则获取)
    - IPC_EXCL 如果已存在则创建失败
  - 返回值 信号量标识

- `int semop(int semid, struct sembuf *sops, size_t nsops);`
  - 操作信号量(对信号进行加/减操作)
  - semid 信号量标识符
  - sops 结构体指针
  - nsopd 数组长度
```C
struct sembuf{
    unsigned short sem_num;  /* semaphore number 信号量下标 */
    short          sem_op;   /* semaphore operation 操作*/
    short          sem_flg;  /* operation flags 标记*/
    IPC_NOWAIT //当信号量不够减时,不阻塞
    SEM_UNDO //当进程结束时,信号量的值自动归还
}
```


- `int semtimedop(int semid, struct sembuf *sops, size_t nsops, const struct timespec *timeout);`
  - 带时间限制的信号量操作


```C
struct timespec{
    __time_t tv_sec; 
    long int tv_nsec; 
}
```
- `int semctl(int semid, int semnum, int cmd, ...);`
  - 初始化信号量的值;删除信号量;获取,设置信号量的属性
  - cmd
    - `IPC_STAT` 获取信号量的属性
    - `IPC_SET` 设置信号量的属性
    
    - `IPC_RMID` 删除信号量的属性
    - `IPC_INFO` 获取信号量的信息
    - `SEM_INFO` 设置信号量的信息

    - `GETALL` 获取所有信号量的值
    - `GETNCNT` 获取信号量的数量
    - `GETVAL` 获取某个信号量的值
    - `GETZCNT` 返回正在等待的完全空闲的资源数目
    - `SETALL` 设置所有信号量的值
    - `SETVAL` 设置某个信号量的值
```C
union semun {
    int              val;    /* Value for SETVAL */
    struct semid_ds *buf;    /* Buffer for IPC_STAT, IPC_SET */
    unsigned short  *array;  /* Array for GETALL, SETALL */
    struct seminfo  *__buf;  /* Buffer for IPC_INFO
                                           (Linux-specific) */
};
struct  seminfo {
    int semmap;  /* Number of entries in semaphore map; unused within kernel */
    int semmni;  /* Maximum number of semaphore sets */
    int semmns;  /* Maximum number of semaphores in all semaphore sets */
    int semmnu;  /* System-wide maximum number of undo structures; unused within kernel */
    int semmsl;  /* Maximum number of semaphores in a set */
    int semopm;  /* Maximum number of operations for semop(2) */
    int semume;  /* Maximum number of undo entries per process; unused within kernel */
    int semusz;  /* Size of struct sem_undo */
    int semvmx;  /* Maximum semaphore value */
    int semaem;  /* Max. value that can be recorded for semaphore adjustment (SEM_UNDO) */
};
```

编程模型:

|进程A||进程B|
|:-:|:-:|:-:|
|创建信号量|semget|获取信号量|
|初始化信号量|semctl|...|
|加减信号量|semop|加减信号量|
|删除信号量|semctl|...|

注意:信号量是用来计数的,一定要有资源对应

[一个简单的信号量的用例](https://github.com/BroWuGou/note_semaphore/blob/master/semaphore.c)
