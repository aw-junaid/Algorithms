# Activity Selection Problem - Greedy Algorithm Approach

## Introduction to the Activity Selection Problem

The activity selection problem is a classic optimization problem where we're given a set of activities, each with a start and finish time, and we need to select the maximum number of activities that can be performed by a single person or machine, assuming they can't handle more than one activity at a time.

## Problem Definition

Given:
- A set S = {a₁, a₂, ..., aₙ} of n activities
- Each activity aᵢ has a start time sᵢ and finish time fᵢ (where 0 ≤ sᵢ < fᵢ < ∞)

Goal:
- Select the largest possible subset of mutually compatible activities (no two activities overlap in time)

## Greedy Approach

The greedy algorithm for activity selection works as follows:

1. Sort the activities by their finish times in increasing order
2. Select the first activity (the one with the earliest finish time)
3. For each remaining activity, if its start time is greater than or equal to the finish time of the previously selected activity, select it

This approach is optimal because:
- By selecting the activity that finishes earliest, we leave as much time as possible for remaining activities
- The problem exhibits optimal substructure (optimal solution contains optimal solutions to subproblems)
- The greedy choice property holds (locally optimal choices lead to globally optimal solution)

## Time Complexity

- Sorting: O(n log n)
- Selecting activities: O(n)
- Total: O(n log n)

## C Implementation

```c
#include <stdio.h>
#include <stdlib.h>

// Structure to represent an activity
typedef struct {
    int start;
    int finish;
    int index; // to keep track of original index if needed
} Activity;

// Comparison function for sorting activities by finish time
int compare(const void *a, const void *b) {
    Activity *act1 = (Activity *)a;
    Activity *act2 = (Activity *)b;
    return act1->finish - act2->finish;
}

// Function to select maximum number of activities
void selectActivities(Activity activities[], int n) {
    // Sort activities by finish time
    qsort(activities, n, sizeof(Activity), compare);
    
    printf("Selected Activities:\n");
    
    // The first activity is always selected
    int i = 0;
    printf("Activity %d (Start: %d, Finish: %d)\n", 
           activities[i].index, activities[i].start, activities[i].finish);
    
    // Consider rest of the activities
    for (int j = 1; j < n; j++) {
        // If this activity has start time greater than or equal to the finish
        // time of previously selected activity, then select it
        if (activities[j].start >= activities[i].finish) {
            printf("Activity %d (Start: %d, Finish: %d)\n", 
                   activities[j].index, activities[j].start, activities[j].finish);
            i = j;
        }
    }
}

int main() {
    // Example activities
    Activity activities[] = {
        {5, 9, 1},
        {1, 2, 2},
        {3, 4, 3},
        {0, 6, 4},
        {5, 7, 5},
        {8, 9, 6}
    };
    
    int n = sizeof(activities) / sizeof(activities[0]);
    
    selectActivities(activities, n);
    
    return 0;
}
```

## Explanation of the Implementation

1. **Activity Structure**: We define a structure to hold the start time, finish time, and index of each activity.

2. **Comparison Function**: This is used by qsort to sort the activities by their finish times in ascending order.

3. **selectActivities Function**:
   - First sorts the activities using qsort
   - Always selects the first activity (earliest finish time)
   - Then iterates through the remaining activities, selecting each one that doesn't conflict with the previously selected activity

4. **Main Function**:
   - Creates an array of activities with start and finish times
   - Calls the selection function to find and print the maximum set of non-conflicting activities

## Example Output

For the given activities:
- Activity 2 (1, 2)
- Activity 3 (3, 4)
- Activity 5 (5, 7)
- Activity 6 (8, 9)

The output would be:
```
Selected Activities:
Activity 2 (Start: 1, Finish: 2)
Activity 3 (Start: 3, Finish: 4)
Activity 5 (Start: 5, Finish: 7)
Activity 6 (Start: 8, Finish: 9)
```

This shows that a maximum of 4 activities can be performed without any time conflicts.
