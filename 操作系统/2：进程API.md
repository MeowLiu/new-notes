## 1.1fork系统调用
fork()是系统调用函数，用于创建新的线程，如果创建成功的话，返回新线程的PID给老线程，返回0给新线程。
```
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(int argc, char const *argv[]) {
    printf("hello world (pid: %d)\n", (int)getpid());
    int rc = fork();

    if (rc < 0) {
        fprintf(stderr, "fork failed.");
        exit(1);
    } else if (rc == 0) {
        printf("hello I am child (pid: %d)\n", (int)getpid());
    } else {
        printf("hello I am parent of %d (pid: %d)\n", rc, (int)getpid());
    }

    return 0;
}
```
## 1.2wait系统调用
wait让父进程等待子进程结束再继续往下运行，返回子进程的PID。
```
#include <stdio.h>
#include <stdlib.h>
#include <sys/wait.h>
#include <unistd.h>
  
int main(int argc, char const *argv[]) {
    printf("hello world (pid: %d)\n", (int)getpid());
    int rc = fork();
    
    if (rc < 0) {
        fprintf(stderr, "fork failed.");
        exit(1);
    } else if (rc == 0) {
        printf("hello I am child (pid: %d)\n", (int)getpid());
    } else {
        int wc = wait(NULL);
        printf("hello I am parent of %d (wc:%d) (pid: %d)\n", rc, wc,
               (int)getpid());
    }

    return 0;
}
```
## 1.3exec系统调用
exec可以在当前进程调用别的文件的代码，具体工作原理是将指定文件的代码读取到本进程，覆写自己的代码，从而开始执行。配合fork一起使用效果甚佳。
```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/wait.h>
#include <unistd.h>

int main(int argc, char const *argv[]) {
    printf("hello world (pid: %d)\n", (int)getpid());
    int rc = fork();

    if (rc < 0) {
        fprintf(stderr, "fork failed.");
        exit(1);
    } else if (rc == 0) {
        printf("hello I am child (pid: %d)\n", (int)getpid());
    } else {
        int wc = wait(NULL);
        printf("hello I am parent of %d (wc:%d) (pid: %d)\n", rc, wc,
               (int)getpid());
        char *my_args[3];
        my_args[0] = strdup("wc");
        my_args[1] = strdup("pl3.c");
        my_args[2] = NULL;
        execvp(my_args[0], my_args);
        printf("this shouldn't print out");
    }
  
    return 0;
}
```