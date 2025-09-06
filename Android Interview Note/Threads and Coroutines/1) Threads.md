A **thread** is the smallest unit of execution within a process that can be managed independently by a scheduler. Think of a **process** as a complete, self-contained application, like a web browser. Within that process, threads are the individual workers that perform specific tasks. For example, one thread might be responsible for displaying the web page, while another handles network requests to download images.

## How a Thread Works

Threads work by sharing resources with other threads within the same process. This is what makes them "lightweight" compared to processes.

### Shared Resources 🤝

All threads within a process share the same:

- **Memory space:** They can access the same code, global variables, and heap memory. This makes communication between threads very fast and efficient.
- **File handles:** They can all access the same open files and network connections.

### Independent Resources 🧠

However, each thread has its own unique, independent set of resources that define its execution context. This allows it to be scheduled and run separately from other threads. These resources include:

- **Program Counter:** This keeps track of the next instruction the thread needs to execute.
- **CPU Registers:** These are small, fast storage locations on the CPU that hold temporary data for the thread.
- **Stack:** This is a memory area for the thread's local variables and function call history.

When a single-core CPU runs a multi-threaded application, it creates the illusion of simultaneous execution through a technique called **context switching**. The operating system's scheduler rapidly switches between threads, giving each one a tiny slice of time on the CPU. This switching happens so fast that it appears as if all threads are running at the same time. On a multi-core processor, different threads can truly execute in parallel on different cores