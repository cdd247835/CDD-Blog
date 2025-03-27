# 代码随想录算法训练营第59天|dijkstra（堆优化版）精讲、Bellman_ford 算法精讲

# dijkstra（堆优化版）精讲

```java
import java.util.*;

public class DijkstraAlgorithm {
    // 定义一个内部类来表示带权重的边
    static class Edge {
        int to;     // 邻接顶点
        int val;    // 边的权重

        Edge(int t, int w) {
            to = t;
            val = w;
        }
    }

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int n = scanner.nextInt();
        int m = scanner.nextInt();

        // 使用邻接表表示图
        List<List<Edge>> grid = new ArrayList<>();
        for (int i = 0; i <= n; i++) {
            grid.add(new ArrayList<>());
        }

        for (int i = 0; i < m; i++) {
            int p1 = scanner.nextInt();
            int p2 = scanner.nextInt();
            int val = scanner.nextInt();
            // p1 指向 p2，权值为 val
            grid.get(p1).add(new Edge(p2, val));
        }

        int start = 1;  // 起点
        int end = n;    // 终点

        // 存储从源点到每个节点的最短距离
        int[] minDist = new int[n + 1];
        Arrays.fill(minDist, Integer.MAX_VALUE);

        // 记录顶点是否被访问过
        boolean[] visited = new boolean[n + 1];

        // 优先队列，按照距离从小到大排序
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> a[1] - b[1]);

        // 初始化队列，源点到源点的距离为0
        pq.offer(new int[]{start, 0});
        minDist[start] = 0;

        while (!pq.isEmpty()) {
            // 1. 取出当前距离源点最近的节点
            int[] cur = pq.poll();
            int currentNode = cur[0];
            int currentDist = cur[1];

            if (visited[currentNode]) continue;

            // 2. 标记该节点为已访问
            visited[currentNode] = true;

            // 3. 更新相邻节点到源点的距离
            for (Edge edge : grid.get(currentNode)) {
                int neighbor = edge.to;
                int weight = edge.val;
                if (!visited[neighbor] && minDist[currentNode] + weight < minDist[neighbor]) {
                    minDist[neighbor] = minDist[currentNode] + weight;
                    pq.offer(new int[]{neighbor, minDist[neighbor]});
                }
            }
        }

        if (minDist[end] == Integer.MAX_VALUE) {
            System.out.println(-1);
        } else {
            System.out.println(minDist[end]);
        }
    }
}
```

# Bellman_ford 算法精讲

 通过 A 到 B 这条边可以获得更短的到达B节点的路径，即如果 `minDist[B] > minDist[A] + value`，那么我们就更新 `minDist[B] = minDist[A] + value` ，**这个过程就叫做 “松弛**” 。

**对所有边松弛一次，相当于计算 起点到达 与起点一条边相连的节点 的最短距离**。

**对所有边松弛一次，相当于计算 起点到达 与起点一条边相连的节点 的最短距离**，这里 说的是 一条边相连的节点。

**需要对所有边松弛 n-1 次才能得到 起点（节点1） 到终点（节点6）的最短距离**

```java
import java.util.*;

public class BellmanFordAlgorithm {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int n = scanner.nextInt();
        int m = scanner.nextInt();

        // 使用列表存储所有边
        List<int[]> edges = new ArrayList<>();

        for (int i = 0; i < m; i++) {
            int p1 = scanner.nextInt();
            int p2 = scanner.nextInt();
            int val = scanner.nextInt();
            // p1 指向 p2，权值为 val
            edges.add(new int[]{p1, p2, val});
        }

        int start = 1;  // 起点
        int end = n;    // 终点

        // 存储从源点到每个节点的最短距离
        int[] minDist = new int[n + 1];
        Arrays.fill(minDist, Integer.MAX_VALUE);
        minDist[start] = 0;

        // Bellman-Ford算法核心部分
        for (int i = 1; i < n; i++) { // 对所有边松弛n-1次
            for (int[] edge : edges) { // 每次松弛所有边
                int from = edge[0];
                int to = edge[1];
                int price = edge[2];
                
                // 松弛操作
                // 防止从未计算过的节点出发（避免整数溢出）
                if (minDist[from] != Integer.MAX_VALUE 
                    && minDist[to] > minDist[from] + price) {
                    minDist[to] = minDist[from] + price;
                }
            }
        }

        if (minDist[end] == Integer.MAX_VALUE) {
            System.out.println("unconnected");
        } else {
            System.out.println(minDist[end]);
        }
    }
}
```

