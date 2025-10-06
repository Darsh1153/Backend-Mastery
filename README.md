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






