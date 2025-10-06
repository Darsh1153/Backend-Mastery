# Backend-Mastery

<img width="2048" height="1216" alt="image" src="https://github.com/user-attachments/assets/3369379f-eae0-4030-ba09-7e4ac268e4a4" />

# Node.js Event Loop - Study Notes

## Easy Explanation
The Event Loop is like a chef 🧑‍🍳 in a single-threaded kitchen. 
He can only cook one dish at a time but delegates long tasks (like file I/O, timers) 
to assistants and gets notified when they're done.

## Advanced Explanation
The Event Loop has multiple **phases** that execute in a cycle:
1. **Timers** (`setTimeout`, `setInterval`)
2. **Pending Callbacks**
3. **Idle/Prepare**
4. **Poll** (I/O events like `fs.readFile`)
5. **Check** (`setImmediate`)
6. **Close Callbacks`

### Microtasks
- `process.nextTick()`
- Promises (`.then`, `async/await`)

Microtasks run immediately after the current operation, before the loop continues.

## Example Output Order

```js
Last line of the file.
nextTick
Promise
Timer expired
setImmediate
File Reading CB
2nd nextTick
2nd setImmediate
2nd timer



```

<br/>
# WHAT IS EPOLL AND KQUEUE MECHANISM
# ⚙️ epoll & kqueue — The Power Behind Non-Blocking I/O

> A simple yet detailed explanation of how `epoll` (Linux) and `kqueue` (macOS/BSD) work under the hood to make non-blocking I/O possible in systems like Node.js.

---

## 🧠 Why Do We Need epoll and kqueue?

Traditionally, in older programming models, each client or file operation needed its **own thread**.  
That means if you had 10,000 network connections, you’d need 10,000 threads — which is expensive and inefficient.

Operating systems solved this with **I/O multiplexing**, which means:
> “Monitor multiple I/O sources at once, and tell me when one is ready.”

That’s what `epoll` (Linux) and `kqueue` (macOS/BSD) do.

---

## 🟢 The Problem (Before epoll / kqueue)

Imagine you’re waiting for multiple deliveries 📦:
- You check the door every second to see if someone has arrived.  
  → This wastes time (CPU constantly checking).  

That’s how **poll()** or **select()** worked — they repeatedly asked the OS:  
> “Is socket #1 ready? Is socket #2 ready? … Is file #3 ready?”

This is called **polling**, and it’s inefficient when there are many sockets/files.

---

## ⚡ The Solution: epoll & kqueue

Now imagine:
> Instead of you checking, the **delivery guy calls you** when he’s at the door. 📞

That’s exactly what `epoll` and `kqueue` do —  
They **notify your application** when something happens, instead of your app constantly asking.

---

## 🧩 How epoll Works (Linux)

### Step-by-step:

1. **Create an epoll instance**
   - Think of this like opening a control center:
     ```c
     int epfd = epoll_create1(0);
     ```

2. **Register file descriptors (FDs)**
   - Tell epoll which files/sockets you want to monitor and what events you care about (e.g., read/write):
     ```c
     epoll_ctl(epfd, EPOLL_CTL_ADD, sockfd, &event);
     ```

3. **Wait for events**
   - The OS keeps watching in the background.
   - When something is ready, epoll **notifies you**:
     ```c
     epoll_wait(epfd, events, MAX_EVENTS, -1);
     ```

4. **Handle the ready event**
   - You get the event instantly and can handle it:
     ```c
     for (int i = 0; i < num_events; i++) {
         handle_event(events[i]);
     }
     ```

✅ **No polling**, **no wasted CPU cycles**, and **massive scalability**.

---



