#include <iostream>
#include <vector>

using namespace std;

class DeadlockDetection {
private:
    int numProcesses, numResources;
    vector<vector<int>> allocation;
    vector<vector<int>> maxNeed;
    vector<int> available;

public:
    DeadlockDetection(int processes, int resources, vector<vector<int>> alloc, vector<vector<int>> maxN, vector<int> avail)
        : numProcesses(processes), numResources(resources), allocation(alloc), maxNeed(maxN), available(avail) {}

    bool detectDeadlock() {
        vector<int> work = available;
        vector<bool> finish(numProcesses, false);
        vector<int> safeSequence;
        
        int count = 0;
        while (count < numProcesses) {
            bool found = false;
            for (int i = 0; i < numProcesses; i++) {
                if (!finish[i]) {
                    bool canExecute = true;
                    for (int j = 0; j < numResources; j++) {
                        if (maxNeed[i][j] - allocation[i][j] > work[j]) {
                            canExecute = false;
                            break;
                        }
                    }
                    if (canExecute) {
                        for (int j = 0; j < numResources; j++) {
                            work[j] += allocation[i][j];
                        }
                        safeSequence.push_back(i);
                        finish[i] = true;
                        found = true;
                        count++;
                    }
                }
            }
            if (!found) {
                cout << "Deadlock detected!" << endl;
                return true;
            }
        }
        
        cout << "No Deadlock detected. Safe sequence: ";
        for (int i : safeSequence) {
            cout << i << " ";
        }
        cout << endl;
        return false;
    }
};

int main() {
    int processes = 5, resources = 3;
    vector<vector<int>> allocation = {{0, 1, 0}, {2, 0, 0}, {3, 0, 2}, {2, 1, 1}, {0, 0, 2}};
    vector<vector<int>> maxNeed = {{7, 5, 3}, {3, 2, 2}, {9, 0, 2}, {2, 2, 2}, {4, 3, 3}};
    vector<int> available = {3, 3, 2};

    DeadlockDetection dd(processes, resources, allocation, maxNeed, available);
    dd.detectDeadlock();
    
    return 0;
}
