[TOC]



# java 多线程

#### 概念

- 进程：进程是程序运行和资源分配的基本单位，一个程序至少有一个进程，一个进程至 

少有一个线程。进程在执行过程中拥有独立的内存单元，而多个线程共享内存资源，减少切 

换次数，从而效率更高

- 线程：线程是进程的一个实体，是 cpu 调度和分派的基本单位，是比程序 

  更小的能独立运行的基本单位。同一进程中的多个线程之间可以并发执行



#### 守护进程

进程分为守护进程，和非守护进程