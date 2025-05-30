# Computational Geometry: Key Algorithms and Implementations

Computational geometry is a branch of computer science that studies algorithms for solving geometric problems. Here I'll explain four fundamental computational geometry problems and provide C implementations for each.

## 1. Determining Whether Any Pair of Segments Intersects

### Explanation
The segment intersection problem involves determining if any two segments in a given set intersect each other. The efficient solution uses a sweep line algorithm that runs in O(n log n) time.

**Key concepts:**
- **Orientation test:** Determines the relative orientation of three points (clockwise, counter-clockwise, or colinear)
- **Sweep line:** An imaginary vertical line that moves across the plane to process geometric objects

### C Implementation
```c
#include <stdio.h>
#include <stdbool.h>

typedef struct {
    double x, y;
} Point;

typedef struct {
    Point p, q;
} Segment;

// Find orientation of ordered triplet (p, q, r)
int orientation(Point p, Point q, Point r) {
    double val = (q.y - p.y) * (r.x - q.x) - (q.x - p.x) * (r.y - q.y);
    
    if (val == 0) return 0;  // colinear
    return (val > 0) ? 1 : 2; // clockwise or counter-clockwise
}

// Check if point q lies on segment pr
bool onSegment(Point p, Point q, Point r) {
    if (q.x <= fmax(p.x, r.x) && q.x >= fmin(p.x, r.x) &&
        q.y <= fmax(p.y, r.y) && q.y >= fmin(p.y, r.y))
        return true;
    return false;
}

// Check if segments s1 and s2 intersect
bool segmentsIntersect(Segment s1, Segment s2) {
    Point p1 = s1.p, q1 = s1.q, p2 = s2.p, q2 = s2.q;
    
    int o1 = orientation(p1, q1, p2);
    int o2 = orientation(p1, q1, q2);
    int o3 = orientation(p2, q2, p1);
    int o4 = orientation(p2, q2, q1);
    
    // General case
    if (o1 != o2 && o3 != o4)
        return true;
    
    // Special cases (colinear)
    if (o1 == 0 && onSegment(p1, p2, q1)) return true;
    if (o2 == 0 && onSegment(p1, q2, q1)) return true;
    if (o3 == 0 && onSegment(p2, p1, q2)) return true;
    if (o4 == 0 && onSegment(p2, q1, q2)) return true;
    
    return false;
}

// Check if any pair of segments in the array intersect
bool anySegmentIntersect(Segment segments[], int n) {
    for (int i = 0; i < n; i++) {
        for (int j = i + 1; j < n; j++) {
            if (segmentsIntersect(segments[i], segments[j])) {
                return true;
            }
        }
    }
    return false;
}

int main() {
    Segment segments[] = {
        {{1, 1}, {10, 1}},
        {{1, 2}, {10, 2}},
        {{5, 0}, {5, 10}}
    };
    int n = sizeof(segments)/sizeof(segments[0]);
    
    if (anySegmentIntersect(segments, n))
        printf("Segments intersect\n");
    else
        printf("No segments intersect\n");
    
    return 0;
}
```

## 2. Finding the Convex Hull

### Explanation
The convex hull of a set of points is the smallest convex polygon that contains all the points. Graham's scan is a common O(n log n) algorithm.

**Steps:**
1. Find the point with the lowest y-coordinate (and leftmost if tie)
2. Sort points by polar angle with respect to the pivot
3. Build the hull by keeping only points that make non-right turns

### C Implementation
```c
#include <stdio.h>
#include <stdlib.h>
#include <math.h>

typedef struct {
    int x, y;
} Point;

// Find the point with lowest y (and leftmost if tie)
Point pivot;

// Compute the cross product of vectors (p2-p1) and (p3-p1)
int cross(Point p1, Point p2, Point p3) {
    return (p2.x - p1.x)*(p3.y - p1.y) - (p2.y - p1.y)*(p3.x - p1.x);
}

// Compute the squared distance between two points
int distance(Point a, Point b) {
    return (a.x - b.x)*(a.x - b.x) + (a.y - b.y)*(a.y - b.y);
}

// Compare function for sorting points with respect to pivot
int compare(const void *vp1, const void *vp2) {
    Point *p1 = (Point *)vp1;
    Point *p2 = (Point *)vp2;
    
    int o = cross(pivot, *p1, *p2);
    if (o == 0)
        return (distance(pivot, *p1) < distance(pivot, *p2)) ? -1 : 1;
    return (o > 0) ? -1 : 1;
}

// Graham Scan algorithm
void convexHull(Point points[], int n, Point hull[], int *hull_size) {
    // Find the pivot point (lowest y, then leftmost)
    int min_y = points[0].y, min_idx = 0;
    for (int i = 1; i < n; i++) {
        int y = points[i].y;
        if ((y < min_y) || (y == min_y && points[i].x < points[min_idx].x)) {
            min_y = points[i].y;
            min_idx = i;
        }
    }
    
    // Swap pivot with first point
    Point temp = points[0];
    points[0] = points[min_idx];
    points[min_idx] = temp;
    pivot = points[0];
    
    // Sort the remaining points by polar angle
    qsort(&points[1], n-1, sizeof(Point), compare);
    
    // Initialize stack
    hull[0] = points[0];
    hull[1] = points[1];
    *hull_size = 2;
    
    // Process remaining points
    for (int i = 2; i < n; i++) {
        while (*hull_size >= 2 && cross(hull[*hull_size-2], hull[*hull_size-1], points[i]) <= 0) {
            (*hull_size)--;
        }
        hull[(*hull_size)++] = points[i];
    }
}

int main() {
    Point points[] = {{0, 3}, {1, 1}, {2, 2}, {4, 4}, 
                      {0, 0}, {1, 2}, {3, 1}, {3, 3}};
    int n = sizeof(points)/sizeof(points[0]);
    Point hull[n];
    int hull_size;
    
    convexHull(points, n, hull, &hull_size);
    
    printf("Convex Hull Points:\n");
    for (int i = 0; i < hull_size; i++) {
        printf("(%d, %d)\n", hull[i].x, hull[i].y);
    }
    
    return 0;
}
```

## 3. Finding the Closest Pair of Points

### Explanation
The closest pair problem involves finding the two points in a set that are closest to each other. The divide-and-conquer algorithm solves this in O(n log n) time.

**Approach:**
1. Sort points by x-coordinate
2. Divide the set into two equal halves
3. Recursively find closest pairs in each half
4. Check for closer pairs that cross the division line

### C Implementation
```c
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include <float.h>

typedef struct {
    double x, y;
} Point;

// Compare function for sorting by x-coordinate
int compareX(const void* a, const void* b) {
    Point *p1 = (Point *)a, *p2 = (Point *)b;
    return (p1->x - p2->x);
}

// Compare function for sorting by y-coordinate
int compareY(const void* a, const void* b) {
    Point *p1 = (Point *)a, *p2 = (Point *)b;
    return (p1->y - p2->y);
}

// Calculate distance between two points
double distance(Point a, Point b) {
    return sqrt((a.x-b.x)*(a.x-b.x) + (a.y-b.y)*(a.y-b.y));
}

// Brute force method for small number of points
double bruteForce(Point P[], int n, Point *p1, Point *p2) {
    double min = DBL_MAX;
    for (int i = 0; i < n; i++) {
        for (int j = i+1; j < n; j++) {
            double dist = distance(P[i], P[j]);
            if (dist < min) {
                min = dist;
                *p1 = P[i];
                *p2 = P[j];
            }
        }
    }
    return min;
}

// Find the smallest distance in strip of given size
double stripClosest(Point strip[], int size, double d, Point *p1, Point *p2) {
    double min = d;
    
    // Sort by y-coordinate
    qsort(strip, size, sizeof(Point), compareY);
    
    for (int i = 0; i < size; i++) {
        for (int j = i+1; j < size && (strip[j].y - strip[i].y) < min; j++) {
            double dist = distance(strip[i], strip[j]);
            if (dist < min) {
                min = dist;
                *p1 = strip[i];
                *p2 = strip[j];
            }
        }
    }
    return min;
}

// Recursive function to find smallest distance
double closestUtil(Point P[], int n, Point *p1, Point *p2) {
    if (n <= 3)
        return bruteForce(P, n, p1, p2);
    
    int mid = n/2;
    Point midPoint = P[mid];
    
    // Recursively find smallest distances in both subarrays
    Point pl1, pl2, pr1, pr2;
    double dl = closestUtil(P, mid, &pl1, &pl2);
    double dr = closestUtil(P + mid, n - mid, &pr1, &pr2);
    
    // Get the smaller of two distances
    double d;
    if (dl < dr) {
        d = dl;
        *p1 = pl1;
        *p2 = pl2;
    } else {
        d = dr;
        *p1 = pr1;
        *p2 = pr2;
    }
    
    // Build strip of points within d distance of middle line
    Point strip[n];
    int j = 0;
    for (int i = 0; i < n; i++) {
        if (fabs(P[i].x - midPoint.x) < d) {
            strip[j] = P[i];
            j++;
        }
    }
    
    // Find closest points in strip
    Point strip_p1, strip_p2;
    double strip_d = stripClosest(strip, j, d, &strip_p1, &strip_p2);
    
    if (strip_d < d) {
        *p1 = strip_p1;
        *p2 = strip_p2;
        return strip_d;
    } else {
        return d;
    }
}

// Main function to find closest pair
void closestPair(Point P[], int n, Point *p1, Point *p2) {
    // Sort points by x-coordinate
    qsort(P, n, sizeof(Point), compareX);
    
    // Use recursive function
    closestUtil(P, n, p1, p2);
}

int main() {
    Point points[] = {{2, 3}, {12, 30}, {40, 50}, {5, 1}, {12, 10}, {3, 4}};
    int n = sizeof(points)/sizeof(points[0]);
    Point p1, p2;
    
    closestPair(points, n, &p1, &p2);
    
    printf("The closest pair is (%g, %g) and (%g, %g) with distance %g\n",
           p1.x, p1.y, p2.x, p2.y, distance(p1, p2));
    
    return 0;
}
```

---
