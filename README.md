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

## 2. Write a program to implement round robin algorithm with time quantam = 3 secs

```cpp
#include <iostream>
#include <queue>
using namespace std;

struct Process {
    int pid;
    int at;     
    int bt;     
    int rt;     
    int ct;     
    int tat;    
    int wt;     
};

int main() {
    int n;
    int tq = 3;

    cout << "Enter number of processes: ";
    cin >> n;

    Process p[n];

    for (int i = 0; i < n; i++) {
        p[i].pid = i + 1;

        cout << "\nProcess P" << i + 1 << endl;

        cout << "Enter Arrival Time: ";
        cin >> p[i].at;

        cout << "Enter Burst Time: ";
        cin >> p[i].bt;

        p[i].rt = p[i].bt;
    }

    queue<int> q;
    bool added[n] = {false};

    int currentTime = 0;
    int completed = 0;

    while (completed < n) {

        for (int i = 0; i < n; i++) {
            if (!added[i] && p[i].at <= currentTime) {
                q.push(i);
                added[i] = true;
            }
        }

        if (q.empty()) {
            currentTime++;

            continue;
        }

        int i = q.front();
        q.pop();

        int executionTime;

        if (p[i].rt > tq)
            executionTime = tq;
        else
            executionTime = p[i].rt;

        currentTime += executionTime;
        p[i].rt -= executionTime;

        for (int j = 0; j < n; j++) {
            if (!added[j] && p[j].at <= currentTime) {
                q.push(j);
                added[j] = true;
            }
        }

        if (p[i].rt > 0) {
            q.push(i);
        }
        else {
            p[i].ct = currentTime;

            p[i].tat = p[i].ct - p[i].at;

            p[i].wt = p[i].tat - p[i].bt;

            completed++;
        }
    }

    cout << "\n\nRound Robin Scheduling";
    cout << "\nTime Quantum = " << tq << " seconds\n\n";

    cout << "---------------------------------------------------\n";
    cout << "Process\tAT\tBT\tCT\tTAT\tWT\n";
    cout << "---------------------------------------------------\n";

    float totalWT = 0;
    float totalTAT = 0;

    for (int i = 0; i < n; i++) {

        cout << "P" << p[i].pid << "\t"
             << p[i].at << "\t"
             << p[i].bt << "\t"
             << p[i].ct << "\t"
             << p[i].tat << "\t"
             << p[i].wt << endl;

        totalWT += p[i].wt;
        totalTAT += p[i].tat;
    }

    cout << "---------------------------------------------------\n";

    cout << "\nAverage Waiting Time = "
         << totalWT / n << " seconds";

    cout << "\nAverage Turnaround Time = "
         << totalTAT / n << " seconds\n";

    return 0;
}
```

---


```output
Enter number of processes: 3

Process P1
Enter Arrival Time: 0
Enter Burst Time: 5

Process P2
Enter Arrival Time: 1
Enter Burst Time: 4

Process P3
Enter Arrival Time: 2
Enter Burst Time: 2


Round Robin Scheduling
Time Quantum = 3 seconds

---------------------------------------------------
Process	AT	BT	CT	TAT	WT
---------------------------------------------------
P1	0	5	10	10	5
P2	1	4	11	10	6
P3	2	2	8	6	4
---------------------------------------------------

Average Waiting Time = 5 seconds
Average Turnaround Time = 8.66667 seconds
```
