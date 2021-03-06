# 死锁

## 原理

### 定义

> 死锁定义为一组**相互竞争系统资源或进行通信**的进程间的“**永久**”阻塞。当一组进程中的每个进程都在等待某个事件（典型情况下是等待释放所有请求的资源），而仅有这组进程中被阻塞的其他进程才可触发该事件时，就称这组进程发生了死锁。因为没有事件能够被触发，故死锁是永久性的。与并发进程管理中的其他问题不同，死锁问题并无有效的通用解决方案。         ——《操作系统：精髓与设计原理》P164

简单地说：死锁\(Deadlock\)是指一组进程因**竞争资源**而造成的一种**僵局**\(Deadly Embrace\) ， 即每个进程都占有部分资源，同时又需得到已被该组进程中其他占用的资源，若无外力作用，这些进程都将永远处于等待状态。

### 举例

![&#x6B7B;&#x9501;&#x56FE;&#x793A;](../.gitbook/assets/qq-tu-pian-20210524192145%20%281%29.jpg)

所有死锁都涉及多进程间资源的竞争冲突。常见例子是**交通死锁**。四辆汽车同时达到十字路口并停下（如图a），交叉路口上的四个象限就是需要被控制的资源。四辆车都想要直线通过十字路口：向北的汽车1需要ab，向南的汽车3需要cd，向西的汽车2需要bc，向东的汽车4需要ld。

此时任何一辆汽车继续通行的资源都能被满足，但如果四辆车同时前行，就会出现图b的死锁情况。每辆汽车都占有一个象限而需要的另一个象限被旁边的车占有，它们都无法继续同行。

另外，**哲学家进餐问题**中所有哲学家都拿起同一侧的筷子，则所有人都拿着一根筷子，而需要的另一根在旁边的哲学家手上，导致所有人都无法进餐，永远阻塞。

### 死锁、死循环、饥饿

死锁：所有进程等待对方占有的资源而无法继续运行的阻塞现象。

饥饿：由于长期得不到所需资源，某进程无法继续运行的现象。如：SPJ算法中，如果不断有短进程到来，则长进程始终得不到处理机，进而发生长进程的“饥饿”。

死循环：某进程执行过程中一直不跳出某循环的现象。

![&#x6982;&#x5FF5;&#x533A;&#x522B;](../.gitbook/assets/image%20%286%29.png)

### 

### 产生死锁的必要条件

1. **互斥条件**：只有对必须互斥访问使用的资源的争夺才会导致死锁（如哲学家的筷子、打印机等），像内存、扬声器这样可以让多个进程使用的资源是不会导致死锁的，因为进程无需阻塞等待这种资源。
2. **请求和保持/占有且等待条件**：进程已经保持了至少一个资源，但提出了新的资源请求，而该资源为其它进程占有，此时请求进程被阻塞，而已获得的资源不释放。
3. **不剥夺/不可抢占条件**：进程所获得资源在未使用完前无法被其他进程剥夺，只能主动释放。
4. **循环等待/环路等待条件**：存在一个闭合的进程资源循环等待链（即资源分配的有向图中存在环路）。链中每个资源已获得的资源被下个进程所请求。注意，循环等待未必死锁，而死锁一定循环等待。（循环等待是死锁的必要不充分条件）。

### 产生死锁的原因

![&#x5BF9;&#x4E0D;&#x53EF;&#x5265;&#x593A;&#x8D44;&#x6E90;&#x7684;&#x4E0D;&#x5408;&#x7406;&#x5206;&#x914D;&#xFF0C;&#x53EF;&#x80FD;&#x5BFC;&#x81F4;&#x6B7B;&#x9501;](../.gitbook/assets/image%20%2810%29.png)

### 联合进程图

![joint progress diagram](../.gitbook/assets/image%20%2813%29.png)

**进程推进顺序非法的例子：**

如上图，两个进程的联合进程图，展示了六条不同的进程执行顺序路径（A、B是两个不同的资源）：

1、Q进程获得B，然后获得A，再释放B和A；当P进程恢复执行时，它可以获得全部的资源；

 2、Q进程获得B，然后获得A；P进程开始执行但阻塞在对A的请求上；Q进程释放B和A；当P进程恢复执行时它可以获得全部资源； 

3、Q进程获得B，然后P进程获得A；Q继续执行但阻塞在对A的请求上；P进行继续执行但阻塞在对B的请求上，构成死锁； 

4、P进程获得A，然后Q进程获得B；Q继续执行但阻塞在对A的请求上；P进程继续执行但阻塞在对B的请求上，构成死锁； 

5、P进程获得A，然后获得B；Q进程开始执行但阻塞在对B的请求上；P释放A和B；当Q进程恢复执行时可获得全部资源；

 6、P进程获得A，然后获得B，再释放A和B；当Q进程恢复执行时可以获得全部资源。 

图出处：[https://blog.csdn.net/d5\_\_j9/article/details/90552063](https://blog.csdn.net/d5__j9/article/details/90552063)

## 死锁处理策略

### 死锁预防

破坏死锁产生的必要条件（一个或多个）但互斥无法被破坏（若需要对资源进行互斥访问，则OS必须支持互斥）。

**特点**：易实现，应用广泛，但源利用率低。

### 死锁避免

避免系统进入不安全状态，从而避免死锁。（银行家算法）

**特点**：此方法只需事先加以较弱限制条件便可获得较高资源利用率及系统吞吐量，但实现有难度。目前较完善系统中常用此法。

### 死锁检测和接触

允许死锁发生，但操作系统需要负责检测出死锁发生，并采取措施解除死锁。

**特点**：有可能使系统获得较好的资源利用率和系统吞吐量，但在实现上难度也最大。

## 死锁预防

### 破坏互斥条件

![&#x5982;&#x679C;&#x4E00;&#x5B9A;&#x8981;&#x7834;&#x574F;&#x4E92;&#x65A5;&#x6761;&#x4EF6;&#x7684;&#x60C5;&#x51B5;](../.gitbook/assets/image%20%283%29.png)

### 破坏不剥夺条件

![](../.gitbook/assets/image%20%285%29.png)

![](../.gitbook/assets/image%20%281%29.png)

### 破坏请求和保持条件

1.资源静态分配。即要求一个进程在运行之前，一次性申请所需的全部资源，若得不到满足，便不让其运行。进程在运行过程中不再请求资源。

![](../.gitbook/assets/image%20%287%29.png)

2.请求新资源之前须释放其它已占用的全部资源。

这两种方法的**缺点**： 

\(1\)资源利用率低，有些资源多数时间空闲不用；

\(2\)可能产生“饥饿”现象，如某个进程需要几种竞争激烈的资源，可能老是处于等待状态。

### 破坏循环等待条件

将所有的资源按类型编号，申请资源时必须按编号递增的顺序请求资源，同类资源（即编号相同的资源）一次性申请完。

![&#x6B63;&#x786E;&#x6027;&#x5206;&#x6790;](../.gitbook/assets/image%20%288%29.png)

**正确性证明**：我们可以假设两个进程A和B死锁。原因是A占有Xi、请求Xj，而B占有Xj、请求Xi。前半句可知i&lt;j，而后半句可知j&lt;i，这显然是矛盾的。不可能存在这样的情况。

**缺点：**

1、造成资源浪费的现象还会发生，如打印机编号一般比磁带机的小，但某个进程若先使用磁带机后用打印机，则会使打印机长期闲置不用。

2、不方便添加新设备，可能需要重新编号。

3、必须按次序申请资源，用户编程困难。

### 小结

![](../.gitbook/assets/image.png)

## 死锁避免

{% hint style="info" %}
在前一节中，我们可以看到，预防死锁实现简单，却严重损害性能。
{% endhint %}

{% hint style="success" %}
避免死锁的方法将系统状态分为安全和不安全，只要保持系统始终处于安全状态，就可以避免发生死锁。其施加的限制条件较弱，可以获得满意的系统性能。
{% endhint %}

在死锁避免\(dead lock avoidance\)中，是否允许当前的资源分配请求时通过判断该请求是否可能导致死锁来决定的。因此，死锁避免需要知道未来进程资源请求的情况。

### 方法

允许进程动态地申请资源，系统在资源分配前，先计算资源分配的安全性。若此次分配不会导致系统进入不安全状态， 便将资源分配给进程；否则，进程等待。

### 安全状态

指系统能按某种顺序如{Pn}=&lt;p0,p1,p2,...,pn&gt; \(称序列{Pn}为安全序列\)，来为每个进程分配其所需资源，直至最大需求，使每个进程都可顺序完成。若系统不存在这样一个安全序列，则称系统处于不安全状态。

**注意**：虽然并非所有不安全状态都是死锁状态，但当系统进入不安全状态后，便可能进而进入死锁状态；反之，只要系统处于安全状态，就可避免死锁。

**避免死锁的实质**在于：如何使系统不进入不安全状态。

### 举例

![](../.gitbook/assets/image%20%2814%29.png)

### 银行家算法

算法步骤：

1. 输入请求资源的进程编号IDX和请求的资源向量REQ\(从第1种到第m种资源的个数）；
2. 若请求的资源向量大于初始声明的Claim\[IDX\]向量减去已分配的Allocate\[IDX\]向量（即Need向量表示进程IDX还需要各种资源的数目），则认为出错，否则转入第三步；
3. 若请求的向量资源大于当前系统可用资源Available向量，则使进程IDX等待，否则转入第四步；
4. 尝试分配，调用安全性算法检测分配后是否安全。若安全，则真正分配。否则作废，使进程IDX等待。

安全性算法步骤：

1. 设置两个向量：工作向量Work=Available、Finish\[i\]表示是否已向进程i分配过资源。
2. 通过死循环不断检查进程列表中是否满足以下条件的进程：

   1. Finish\[i\]=false;
   2. Claim\[i\]-Allocate\[i\]&lt;=Work;

   若找到，则执行第三步，否则第四步。

3. 将进程i的Finish\[i\]标记为true，并赋值Work+=Allocate\[i\]表示进程i运行完成后会释放的资源数目要加到当前系统可用的资源数目上。
4. 扫描一遍所有进程的Finish是否为true，若存在false则跳出扫描，返回false表示当前为不安全状态。若没有找到false，则返回true表示当前为安全状态。

用C实现了一个简单的银行家算法程序，代码如下：

```c
#include <stdio.h>
#define m 3
#define n 4

//to record safe exectuting order
int order[n];

/*  struct for
*   system resource allocation state */
typedef struct {
    int resrc[m];    // resources in general
    int avail[m];    // available resouces
    int alloc[n][m]; // resources allocated
    int claim[n][m]; // resouces max claimed
} state;

/* return matrix a - matrix b */
int *delta(int a[], int b[], int need[]) {
    for (int i = 0; i < m; i++) {
        need[i] = a[i] - b[i];
    }
    return need;
}

/* return if matrix a <= matrix b */
int notBiggerThan(int a[], int b[]) {
    int flag = 1;
    for (int i = 0; i < m; i++) {
        if (a[i] > b[i]) {
            flag = 0;
            break;
        }
    }
    return flag;
}

int isSafe(state x) {
    int flag = 1;
    int Possible = 1;
    int cnt = 0; // index of oder array
    int Finish[n] = {0};
    int Work[m]; // current resources allocated
    for (int i = 0; i < m; i++)
        Work[i] = x.avail[i];

    while (Possible) {
        int Found = 0;
        for (int i = 0; i < n; i++) {
            int Need[m] = {0};
            if (!Finish[i]) {
                if (notBiggerThan(delta(x.claim[i], x.alloc[i], Need), Work)) {
                    for (int j = 0; j < m; j++) {
                        Work[j] += x.alloc[i][j];
                    }
                    Finish[i] = 1;
                    Found = 1;
                    order[cnt++] = i + 1;
                }
            }
        }
        if (!Found)
            Possible = 0;
    }

    for (int i = 0; i < n; i++)
        if (!Finish[i]) {
            flag = 0;
            break;
        }

    return flag;
    // 0:unsafe ; 1:safe ;
}

void allocResrc(state *src, int req[], int pIndex) {
    for (int i = 0; i < m; i++) {
        src->alloc[pIndex][i] += req[i];
        src->avail[i] -= req[i];
    }
}

/*   params:
 *       @x:  current system resource allocation state
 *       @pIndex:    process index
 *       @request:   request vector */
void banker(state x, int pIndex, int request[]) {
    int Need[m] = {0};
    if (!notBiggerThan(request,
                       delta(x.claim[pIndex], x.alloc[pIndex], Need))) {
        printf("[ERROR] Requsted resrc num is bigger than it've claimed!\n");
    } else if (!notBiggerThan(request, x.avail)) {
        printf("[WARN] Requsted resrc num is bigger than those "
               "available!\nProcess %d blocked.\n",
               pIndex);
    } else {
        state tmp = x;
        allocResrc(&tmp, request, pIndex);
        int s = isSafe(tmp);
        if (s) {
            x=tmp;  //allocate for real
            printf("Safe! Found an order:");
            for (int i = 0; i < n; i++)
                printf("%d ", order[i]);
            printf("\n");
        } else
            printf("Unsafe!\n");
    }
}

/*   params:
 *       @IDX:    process index
 *       @REQ[]:  request vector */
void prompt(int *IDX, int REQ[]) {
    printf("Please input the process index(1-%d):\n", m + 1);
    scanf("%d", IDX);
    *IDX -= 1;
    printf("Please input the request resrc vector(divided by space):\n");
    for (int i = 0; i < m; i++) {
        scanf("%d", &REQ[i]);
    }
}

int main() {
    int IDX;
    int REQ[m];
    state input = {
        claim : {{3, 2, 2}, {6, 1, 3}, {3, 1, 4}, {4, 2, 2}},
        alloc : {{1, 0, 0}, {5, 1, 1}, {2, 1, 1}, {0, 0, 2}},
        avail : {1, 1, 2},
        resrc : {9, 3, 6}
    };
    prompt(&IDX, REQ);
    banker(input, IDX, REQ);
    return 0;
}
```



