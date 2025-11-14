# Problem 2: Dynamic Changes and Resource Contention

## Question 1: Dynamic Changes and Re-Prioritization

Imagine a Level 4 goal (e.g., "Start a Retirement Fund") suddenly becomes Level 1 due to a deadline change. The STL `std::priority_queue` does not support an efficient 'update-priority' operation. Explain the two manual steps required to effectively change a goal's priority within the queue and maintain the correct heap property.

### Answer:

The two manual steps required are:

### Step 1: Remove the Element
**Find and remove** the goal with the old priority from the priority queue. Since `std::priority_queue` doesn't support direct removal of a specific element, you must:
- Extract all elements from the queue one by one
- Skip the element you want to update (don't add it to the temporary storage)
- Store all other elements in a temporary container

### Step 2: Reinsert with New Priority
**Modify and reinsert** the goal with its new priority:
- Update the priority level of the goal (from Level 4 to Level 1)
- Insert the modified goal back into the priority queue along with all other elements from the temporary container
- This automatically maintains the heap property as each insertion triggers the heap to reorganize

### Implementation Example:

```cpp
// Step 1: Remove the element
std::vector<Goal> temp;
Goal targetGoal;
bool found = false;

while (!goalQueue.empty()) {
    Goal current = goalQueue.top();
    goalQueue.pop();
    
    if (current.name == "Start a Retirement Fund" && !found) {
        targetGoal = current;  // Save it for modification
        found = true;
        // Don't add to temp - effectively removing it
    } else {
        temp.push_back(current);  // Keep other elements
    }
}

// Step 2: Reinsert with new priority
targetGoal.priority = 1;  // Change from Level 4 to Level 1
goalQueue.push(targetGoal);  // Insert with new priority

// Restore other elements
for (const auto& goal : temp) {
    goalQueue.push(goal);
}
```

---

## Question 2: Resource Contention

Goals with urgency Level 1 (Highest) often require significant resources (time, money, effort). If five goals are all assigned Level 1, how does the priority queue only address the problem of relative order (which is done first) but fail to address the problem of resource contention (how to execute them simultaneously with limited resources)?

### Answer:

The priority queue has fundamental limitations when dealing with resource contention:

### What Priority Queue DOES Address:
**Relative Order (Sequential Execution)**
- The priority queue determines *which task comes first* among the five Level 1 goals
- It establishes a sequential ordering based on secondary factors (e.g., insertion order, tie-breaking rules)
- When you call `pop()`, you get one goal at a time in priority order
- This works well for single-threaded, sequential execution

### What Priority Queue FAILS to Address:
**Resource Contention (Parallel Execution)**

1. **No Resource Awareness**: The priority queue has no concept of:
   - How much time each goal requires
   - How much money is available
   - What human effort/skills are needed
   - Whether resources are shared or independent

2. **No Capacity Planning**: It cannot determine:
   - Whether multiple Level 1 goals can be executed simultaneously
   - How to allocate limited resources among competing goals
   - Whether completing one goal frees up resources for others

3. **No Conflict Resolution**: It doesn't handle:
   - Two goals competing for the same $1000 budget
   - Two goals requiring the same person's time on the same day
   - Goals with resource dependencies or conflicts

### Example Scenario:

Consider five Level 1 goals:
- Goal A: Requires $500 and 10 hours
- Goal B: Requires $800 and 5 hours  
- Goal C: Requires $300 and 15 hours
- Goal D: Requires $400 and 8 hours
- Goal E: Requires $600 and 12 hours

**Available Resources**: $1000 budget, 20 hours per week

The priority queue will only tell you to do them in order: A → B → C → D → E

It **will not** tell you that:
- You could do A + C + D simultaneously (totaling $1200 - over budget!)
- B + C would fit the time budget (20 hours) but exceed money ($1100)
- A + D together would fit both constraints ($900, 18 hours)

### Solution:

Resource contention requires additional scheduling algorithms beyond priority queues:
- **Resource allocation algorithms** (bin packing, knapsack problems)
- **Scheduling systems** that consider resource constraints
- **Project management tools** that track resource availability
- **Constraint satisfaction** solvers that optimize resource usage

The priority queue is simply a data structure for ordering—it needs to be combined with resource management logic to handle real-world complexity.
