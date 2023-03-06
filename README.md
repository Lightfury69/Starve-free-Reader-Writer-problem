# Starvers-free-Readers-writers-problem

The Reader-Writer Issue is a well-known issue in computer science where numerous programmes simultaneously access a data structure, such as a database or storage area. The important area is set up so that only one writer can access it at any given time, whereas numerous readers can do so concurrently. To accomplish this, we use semaphores so that access by writers and readers will not conflict and process synchronisation will be effectively met. Yet, depending on their objectives, this can result in the starvation of either readers or writers.

The Reader-Writer's problem involves synchronising numerous operations, which can be divided into two categories:

**Readers** - They read data from a shared memory location

**Writers** - They write data to the shared memory location

# Starve-Free Solution
In the starve-free solution, the entry_mutex semaphore is used with the read_mutex and write_mutex semaphore. This entry_mutex semaphore is first required to be obained before anyone (the reader or the writer) accesses the write_mutex or before anyone (any reader) enters the critcal section directly. This solves the problem of starvation as if readers keep coming one after another, then this won't starve the writers as it used to above. Here, if a writer comes in between two readers, and even if some readers are still present in the critical section, the next reader if it comes after a writer, the writer would have already acquired the entry_mutex and thus the reader can't acquire it and thus after the existing readers exit the critical section, the writer that was waiting would enter the critical section and the same process would repeat. Thus readers and writers are now at equal priority and none would starve. Doing this also preserves the advantage of readers not having to acquire the rw_mutex everytime, when some other reader is already present. Thus an effiecient and starve-free solution to the Reader-Writer problem.

#### Reader's Implementation
```
do{

    // Entry Section
    entry_mutex->wait();
    read_mutex->wait();
    counter++;
    if(counter == 1)      // this implies that the first reader tries to access
    {
      write_mutex->wait();
    }
    read_mutex->signal();   // now readers that have acquired the entry_mutex can access the read_count
    entry_mutex->signal();  //  Once the reader is ready to enter the critical section, it frees the entry_mutex semaphore so that next process can be ready.
                            //  This entry_mutex semaphore can be acquired by either a reader or writer, whichever arrives first.
    /**
     * 
     * Critical Section
     * 
    */

   // Exit Section
   read_mutex->wait();
   counter--;
   if(counter == 0) write_mutex->signal();  // the last reader frees the write_mutex
   read_mutex->signal();

   // Remainder Section

}while(true);
```
#### Writer's Implementation
```
do{

    // Entry Section
    entry_mutex->wait();       //  the writer also waits for the entry mutex first and can be acquired even if a reader is already present in critical section
    write_mutex->wait();       //  the writer process would be blocked while waiting. Once free it will acquire the write_mutex and enter the critical section

    entry_mutex->signal();
    /**
     * 
     * Critical Section
     * 
    */

    // Exit Section
    write_mutex->signal();
    

    // Remainder Section

}while(true);
```

# Checking the starve free solution
### Mutual Exclusion
To achieve mutual exclusion, the critical section may at any moment have only one writer process present or numerous reader processes but no writer processes. The read mutex and write mutex semaphores guarantee this. Entrance is only granted to a writer when the critical section is clear, and furthermore to readers when there are other readers present. Mutual exclusion is met as a result.
### Progress
There is no possibility of a deadlock developing in our system because of the way the code is structured. When semaphores are released when each process completes its execution in the critical section, readers and writers also take a finite amount of time, allowing others to join the critical section. As a result, the progress condition is met.
### Bounded Waiting
Writers would have starved in the past if readers arrived before other readers have completed, but now thanks to the entry mutex semaphore, everyone gets queued up and is released one at a time. The same is true for readers, who have the opportunity to enter the critical part in a group once they get to the head of the line. As a result, the bounded waiting criteria are met because neither the reader nor the writer processes must wait endlessly.

**As we can see that all the three requirements have been properly met. We can say that the solution above is truly starve-free.**
