#include <stdio.h>
#include <unistd.h>
#include <sys/types.h>
#include <sys/sem.h>
#include <stdlib.h>
#include <sys/wait.h>

int main(){

    //创建信号量
    int semid = semget(ftok(".",100),1,IPC_CREAT|0644);
    if(0 > semid){
        perror("semget");
        return -1;
    }
    //初始化信号量的值
    if(semctl(semid,0,SETVAL,5)){
        perror("semctl");
        return -1;
    }
    printf("我有5头小毛驴");
    //创建进程,子进程使用信号量
    for(int i=0;i<10;i++){
        if(0 == fork()){
            //使用信号量
            struct sembuf buf = {0,-1,0};
            semop(semid,&nuf,1);
            printf("%u:我骑了一头小毛驴,还剩%d头小毛驴\n",getpid(),semctl(semid,0,GETVAL));
            sleep(rand()%20+5);//使用
            //归还信号量
            buf.sem_op = 1;
            semop(semid,&buf,1)
            printf("%u:我还了一头小毛驴\n",getpid());
            //结束进程
            exit(0);
        }
    }
    while(-1 != wait(NULL));
    //删除信号量
    semctl(semid,0,IPC_RMID);
}
