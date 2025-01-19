# Race Conditions
Race conditions stem from simple programming mistakes developers often make and can lead to: </br>
1. Steal money from online banks/e-commerce websites
2. Crypto currency exchanges</br>

# Mechanisms
**Race conditions** happen when two blocks of code are designed to be executed in sequence, get executed out of sequence.</br>
**Concurrency**: is the ability to run different parts of a program at once without affecting the output of the program.</br>
Concurrency has two types:</br>
|Multiprocessing | Multithreading|
|--------|-----------|
|The ability to run multiple CPUs at the same time to perform simultaneous computations| The ability for a single CPU to provide multiple threads or concurrent executions|
|![image](https://github.com/user-attachments/assets/28a3e8c9-a247-4805-978e-01822d820398) |![image](https://github.com/user-attachments/assets/8418629b-663f-427e-adb1-5f9ddee983ba)|

>[!Note]
>Threads don't run at the same time. Instead, they take turns using the CPU.
>When one thread is waiting (like for user input), another thread can use the CPU to do its work. This way the CPU stays busy and resources aren't wasted.</br>

**Scheduling:** is the process of arranging the sequence of execution of multiple threads.</br>
There are differt scheduling algorithms depending on the system:</br>
1.  Priority-based scheduling.  
2. Round-Robin scheduling-> tasks take turns using the CPU, giving each one a fair share of time, regardless of priority.</br>

**Flexible scheduling is what causes race conditons.Race conditions occur when developers don't follow proper rules for handling multiple threads working at the same time, which can lead to unexpected problems.** </br>
Since scheduling swaps between the threads, it is hard to know the sequence of executing actions. (Why that matters?) ⬇️
### Concurrency and Parallelism
Assume we have a global variable (A) and two threads that need to add up 1 to that variable (A=0). If the two threads working concurrently **(normal execution of two threads operating on the same resource)*** , the flow is as the following:</br>
(Concurret execution)
1. Thread 1 read value of A (A = 0)
2. Thread 1 increase A by 1
3. Thread 1 write the value of A -> now (A = 1)
4. Thread 2  takes turn and read value of A (A =1)
5. Thread 2 increase A by 1
6. Thread 2 write the value of A (A =2)
7. Final result of A is (A = 2)</br>
>[!Note]
>Concurrency is not parallelism , concurrency is like juggling multiple tasks at once, but not necessarily doing them at the exact same time, while paralellism  is when tasks are actually executed at the same time.</br>

Now if the two threads are running in parallel without consering the conflicts that might happen at accessing the same resource, the flow would be as the following: </br>
1. Thread 1 read value of A
2. Thread 2 read value of A 
3. Thread 1 increase A by 1 (A =0)
4. Thread 2 increase A by (A=0)
5. Thread 1 write the value of A (A =1)
6. Thread 2 write the value of A (A =1)
7. Final result of A is (A = 1) </br>

**Here race condition happens!**; because the outcome of execution one thread depends on the outcome of another thread.
