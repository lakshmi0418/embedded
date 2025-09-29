what is semaphore?types of semaphore

semaphore is a synchronization tool used in real-time operating systems (RTOS) and multithreaded applications to control access to shared resources and prevent race conditions.

Think of it like a traffic signal that controls when a task (or thread) can access a resource.

🔹 Purpose of Semaphores:

Prevent multiple tasks from using the same resource at the same time.

Help implement mutual exclusion and task synchronization.
ypes of Semaphores:
1. Binary Semaphore


Used for mutual A binary semaphore is a synchronization primitive that can have only two values: 0 and 1. It is often used to implement mutual exclusion (mutex) — allowing only one thread to access a critical section at a time.exclusion (like a simple lock).

Example: Used to signal between an ISR and a task.
#include <stdio.h>
#include <pthread.h>
#include <semaphore.h>
#include <unistd.h>

sem_t binary_semaphore;  // Binary semaphore initialized to 1

void* thread_function(void* arg) {
    int thread_id = *(int*)arg;

    printf("Thread %d is waiting to enter the critical section...\n", thread_id);

    // Wait (P operation) - will block if the value is 0
    sem_wait(&binary_semaphore);

    // Begin critical section
    printf("Thread %d has entered the critical section.\n", thread_id);
    sleep(2);  // Simulate work in critical section
    printf("Thread %d is leaving the critical section.\n", thread_id);
    // End critical section

    // Signal (V operation) - set semaphore back to 1
    sem_post(&binary_semaphore);

    return NULL;
}

int main() {
    pthread_t t1, t2;
    int id1 = 1, id2 = 2;

    // Initialize binary semaphore to 1
    sem_init(&binary_semaphore, 0, 1);

    // Create two threads
    pthread_create(&t1, NULL, thread_function, &id1);
    pthread_create(&t2, NULL, thread_function, &id2);

    // Wait for threads to finish
    pthread_join(t1, NULL);
    pthread_join(t2, NULL);

    // Destroy the semaphore
    sem_destroy(&binary_semaphore);

    return 0;
}



2. Counting Semaphore

Holds a count value, representing the number of available resources.

Used when multiple identical resources are shared (e.g., connection pool).

Tasks decrement the count when accessing a resource, and increment when done.


#include <stdio.h>
#include <pthread.h>
#include <semaphore.h>
#include <unistd.h>

#define NUM_USERS 5
#define NUM_PRINTERS 3

sem_t printer_semaphore;

void* user_thread(void* arg) {
    int user_id = *(int*)arg;

    printf("User %d is waiting to print...\n", user_id);

    // Wait for an available printer (decrement semaphore)
    sem_wait(&printer_semaphore);

    printf("User %d got access to a printer.\n", user_id);
    sleep(2);  // Simulate time taken to print
    printf("User %d is done printing.\n", user_id);

    // Release the printer (increment semaphore)
    sem_post(&printer_semaphore);

    return NULL;
}

int main() {
    pthread_t users[NUM_USERS];
    int user_ids[NUM_USERS];

    // Initialize semaphore with NUM_PRINTERS resources
    sem_init(&printer_semaphore, 0, NUM_PRINTERS);

    // Create threads for users
    for (int i = 0; i < NUM_USERS; i++) {
        user_ids[i] = i + 1;
        pthread_create(&users[i], NULL, user_thread, &user_ids[i]);
    }

    // Wait for all threads to finish
    for (int i = 0; i < NUM_USERS; i++) {
        pthread_join(users[i], NULL);
    }

    // Destroy the semaphore
    sem_destroy(&printer_semaphore);

    return 0;
}

3. Mutex (Mutual Exclusion Semaphore)How Semaphore is Used:

When the button is pressed, the interrupt service routine (ISR) gives the semaphore.
Mutual Exclusion (often abbreviated as mutex) means only one thread or process can access a shared resource at a time.
Special type of binary semaphore with ownership.

How Semaphore is Used:

A binary semaphore is created.

When the button is pressed, the interrupt service routine (ISR) gives the semaphore.

The LED task waits (blocks) on the semaphore.

Once the semaphore is given, the task wakes up and toggles the LED.

Why it's needed:

In multi-threaded or multi-process programs:

If two threads try to write to the same file or memory at the same time, it can cause data corruption.

Mutual exclusion prevents this by locking the resource, so only one thread can use it until it’s done.

🧠 Real-Life Example:

Imagine a bathroom with one key. Only one person can use it at a time:

If someone is inside, others have to wait.

Once they come out and return the key, the next person can enter.
Only the task #include <stdio.h>
#include <pthread.h>

#define NUM_ITERATIONS 1000000

int counter = 0;             // Shared resource
pthread_mutex_t lock;        // Mutex variable

void* increment(void* arg) {
    for (int i = 0; i < NUM_ITERATIONS; i++) {
        // Lock the mutex before accessing shared resource
        pthread_mutex_lock(&lock);

        counter++;

        // Unlock the mutex after done
        pthread_mutex_unlock(&lock);
    }
    return NULL;
}

int main() {
    pthread_t t1, t2;

    // Initialize the mutex
    pthread_mutex_init(&lock, NULL);

    // Create two threads
    pthread_create(&t1, NULL, increment, NULL);
    pthread_create(&t2, NULL, increment, NULL);

    // Wait for both threads to finish
    pthread_join(t1, NULL);
    pthread_join(t2, NULL);

    // Destroy the mutex
    pthread_mutex_destroy(&lock);

    // Print the result
    printf("Final counter value: %d\n", counter);  // Should be 2 * NUM_ITERATIONS

    return 0;
}
that takes (locks) the mutex can give (unlock) it.

Real-Time Example of a Mutex
💡 Real-Life Analogy:

Imagine a bathroom with only one key. If a person has the key, no one else can enter until they return it. This ensures mutual exclusion — only one person (or thread) can use the bathroom (or resource) at a time.
