---
layout: single
title: "C++ 11 Multithreading"
author: "Danni"
header: 
  overlay_iamges: "assets/images/post/cplus.png"
header-mask: 0.4
toc: true
toc_sticky: true
lang: en
tags:
  - C++
---

Multithreading is a specialized form of multitasking which allows your computer to run two or more programs concurrently. In general, there are two types of multitasking: process-based and thread-based.

Process-based multitasking handles the concurrent execution of programs. Thread-based multitasking deals with the concurrent execution of pieces of the same program.

Perhaps one of the biggest change to the language since C++ 11 is the addition of multithreading support. Before C++11, it was possible to target multicore computers using OS facilities (pthreads on Unix like systems) or libraries like OpenMP and MPI.

```c++
#include <iostream>
#include <thread>
#include <Windows.h>
 
using namespace std;
 
void thread01()
{
	for (int i = 0; i < 5; i++)
	{
		cout << "Thread 01 is working ！" << endl;
		Sleep(100);
	}
}
void thread02()
{
	for (int i = 0; i < 5; i++)
	{
		cout << "Thread 02 is working ！" << endl;
		Sleep(200);
	}
}
 
int main()
{
	thread task01(thread01);
	thread task02(thread02);
	task01.join();
	task02.join();
 
	for (int i = 0; i < 5; i++)
	{
		cout << "Main thread is working ！" << endl;
		Sleep(200);
	}
	system("pause");
}
```


However, the join function will block the main thread so that the main thread can only be processed after all child threads finish processing. To optimize such, we can use detach to separate child and main threads to facilitate execution.


```c++
#include <iostream>
#include <thread>
#include <Windows.h>
 
using namespace std;
 
void thread01()
{
	for (int i = 0; i < 5; i++)
	{
		cout << "Thread 01 is working ！" << endl;
		Sleep(100);
	}
}
void thread02()
{
	for (int i = 0; i < 5; i++)
	{
		cout << "Thread 02 is working ！" << endl;
		Sleep(200);
	}
}
 
int main()
{
	thread task01(thread01);
	thread task02(thread02);
	task01.detach();
	task02.detach();
 
	for (int i = 0; i < 5; i++)
	{
		cout << "Main thread is working ！" << endl;
		Sleep(200);
	}
	system("pause");
}
```

### Mutex

When multiple threads are making changes to the same variable, the data can be corrupted if not taking protective measures. For example, in the following code, thread01 and thread02 are both making changes to the totalNum, and its output is corrupted such that some desired values are not printed while some are duplicatedly printed.

The reason could be, while thread01 is making changes, thread02 is also making changes so the output of thread01 is the actual processed result of thread02. To deal with such problems, we can use mutex to update shared data. To use mutex, just **#include <mutex>** in the header.

The mutex class is a synchronization primitive that can be used to protect shared data from being simultaneously accessed by multiple threads. mutex offers exclusive, non-recursive ownership semantics: A calling thread owns a mutex from the time that it successfully calls either lock or try_lock until it calls unlock .

`……9494, 9292, 9090, 8888, ……`


Original code without mutex:

```c++
int totalNum = 100;
 
void thread01()
{
	while (totalNum > 0)
	{
		cout << totalNum << endl;
		totalNum--;
		Sleep(100);
	}
}
void thread02()
{
	while (totalNum > 0)
	{
		cout << totalNum << endl;
		totalNum--;
		Sleep(100);
	}
}
 
int main()
{
	thread task01(thread01);
	thread task02(thread02);
	task01.detach();
	task02.detach();
	system("pause");
}
```

Updated code using Mutex:

```c++
#include <iostream>
#include <thread>
#include <Windows.h>
#include <mutex>
 
using namespace std;
 
mutex mu; 
 
int totalNum = 100;
 
void thread01()
{
	while (totalNum > 0)
	{
		mu.lock();
		cout << totalNum << endl;
		totalNum--;
		Sleep(100);
		mu.unlock();
	}
}
void thread02()
{
	while (totalNum > 0)
	{
		mu.lock();
		cout << totalNum << endl;
		totalNum--;
		Sleep(100);
		mu.unlock();
	}
}
 
int main()
{
	thread task01(thread01);
	thread task02(thread02);
	task01.detach();
	task02.detach();
	system("pause");
}
```

Now the output is normal:
```
100

99

98
```
