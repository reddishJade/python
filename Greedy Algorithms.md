# Greedy Algorithms

## Main Idea

### Largest Number

- find max digit
- append it to the number
- remove it from the list of digits
- repeat while there are digits in the list

### Queue of Patients

#### Greedy Strategy

* make some greedy choice
* reduce to a smaller problem
* Iterate

````c
#include <stdio.h>
int MinTotalWaitingTime(int t[], int n)
{
	int waitingTime = 0;
	bool treated[n];
	for (int i = 0; i < n; ++i)
	{
		treated[i] = false;
	}
	for( int i = 0; i < n; i++)
	{
		int tmin = 99999;
		int minIndex = -1;
		for( int j = 0; j < n; j++)
		{
			if( treated[j] == false && t[j]<tmin )
			{
				tmin = t[j];
				minIndex = j;
			}
		}
		waitingTime += (n-i) * tmin;
		treated[minIndex] = true;
	}
	return waitingTime;
}
int main() {
    int n;
    printf("Enter the number of people: ");
    scanf("%d", &n);

    int t[n];
    printf("Enter the treatment times for each person:\n");
    for (int i = 0; i < n; i++) {
        scanf("%d", &t[i]);
    }

    int result = mintotalwaitingtime(t, n);
    printf("Minimum total waiting time: %d\n", result);

    return 0;
}
````



T:O(n^2)可以优化到O(nlogn) 先对事件排序，然后依次选择患者，即可避免依靠for选择时间最小的j患者

S:O(n)

### Main Ingredients

* greedy choice
* subproblem(a similar problem of smaller size)
* - make some first choice
  - solve a problem of the same kind but smaller
* safe choice(an optimal solution consistent with this first choice)(最优子结构)
* * not all first choices are safe
  * greedy choice are often unsafe

### General Strategy

- make a greedy choice
- prove that it is a safe choice
- reduce to a subproblem
- solve the subproblem

## Celebration Party

````c++
#include <stdio.h>
#include <stdlib.h>

typedef struct {
    int left;
    int right;
} Segment;

Segment* PointsCoverSorted(int x[], int n) {
    Segment* segments = NULL;
    int left = 0;

    if (n <= 0) {
        return segments;
    }

    segments = (Segment*)malloc(n * sizeof(Segment));
    if (segments == NULL) {
        // Handle memory allocation error
        exit(1);
    }

    while (left < n) {
        int l = x[left];
        int r = l + 2;

        segments[left].left = l;
        segments[left].right = r;
        left++;

        while (left < n && x[left] <= r) {
            left++;
        }
    }

    return segments;
}

int main() {
    // Define and initialize the 'x' array and 'n' as needed
    // ...

    int x[] = {1, 3, 4, 6, 8};
    int n = 5;

    Segment* result = PointsCoverSorted(x, n);

    for (int i = 0; i < n; i++) {
        printf("Segment %d: (%d, %d)\n", i, result[i].left, result[i].right);
    }

    free(result);  // Don't forget to free the memory when done

    return 0;
}
````



O(n+nlogn)

## Maximizing Loot

也就是**背包算法**

**性价比原则**

- while knapsack is not full
- choose item i with maximum vi/wi
- if item  fits into knapsack, take all of it
- otherwise take so much as to fill the knapsack
- return total value and amounts taken

````c
#include <stdio.h>

int bestItem(int w[], int v[], int n) {
    double maxValuePerWeight = 0.0;
    int bestItem = -1;

    for (int i = 0; i < n; i++) {
        if (w[i] > 0) {
            double valuePerWeight = (double)v[i] / w[i];
            if (valuePerWeight > maxValuePerWeight) {
                maxValuePerWeight = valuePerWeight;
                bestItem = i;
            }
        }
    }

    return bestItem;
}

int main() {
    int w[] = {10, 20, 30, 40};
    int v[] = {60, 100, 120, 160};
    int n = 4;

    int best = bestItem(w, v, n);

    if (best != -1) {
        printf("Best item is at index %d\n", best);
        printf("Weight: %d\n", w[best]);
        printf("Value: %d\n", v[best]);
    } else {
        printf("No valid item found.\n");
    }

    return 0;
}
````

````
Knapsack(W, W1, V1, …., Wn, Vn)
	amounts ← [0, 0,...,0]
	totalValue ← 0
	repeat n times:
		if W=0:
			return (totalValue, amounts)
		i← BestItem(W1, V1,..., Wn, Vn)
		a← min(wi, W)
		totalValue←totalValue+a
		Wi ← Wi - a
		amounts[i] ← amounts[i] +a W← W-a
	return (totalValue, amounts)
````

````#include <stdio.h>
#include <stdlib.h>

typedef struct {
    int weight;
    int value;
} Item;

// 按单位重量价值升序排序的比较函数
int compare(const void *a, const void *b) {
    double valuePerWeightA = (double)((Item *)a)->value / ((Item *)a)->weight;
    double valuePerWeightB = (double)((Item *)b)->value / ((Item *)b)->weight;
    return valuePerWeightA - valuePerWeightB;
}

// 返回最佳物品的索引
int bestItem(Item items[], int n, int W) {
    int bestItemIndex = -1;
    double maxValuePerWeight = 0.0;

    for (int i = 0; i < n; i++) {
        if (items[i].weight > 0 && (double)items[i].value / items[i].weight > maxValuePerWeight) {
            maxValuePerWeight = (double)items[i].value / items[i].weight;
            bestItemIndex = i;
        }
    }

    return bestItemIndex;
}

int* knapsack(Item items[], int n, int W) {
    int* amounts = (int*)malloc(n * sizeof(int));
    for (int i = 0; i < n; i++) {
        amounts[i] = 0;
    }
    int totalValue = 0;

    while (W > 0) {
        int bestIdx = bestItem(items, n, W);
        if (bestIdx == -1) {
            break;
        }

        int a = (items[bestIdx].weight < W) ? items[bestIdx].weight : W;
        totalValue += a;
        W -= a;
        amounts[bestIdx] += a;
    }

    int* result = (int*)malloc((n + 1) * sizeof(int));
    for (int i = 0; i < n; i++) {
        result[i] = amounts[i];
    }
    result[n] = totalValue;

    free(amounts);

    return result;
}

int main() {
    int n, W;
    printf("Enter the number of items: ");
    scanf("%d", &n);
    printf("Enter the knapsack capacity: ");
    scanf("%d", &W);

    Item items[n];
    printf("Enter item weight and value for each item:\n");
    for (int i = 0; i < n; i++) {
        scanf("%d %d", &items[i].weight, &items[i].value);
    }

    int* result = knapsack(items, n, W);

    printf("Amounts of each item in knapsack:\n");
    for (int i = 0; i < n; i++) {
        printf("Item %d: %d\n", i + 1, result[i]);
    }
    printf("Total value in knapsack: %d\n", result[n]);

    free(result);

    return 0;
}
````

````c
void knapSack(int W, int n, int w[], int v[])
{
    int amounts[n];
    for(int i = 0; i < n; i++)
        amounts[i] = 0;
    int totalValue = 0;
    for(int i = 0; i < n; i++)
    {
        if(W = 0)
            break;
        i = bestItem(w,v,n);
        int a = min(w[i],W);
        totalValue += a(v[i]/w[i]);
        w[i] -= a;
        amounts[i] += a;
        W -= a;
    }
}
````

````c
#include <stdio.h>
#include <stdlib.h>

typedef struct {
    int weight;
    int value;
} Item;

// 定义函数，返回最佳物品的索引
int bestItem(int w[], int v[], int n) {
    int bestItemIndex = -1;
    double maxValuePerWeight = 0.0;

    for (int i = 0; i < n; i++) {
        if (w[i] > 0 && (double)v[i] / w[i] > maxValuePerWeight) {
            maxValuePerWeight = (double)v[i] / w[i];
            bestItemIndex = i;
        }
    }

    return bestItemIndex;
}

void knapSack(int W, int n, int w[], int v[]) {
    int amounts[n];
    for (int i = 0; i < n; i++) {
        amounts[i] = 0;
    }
    int totalValue = 0;

    while (W > 0) {
        int i = bestItem(w, v, n);
        if (i == -1) {
            break;
        }

        int a = (w[i] < W) ? w[i] : W;
        totalValue += a * (double)v[i] / w[i];
        w[i] -= a;
        amounts[i] += a;
        W -= a;
    }

    // 输出结果
    printf("Amounts of each item in knapsack:\n");
    for (int i = 0; i < n; i++) {
        printf("Item %d: %d\n", i + 1, amounts[i]);
    }
    printf("Total value in knapsack: %d\n", totalValue);
}

int main() {
    int n, W;
    printf("Enter the number of items: ");
    scanf("%d", &n);
    printf("Enter the knapsack capacity: ");
    scanf("%d", &W);

    int w[n], v[n];
    printf("Enter item weight and value for each item:\n");
    for (int i = 0; i < n; i++) {
        scanf("%d %d", &w[i], &v[i]);
    }

    knapSack(W, n, w, v);

    return 0;
}
````

bestItem O(n)

knapSack O(n^2)

#### Improve

sort items by decreasing v/w

````
KnapsackFast(W, W1, V1,..., Wn, Vn) 			amounts←[0,0,...,0]
	totalValue←0
	for i from 1 to n:
		if W=0:
			return (totalValue,amounts) 			a←min(wi,W)
		totalValue←totalValue+avi/wi
		Wi←Wi-a
		amounts[i]← amounts[i]+a
		W←W-a
return (totalValue,amounts) 
````

````c
#include <stdlib.h>

// 定义物品结构
typedef struct {
    int weight;
    int value;
} Item;

// 定义函数，返回最佳物品的索引
int bestItem(int w[], int v[], int n, int W) {
    int bestItemIndex = -1;
    double maxValuePerWeight = 0.0;

    for (int i = 0; i < n; i++) {
        if (w[i] > 0 && (double)v[i] / w[i] > maxValuePerWeight) {
            maxValuePerWeight = (double)v[i] / w[i];
            bestItemIndex = i;
        }
    }

    return bestItemIndex;
}

void knapSackFast(int W, int n, int w[], int v[], int amounts[]) {
    int totalValue = 0;

    while (W > 0) {
        int i = bestItem(w, v, n, W);
        if (i == -1) {
            break;
        }

        int a = (w[i] < W) ? w[i] : W;
        totalValue += a * (double)v[i] / w[i];
        w[i] -= a;
        amounts[i] += a;
        W -= a;
    }

    // 输出结果
    printf("Amounts of each item in knapsack:\n");
    for (int i = 0; i < n; i++) {
        printf("Item %d: %d\n", i + 1, amounts[i]);
    }
    printf("Total value in knapsack: %d\n", totalValue);
}

int main() {
    int n, W;
    printf("Enter the number of items: ");
    scanf("%d", &n);
    printf("Enter the knapsack capacity: ");
    scanf("%d", &W);

    int w[n], v[n];
    int amounts[n]; // 数组来跟踪每个物品的数量
    for (int i = 0; i < n; i++) {
        amounts[i] = 0;
    }

    printf("Enter item weight and value for each item:\n");
    for (int i = 0; i < n; i++) {
        scanf("%d %d", &w[i], &v[i]);
    }

    knapSackFast(W, n, w, v, amounts);

    return 0;
}
````

T:O(n+nlogn)

## Review

- safe choice
- prove safety
- solve subproblem
- estimate running time

- which sort order
- can be faster after sorting