what is semaphore?types of semaphore

semaphore is a synchronization tool used in real-time operating systems (RTOS) and multithreaded applications to control access to shared resources and prevent race conditions.

Think of it like a traffic signal that controls when a task (or thread) can access a resource.

🔹 Purpose of Semaphores:

Prevent multiple tasks from using the same resource at the same time.

Help implement mutual exclusion and task synchronization.
ypes of Semaphores:
1. Binary Semaphore

Has only two states: 0 (locked) and 1 (unlocked).

Used for mutual exclusion (like a simple lock).

Example: Used to signal between an ISR and a task.

2. Counting Semaphore

Holds a count value, representing the number of available resources.

Used when multiple identical resources are shared (e.g., connection pool).

Tasks decrement the count when accessing a resource, and increment when done.

3. Mutex (Mutual Exclusion Semaphore)How Semaphore is Used:

Special type of binary semaphore with ownership.

How Semaphore is Used:

A binary semaphore is created.

When the button is pressed, the interrupt service routine (ISR) gives the semaphore.

The LED task waits (blocks) on the semaphore.

Once the semaphore is given, the task wakes up and toggles the LED.


Only the task that takes (locks) the mutex can give (unlock) it.

