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
Since scheduling swaps between the threads, it is hard to know the sequence of executing actions.
