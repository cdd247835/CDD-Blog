# 代码随想录算法训练营第58天|拓扑排序精讲、dijkstra（朴素版）精讲

## 拓扑排序精讲

拓扑排序的过程

1. 找到入度为0 的节点，加入结果集
2. 将该节点从图中移除

循环以上两步，直到 所有节点都在图中被移除了。

结果集的顺序，就是我们想要的拓扑排序顺序 （结果集里顺序可能不唯一）

```java
import java.util.*;

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int n = scanner.nextInt();
        int m = scanner.nextInt();
        
        int[] inDegree = new int[n]; // 记录每个文件的入度
        Map<Integer, List<Integer>> graph = new HashMap<>(); // 记录文件依赖关系
        
        for (int i = 0; i < m; i++) {
            int s = scanner.nextInt();
            int t = scanner.nextInt();
            inDegree[t]++; // t 的入度加一
            graph.putIfAbsent(s, new ArrayList<>());
            graph.get(s).add(t); // 记录 s 指向哪些文件
        }
        
        Queue<Integer> queue = new LinkedList<>();
        List<Integer> result = new ArrayList<>();
        
        for (int i = 0; i < n; i++) {
            if (inDegree[i] == 0) {
                queue.add(i);
            }
        }
        
        while (!queue.isEmpty()) {
            int cur = queue.poll(); // 当前选中的文件
            result.add(cur);
            
            if (graph.containsKey(cur)) {
                for (int next : graph.get(cur)) {
                    inDegree[next]--; // cur 指向的文件入度 -1
                    if (inDegree[next] == 0) {
                        queue.add(next);
                    }
                }
            }
        }
        
        if (result.size() == n) {
            for (int i = 0; i < n - 1; i++) {
                System.out.print(result.get(i) + " ");
            }
            System.out.println(result.get(n - 1));
        } else {
            System.out.println(-1);
        }
        
        scanner.close();
    }
}
```

## dijkstra（朴素版）精讲

dijkstra算法：在有权图（权值非负数）中求从起点到其他节点的最短路径算法。

需要注意两点：

- dijkstra 算法可以同时求 起点到所有节点的最短路径
- 权值不能为负数

dijkstra 算法 同样是贪心的思路，不断寻找距离 源点最近的没有访问过的节点。

**dijkstra三部曲**：

1. 第一步，选源点到哪个节点近且该节点未被访问过
2. 第二步，该最近节点被标记访问过
3. 第三步，更新非访问节点到源点的距离（即更新minDist数组）

在dijkstra算法中，同样有一个数组很重要，起名为：minDist。

**minDist数组 用来记录 每一个节点距离源点的最小距离**。

```java
import java.util.*;

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int n = scanner.nextInt();
        int m = scanner.nextInt();

        int[][] grid = new int[n + 1][n + 1];
        for (int i = 0; i <= n; i++) {
            Arrays.fill(grid[i], Integer.MAX_VALUE);
        }

        for (int i = 0; i < m; i++) {
            int p1 = scanner.nextInt();
            int p2 = scanner.nextInt();
            int val = scanner.nextInt();
            grid[p1][p2] = val;
        }

        int start = 1;
        int end = n;

        int[] minDist = new int[n + 1];
        Arrays.fill(minDist, Integer.MAX_VALUE);
        boolean[] visited = new boolean[n + 1];

        minDist[start] = 0;
        for (int i = 1; i <= n; i++) {
            int minVal = Integer.MAX_VALUE;
            int cur = 1;

            for (int v = 1; v <= n; v++) {
                if (!visited[v] && minDist[v] < minVal) {
                    minVal = minDist[v];
                    cur = v;
                }
            }

            visited[cur] = true;

            for (int v = 1; v <= n; v++) {
                if (!visited[v] && grid[cur][v] != Integer.MAX_VALUE 
                    && minDist[cur] != Integer.MAX_VALUE 
                    && minDist[cur] + grid[cur][v] < minDist[v]) {
                    minDist[v] = minDist[cur] + grid[cur][v];
                }
            }

            // Print logs:
            System.out.println("select:" + cur);
            for (int v = 1; v <= n; v++) {
                System.out.print(v + ":" + minDist[v] + " ");
            }
            System.out.println("\n");
        }

        if (minDist[end] == Integer.MAX_VALUE) {
            System.out.println(-1);
        } else {
            System.out.println(minDist[end]);
        }
    }
}
```

```txt
select:1
1:0 2:1 3:4 4:2147483647 5:2147483647 6:2147483647 7:2147483647 

select:2
1:0 2:1 3:3 4:6 5:2147483647 6:5 7:2147483647 

select:3
1:0 2:1 3:3 4:5 5:2147483647 6:5 7:2147483647 

select:4
1:0 2:1 3:3 4:5 5:8 6:5 7:2147483647 

select:6
1:0 2:1 3:3 4:5 5:8 6:5 7:14 

select:5
1:0 2:1 3:3 4:5 5:8 6:5 7:12 

select:7
1:0 2:1 3:3 4:5 5:8 6:5 7:12 

12
```

