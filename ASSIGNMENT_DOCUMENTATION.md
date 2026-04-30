# Assignment 3 - Complete Documentation

**Student Name**: [Husaam abdullah alamri]  
**Student ID**: [445050170]  
**Date Submitted**: [April 30, 2026]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [https://drive.google.com/file/d/1nfJfI9mYv9jln3sVxOZXFkE9O3mh5nND/view?usp=sharing]

**Video filename**: `[YourStudentID]_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - [April 23, 2026]
Forked the starter repository from the provided GitHub link.  Changed the repository visibility settings to 'Public' and renamed the repository to OS-Assignment3-FirstName-LastName as per assignment requirements.  Cloned the repository to the local machine using Visual Studio Code to set up the development environment.  Initialized the project by updating the studentID variable in SchedulerSimulationSync.java with my official university student ID.  
**1.5 Hours**
---

### Entry 2 - [April 27, 2026]
**Task 1 **Implementation: Successfully implemented ReentrantLock to safeguard critical sections involving shared counter variables (contextSwitchCount, completedProcessCount, totalWaitingTime). Utilized try-finally blocks to ensure robust resource release and prevent potential deadlocks.

**Task 2 **Implementation: Applied a dedicated ReentrantLock for the executionLog (ArrayList) to maintain thread safety. This prevents ConcurrentModificationException by serializing access to the log during concurrent process execution.

**Verification**: Conducted multiple test runs to ensure that synchronization mechanisms effectively mitigate race conditions and provide consistent output.
**1.5 Hours**
---

### Entry 3 - [April 28, 2026]
**Task 3 Implementation**: Integrated a binary Semaphore with 1 permit to control concurrent CPU access.

**Process Logic**: Modified the run() and runToCompletion() methods in the Process class to acquire the semaphore before execution and release it in the finally block.

**Synchronization Goal**: This ensures that only one process can simulate execution on the CPU at any given time, preventing overlapped output and ensuring mutual exclusion.
**1.5 Hours**

---

### Entry 4 - [April 30, 2026]
- **Execution**: Verified the synchronization logic by running the simulation through the terminal to ensure environment stability.
- **Results**: All processes shared the CPU correctly without race conditions, and all statistics were logged accurately.
- **Video Demonstration**: The video walkthrough has been created, uploaded to Google Drive, and the link is provided at the top of this document.
**2.5 Hours**

---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:

[**In the original code, two significant race conditions existed:

The contextSwitchCount increment: This shared integer was accessed by multiple threads simultaneously. Because count++ is not an atomic operation (it involves read-modify-write), multiple threads could read the same value and overwrite each other's increments, leading to a final count lower than the actual switches.

The executionLog ArrayList: ArrayList is not thread-safe. If two process threads tried to call add() at the exact same moment, it could lead to a ConcurrentModificationException or cause data loss where one log message overwrites another.**]

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

[**A ReentrantLock is a mutual exclusion mechanism (Mutex) that ensures only one thread can access a specific block of code at a time; it is "owned" by the thread that locks it. A Semaphore maintains a set of permits; threads "acquire" a permit to proceed and "release" it when done. In my code, I used ReentrantLock for the shared counters and logs to ensure data integrity. I used a Semaphore with 1 permit in the Process class to simulate the CPU, ensuring only one process thread is "executing" at any given time.**]

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

[**Deadlock occurs when two or more threads are blocked forever, each waiting for a resource held by the other. To prevent this, I used the try-finally block technique. By placing the unlock() or release() calls inside a finally block, I ensured that resources are always freed even if an exception occurs during execution. Additionally, I maintained a simple lock hierarchy where locks are acquired and released in a consistent order to avoid circular wait conditions.**]

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

[**I chose a fine-grained locking approach by using separate locks for different resources: counterLock for numeric variables and logLock for the execution list. Since the counters (like contextSwitchCount) and the log (the ArrayList) are independent, using separate locks allows one thread to increment a counter while another thread is simultaneously writing to the log. This design provides better concurrency and performance compared to a coarse-grained approach (one lock for everything), as it minimizes the time threads spend waiting for a lock that isn't strictly necessary for the resource they are accessing.**]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: contextSwitchCount, completedProcessCount, totalWaitingTime.

**Why they need protection**: They are shared among all process threads. Concurrent updates lead to lost increments (Race Condition).

**Synchronization mechanism used**: ReentrantLock (named counterLock).

**Code snippet**:
```java
public static void incrementContextSwitch() {
    counterLock.lock();
    try { contextSwitchCount++; }
    finally { counterLock.unlock(); }
}
```

**Justification**: A Mutex lock is the most efficient way to ensure atomic updates to simple shared variables.

---

### Critical Section #2: Execution Log

**What resource**: List<String> executionLog.

**Why it needs protection**: ArrayList is not thread-safe; simultaneous access causes crashes or data corruption.

**Synchronization mechanism used**: ReentrantLock (named logLock).

**Code snippet**:
```java
public static void logExecution(String message) {
    logLock.lock();
    try { executionLog.add(message); }
    finally { logLock.unlock(); }
}
```

**Justification**: 

---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: To simulate a single-core CPU environment.

**Number of permits and why**: 1 permit, because a standard CPU core can only execute one process at a time.

**Where implemented**: run() and runToCompletion() methods of the Process class.

**Code snippet**:
```java
SharedResources.cpuSemaphore.acquire();
try { // execution logic... 
finally { SharedResources.cpuSemaphore.release(); }
}
```

**Effect on program behavior**: 

---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**: 
```bash
java SchedulerSimulationSync # Run 1
java SchedulerSimulationSync # Run 2
java SchedulerSimulationSync # Run 3
java SchedulerSimulationSync # Run 4
java SchedulerSimulationSync # Run 5
```

**Results**: 
The "Total Completed Processes" always matched the initial number of processes, and "Total Context Switches" remained consistent across runs with the same Seed.

**Why synchronization is necessary**: 
(Explain what race conditions COULD occur without synchronization, even if you didn't observe them. Explain which shared resources need protection and why.)

**Conclusion**: Synchronization successfully eliminated the randomness caused by race conditions.

---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**: Observed the terminal output during high-concurrency runs (many processes).

**Results**: Zero exceptions occurred.

**What this proves**: The logLock effectively protected the ArrayList.

---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: Completed Processes: Should match the total number of processes generated (e.g., if 15 processes were created, 15 must finish).

Execution Log: Total entries should reflect each quantum start and final completion for every process.

Context Switches: Should be a positive integer reflecting the total number of times the CPU was acquired.

**Actual values**: Completed Processes: 100% completion rate (all processes finished).

Average Waiting Time: Calculated accurately based on the protected totalWaitingTime variable.

Log Entries: Thousands of entries recorded without any data loss or corruption.

**Analysis**: The actual values perfectly match the expected behavior of a synchronized Round Robin scheduler. The use of ReentrantLock ensured that even with high concurrency, every single millisecond of waiting time and every context switch was counted accurately, resulting in 100% data integrity.

---

### Test 4: Different Scenarios
**Scenario tested**: [Testing the system with a large number of processes (e.g., 20+ processes) and a long Time Quantum.]

**Purpose**: To stress-test the synchronization mechanisms and ensure that the Semaphore effectively manages the CPU even when many threads are competing for execution simultaneously.

**Results**: The program maintained its stability and did not crash or hang. The Semaphore successfully queued the threads, allowing only one to execute its quantum at a time, while the others waited in the "Ready Queue" printed in the terminal.

**What I learned**: What I learned:
I learned that robust synchronization is even more critical as the system load increases. Using a binary Semaphore with acquire() and release() is a powerful way to manage shared hardware resources (like a CPU core) and maintain orderly execution in a multi-threaded environment.

---

## Part 5: Reflection and Learning

### What I learned about synchronization:

I learned that writing concurrent code is much more complex than sequential code because of unpredictable thread interleaving. Synchronization isn't just about adding locks; it's about identifying exactly where shared data is modified and ensuring that only one thread can access those "critical sections" at a time. I now understand the importance of the finally block to prevent deadlocks and how semaphores can be used to manage limited resources like a CPU core.

---

### Real-world applications:

Give TWO examples where synchronization is critical:

1. Banking Systems: Ensuring that two simultaneous withdrawals don't result in an incorrect balance.

2. Database Management: Handling multiple users trying to update the same record at the exact same time.

---

### How I would explain synchronization to others:

[Explain to someone who just finished Assignment 1 - use simple terms and analogies]

---

## Part 6: GitHub Repository Information

Repository URL: https://github.com/hussamalamri2/OS-Assignment3-Hussam-Alamri.git

Number of commits: 4

Commit messages:

1. Setup: Forked repository and initialized project with Student ID.

2. Implementation: Added ReentrantLocks for Task 1 and Task 2.

3. Implementation: Integrated CPU Semaphore for Task 3 and verified via terminal.

4. Documentation: Finalized ASSIGNMENT_DOCUMENTATION.md with video link.

---

## Summary

**Total time spent on assignment**:**7 Hours**

**Key takeaways**: 
1. Concurrency Control: I learned how to identify critical sections in a multi-threaded environment where multiple threads access shared resources simultaneously.

2. Resource Management: I gained hands-on experience using Semaphores to manage access to a limited resource (the CPU) and ReentrantLocks for data integrity.

3. Robust Coding: I understood the vital importance of the try-finally pattern to prevent deadlocks and ensure that locks are always released.

**Most challenging aspect**: 

**What I'm most proud of**: 

---

**End of Documentation**
