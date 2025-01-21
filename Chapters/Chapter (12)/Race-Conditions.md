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
# When a Race Condition Becomes a Vulnerability
Race condition becomes a vulenrability when it affects a security control mechanism where attackers can induce a situation in which a sensitive action executes before a security check is complete.</br>
Assume your have two bank accounts; account A that has balance of $500 and account B that has balance of $0, and you want to transfer money from account A to account B. </br>
**Process of transfering money involves three steps:** </br>
1. Check if originating account has enough money to transfer
2. Add money to the destination account
3. Remove the same amount from the originating account</br>

#### Assume you initiated two money trasfers of $500 from account A to account B at the same time. The ideal scenario is as the following:</br>
1. Thread 1 check account A balance ($500) -> Balance of account A+B = $500
2. Thread 1 add $500 to account B -> Balance of account A+B = $1000 (($500 in A, $500 in B)
3. Thread 1 Deduct $500 from account A -> Balance of account A+B = $500 ($0 in A, $500 in B)
4. Thread 2 check account A balance ($0) -> Balance of account A+B = $500 ($0 in A, $500 in B)
5. Thread 2 transfer fails (low balance) -> Balance of account A+B = $500 ($0 in A, $500 in B) </br>

**In this scenario, you end up with the correct amount of mony.** </br>

#### But what if you can send the two requests simultaneously?</br>
1. Thread 1 check account A balance ($500) -> Balance of account A+B = $500
2. Thread 2 check account A balance ($500) -> Balance of account A+B = $500
3. Thread 1 add $500 to account B -> Balance of account A+B = $1000 (($500 in A, $500 in B)
4. Thread 2 add $500 to account B -> Balance of account A+B = $1500 ($500 in A , $1000 in B)
5. Thread 1 Deduct $500 from account A ->  Balance of account A+B = $1000 ($0 in A, $1000 in B)
6. Thread 2 Deduct $500 from account A ->  Balance of account A+B = $1000 ($0 in A, $1000 in B) </br>

**In this scenario, you end up with more money than you started with.**
>[!Note]
> Most race condition vulnerabilities are exploited to manipulate mony, gift card credits, votes, social media likes, and can be used to bypass access controls and trigger other vulnerabilities.</br>

# Prevention
1. Follow synchronization mechanisms -> (only one process or thread can access a shared resource at a time)
    1. Resource lock -> (when one thread acquires a lock, other threads attempting to access the same resource must wait until resource is released)
2. Be aware of built in synchronization mechanisms that most programming languages provide/support
3. Follow secure coding practices -> (lowers the risk of complete system compromise)
    1. Pinciple of least privilege -> (the applications and processes should be granted only the privileges they need to complete their tasks)</br>
