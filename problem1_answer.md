# Problem 1: Updating Priority in std::priority_queue

## Question
Real-World Limitation: This priority queue is static; once the tasks are input, their order only changes upon extraction. In a real-world career preparation plan, the time remaining until a deadline often increases a task's priority (e.g., an exam 1 week away). Given that std::priority_queue does not support efficient updating of an existing element's priority, describe a simple but inefficient method a programmer would have to use in C++ to increase the score of an existing task and re-establish the correct execution order.

## Answer

Since `std::priority_queue` does not provide a direct method to update the priority of an existing element, a simple but inefficient approach would be:

### Method: Extract-Modify-Reinsert

1. **Extract all elements** from the priority queue into a temporary container (such as a `std::vector`)
2. **Locate and modify** the specific task whose priority needs to be updated
3. **Rebuild the priority queue** by inserting all elements back into a new or cleared priority queue

### Implementation Example:

```cpp
// Step 1: Extract all elements into a vector
std::vector<Task> temp;
while (!pq.empty()) {
    temp.push_back(pq.top());
    pq.pop();
}

// Step 2: Find and modify the target task
for (auto& task : temp) {
    if (task.id == targetTaskId) {
        task.priority += priorityIncrease;
        break;
    }
}

// Step 3: Rebuild the priority queue
for (const auto& task : temp) {
    pq.push(task);
}
```

### Why This is Inefficient:

- **Time Complexity**: O(n log n) where n is the number of elements in the queue
  - Extracting all elements: O(n log n)
  - Finding the element: O(n)
  - Reinserting all elements: O(n log n)
  
- **Space Complexity**: O(n) for the temporary container

- **Better Alternative**: In production code, programmers often use alternative data structures like `std::set` or `std::map` with custom comparators, or implement their own heap with index tracking to support O(log n) priority updates.
