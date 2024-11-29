# code-vita
![Screenshot_30-11-2024_02810_codevita tcsapps com](https://github.com/user-attachments/assets/f632723a-e417-4dac-bec0-f3895294f0c5)

![Screenshot_30-11-2024_0295_codevita tcsapps com](https://github.com/user-attachments/assets/333d0b88-cfef-4b5f-9743-74eac4728c35)

```python
class OfficeRostering:
    def __init__(self, N, M, friendships, K):
        self.N = N
        self.M = M
        self.friendships = friendships
        self.K = K
        self.attendance = [1] * N  # Start with everyone WFO
        self.total_attendance = 0
        self.total_days = 0
        self.friends = [[] for _ in range(N)]
        self.build_friendship_graph()

    def build_friendship_graph(self):
        for f1, f2 in self.friendships:
            self.friends[f1 - 1].append(f2 - 1)  # 0-based indexing
            self.friends[f2 - 1].append(f1 - 1)

    def simulate(self):
        while self.total_attendance < self.K:
            self.total_days += 1
            daily_attendance = sum(self.attendance)
            self.total_attendance += daily_attendance

            next_attendance = [0] * self.N
            for i in range(self.N):
                wfo_friends = sum(self.attendance[f] for f in self.friends[i])
                if self.attendance[i] == 1 and wfo_friends == 3:
                    next_attendance[i] = 1
                elif self.attendance[i] == 0 and wfo_friends < 3:
                    next_attendance[i] = 1

            self.attendance = next_attendance

        return self.total_days

if __name__ == "__main__":
    N, M = map(int, input().split())
    friendships = [tuple(map(int, input().split())) for _ in range(M)]
    K = int(input())
    rostering = OfficeRostering(N, M, friendships, K)
    result = rostering.simulate()
    print(result)
```

![Screenshot_30-11-2024_03059_codevita tcsapps com](https://github.com/user-attachments/assets/53f91d7f-b47a-468c-aa9a-a3f675da0d9a)

```python
#include <bits/stdc++.h>
using namespace std;

// Directions for movement: right, down, left, up
int dir[4][2] = {{0, 1}, {1, 0}, {0, -1}, {-1, 0}};

// Function to check if the cell is valid to move to
bool isValid(int x, int y, int n, const vector<vector<char>>& grid) {
    return x >= 0 && x < n && y >= 0 && y < n && grid[x][y] != 'M';
}

// Function to calculate minimum water required
int findMinWater(int n, const vector<vector<char>>& grid, pair<int, int> start, pair<int, int> end) {
    vector<vector<int>> water(n, vector<int>(n, INT_MAX)); // To store minimum water consumption
    queue<pair<int, int>> q; // Queue for BFS

    // Start from the starting position
    q.push(start);
    water[start.first][start.second] = 0;

    while (!q.empty()) {
        auto [x, y] = q.front();
        q.pop();

        // Explore all 4 possible directions
        for (int d = 0; d < 4; d++) {
            int nx = x + dir[d][0];
            int ny = y + dir[d][1];

            if (isValid(nx, ny, n, grid)) {
                int cost = (grid[x][y] == 'T' && grid[nx][ny] == 'T') ? 0 : 1;
                if (water[x][y] + cost < water[nx][ny]) {
                    water[nx][ny] = water[x][y] + cost;
                    q.push({nx, ny});
                }
            }
        }
    }

    return water[end.first][end.second];
}

int main() {
    int n;
    cin >> n;
    vector<vector<char>> grid(n, vector<char>(n));
    pair<int, int> start, end;

    // Input grid and identify start and end positions
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            cin >> grid[i][j];
            if (grid[i][j] == 'S') start = {i, j};
            if (grid[i][j] == 'E') end = {i, j};
        }
    }

    // Find the minimum water required
    int result = findMinWater(n, grid, start, end);

    // Print the result with proper formatting
    cout << result; // Avoid extra newline or spaces
    return 0;
}
```

![Screenshot_30-11-2024_03412_codevita tcsapps com](https://github.com/user-attachments/assets/a3b43664-8882-4018-afa0-2489e01a246b)

```python
#include <iostream>
#include <vector>
#include <queue>
#include <set>
#include <tuple>

using namespace std;

// Count the number of infected neighbors around a given cell
int countInfectedNeighbors(const vector<vector<int>>& grid, int x, int y) {
    int n = grid.size();
    int infected = 0;
    for (int dx = -1; dx <= 1; dx++) {
        for (int dy = -1; dy <= 1; dy++) {
            if (dx == 0 && dy == 0) continue; // Skip the cell itself
            int nx = x + dx, ny = y + dy;
            if (nx >= 0 && nx < n && ny >= 0 && ny < n && grid[nx][ny] == 1) {
                infected++;
            }
        }
    }
    return infected;
}

// Simulate the plague spread for the next day
vector<vector<int>> simulatePlague(const vector<vector<int>>& grid) {
    int n = grid.size();
    vector<vector<int>> nextGrid = grid;

    for (int x = 0; x < n; x++) {
        for (int y = 0; y < n; y++) {
            int infected = countInfectedNeighbors(grid, x, y);
            
            // Apply the rules for plague spread
            if (grid[x][y] == 0 && infected == 3) {
                nextGrid[x][y] = 1; // Uninfected becomes infected
            } else if (grid[x][y] == 1) {
                if (infected < 2 || infected > 3) {
                    nextGrid[x][y] = 0; // Infected becomes uninfected
                }
            }
        }
    }
    return nextGrid;
}

// Solve the problem using BFS
int solve(int n, vector<vector<char>>& initialGrid) {
    vector<vector<int>> grid(n, vector<int>(n));
    int startx = -1, starty = -1, destx = -1, desty = -1;

    // Initialize the grid and locate start ('s') and destination ('d')
    for (int x = 0; x < n; x++) {
        for (int y = 0; y < n; y++) {
            grid[x][y] = (initialGrid[x][y] == '1') ? 1 : 0;

            if (initialGrid[x][y] == 's') {
                startx = x;
                starty = y;
                grid[x][y] = 0; // Treat 's' as uninfected
            }
            if (initialGrid[x][y] == 'd') {
                destx = x;
                desty = y;
                grid[x][y] = 0; // Treat 'd' as uninfected
            }
        }
    }

    // BFS to find the shortest path
    queue<tuple<int, int, vector<vector<int>>, int>> q;
    set<tuple<int, int, vector<vector<int>>>> visited;

    // Start BFS with initial state
    q.push({startx, starty, grid, 0});

    while (!q.empty()) {
        auto [x, y, current_grid, days] = q.front();
        q.pop();

        // If destination is reached
        if (x == destx && y == desty) {
            return days;
        }

        auto state_key = make_tuple(x, y, current_grid);
        if (visited.count(state_key)) continue; // Avoid revisiting states
        visited.insert(state_key);

        // Simulate the next day's plague spread
        vector<vector<int>> next_grid = simulatePlague(current_grid);

        // Explore all possible moves (including staying in place)
        vector<pair<int, int>> moves = {{0, 0}, {-1, 0}, {1, 0}, {0, -1}, {0, 1}};
        for (auto [dx, dy] : moves) {
            int nx = x + dx, ny = y + dy;

            if (nx >= 0 && nx < n && ny >= 0 && ny < n && next_grid[nx][ny] == 0) {
                q.push({nx, ny, next_grid, days + 1});
            }
        }
    }

    return -1; // Should not reach here since the destination is always reachable
}

int main() {
    int n;
    cin >> n;

    vector<vector<char>> grid(n, vector<char>(n));
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            cin >> grid[i][j];
        }
    }

    // Print the result, adding 1 to the returned value for day indexing
    cout << solve(n, grid) + 1;
    return 0;
}
```

![Screenshot_30-11-2024_03519_codevita tcsapps com](https://github.com/user-attachments/assets/0f187d33-c1aa-4b46-bfd1-877a4479698d)

```python
#include <iostream>
#include <vector>
#include <queue>
#include <unordered_map>
#include <sstream>

using namespace std;

int calculate_string_value(const vector<pair<string, string>>& edges, const string& input_string) {
    // Step 1: Build the graph from the edges
    unordered_map<string, vector<string>> graph;
    unordered_map<string, int> in_degree;
    
    for (const auto& edge : edges) {
        const string& word1 = edge.first;
        const string& word2 = edge.second;
        
        graph[word1].push_back(word2);
        in_degree[word2]++;
        
        // Ensure all words are in in_degree map, even if their in-degree is 0
        if (in_degree.find(word1) == in_degree.end()) {
            in_degree[word1] = 0;
        }
    }
    
    // Step 2: Perform BFS to determine levels of each word
    unordered_map<string, int> levels;
    queue<string> q;
    
    // Enqueue all words with in-degree 0 (these are root nodes in the DAG)
    for (const auto& entry : in_degree) {
        if (entry.second == 0) {
            q.push(entry.first);
            levels[entry.first] = 1;  // Root node has level 1
        }
    }
    
    // BFS to assign levels
    while (!q.empty()) {
        string current_word = q.front();
        q.pop();
        int current_level = levels[current_word];
        
        for (const string& neighbor : graph[current_word]) {
            if (levels.find(neighbor) == levels.end()) {  // If the word is not yet assigned a level
                levels[neighbor] = current_level + 1;
                q.push(neighbor);
            }
        }
    }
    
    // Step 3: Process the input string to calculate the total value
    int total_value = 0;
    stringstream ss(input_string);
    string word;
    
    while (ss >> word) {
        if (levels.find(word) != levels.end()) {
            total_value += levels[word];
        } else {
            total_value += -1;  // Word not found in the hierarchy
        }
    }
    
    return total_value;
}

int main() {
    int N;
    cin >> N;
    
    vector<pair<string, string>> edges(N);
    
    // Read the edges
    for (int i = 0; i < N; ++i) {
        cin >> edges[i].first >> edges[i].second;
    }
    
    // Read the string for which we need to calculate the value
    string input_string;
    cin.ignore();  // To ignore the newline character after the integer input
    getline(cin, input_string);
    
    // Calculate the result
    int result = calculate_string_value(edges, input_string);
    
    // Output the result without any extra spaces or newlines
    cout << result << endl;
    
    return 0;
}
```

![Screenshot_30-11-2024_03612_codevita tcsapps com](https://github.com/user-attachments/assets/20123352-1446-4ca4-b675-44aa45b9b16b)

![Screenshot_30-11-2024_03655_codevita tcsapps com](https://github.com/user-attachments/assets/ac79fd97-fea7-481e-b40b-43bb27fbf771)

![Screenshot (371)](https://github.com/user-attachments/assets/4ca12893-3cea-4dd5-848c-d4026352a268)

