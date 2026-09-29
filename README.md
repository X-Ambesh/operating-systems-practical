 ## 1. Write a program to implement non preemptive priority based scheduling

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

struct Process {
    int pid;
    int arrivalTime;
    int burstTime;
    int priority;
    int completionTime;
    int turnaroundTime;
    int waitingTime;
};

int main() {
    int n;

    cout << "Enter number of processes: ";
    cin >> n;

    vector<Process> p(n);

    for (int i = 0; i < n; i++) {
        p[i].pid = i + 1;

        cout << "\nEnter details for Process " << p[i].pid << ":\n";
        cout << "Arrival Time: ";
        cin >> p[i].arrivalTime;

        cout << "Burst Time: ";
        cin >> p[i].burstTime;

        cout << "Priority (smaller number = higher priority): ";
        cin >> p[i].priority;
    }

    int currentTime = 0;
    int completed = 0;

    vector<bool> done(n, false);

    while (completed < n) {
        int selected = -1;

        for (int i = 0; i < n; i++) {
            if (!done[i] && p[i].arrivalTime <= currentTime) {
                if (selected == -1 ||
                    p[i].priority < p[selected].priority ||
                    (p[i].priority == p[selected].priority &&
                     p[i].arrivalTime < p[selected].arrivalTime)) {
                    selected = i;
                }
            }
        }

        if (selected == -1) {
            currentTime++;
            continue;
        }

        currentTime += p[selected].burstTime;

        p[selected].completionTime = currentTime;

        p[selected].turnaroundTime =
            p[selected].completionTime - p[selected].arrivalTime;

        p[selected].waitingTime =
            p[selected].turnaroundTime - p[selected].burstTime;

        done[selected] = true;
        completed++;
    }

    cout << "\n\nProcess\tAT\tBT\tPriority\tCT\tTAT\tWT\n";
    cout << "----------------------------------------------------------\n";

    double totalWT = 0, totalTAT = 0;

    for (int i = 0; i < n; i++) {
        cout << "P" << p[i].pid << "\t"
             << p[i].arrivalTime << "\t"
             << p[i].burstTime << "\t"
             << p[i].priority << "\t\t"
             << p[i].completionTime << "\t"
             << p[i].turnaroundTime << "\t"
             << p[i].waitingTime << "\n";

        totalWT += p[i].waitingTime;
        totalTAT += p[i].turnaroundTime;
    }

    cout << "\nAverage Waiting Time = " << totalWT / n;
    cout << "\nAverage Turnaround Time = " << totalTAT / n << endl;

    return 0;
}
```

output

```output

Enter number of processes: 4

Enter details for Process 1:
Arrival Time: 0
Burst Time: 8
Priority (smaller number = higher priority): 2

Enter details for Process 2:
Arrival Time: 1
Burst Time: 4
Priority (smaller number = higher priority): 1

Enter details for Process 3:
Arrival Time: 2
Burst Time: 2
Priority (smaller number = higher priority): 3

Enter details for Process 4:
Arrival Time: 3
Burst Time: 1
Priority (smaller number = higher priority): 2


Process	AT	BT	Priority	CT	TAT	WT
----------------------------------------------------------
P1	0	8	2		8	8	0
P2	1	4	1		12	11	7
P3	2	2	3		15	13	11
P4	3	1	2		13	10	9

Average Waiting Time = 6.75
Average Turnaround Time = 10.5
```
