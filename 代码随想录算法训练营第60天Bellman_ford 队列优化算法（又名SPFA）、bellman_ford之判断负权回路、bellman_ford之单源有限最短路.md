

# 代码随想录算法训练营第60天|Bellman_ford 队列优化算法（又名SPFA）、bellman_ford之判断负权回路、bellman_ford之单源有限最短路

##  Bellman_ford 队列优化算法（又名SPFA）

```java
import java.util.*;

class Edge {
    int to;     // 目标节点
    int val;    // 边权重
    
    public Edge(int t, int w) {
        to = t;
        val = w;
    }
}

public class SPFAAlgorithm {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int n = scanner.nextInt();  // 节点数量
        int m = scanner.nextInt();  // 边数量
        
        // 邻接表表示图
        List<List<Edge>> grid = new ArrayList<>();
        for (int i = 0; i <= n; i++) {
            grid.add(new LinkedList<>());
        }
        
        boolean[] isInQueue = new boolean[n + 1]; // 优化：标记节点是否在队列中
        
        // 读取所有边
        for (int i = 0; i < m; i++) {
            int p1 = scanner.nextInt();
            int p2 = scanner.nextInt();
            int val = scanner.nextInt();
            grid.get(p1).add(new Edge(p2, val));
        }
        
        int start = 1;  // 起点
        int end = n;     // 终点
        
        int[] minDist = new int[n + 1];
        Arrays.fill(minDist, Integer.MAX_VALUE); // 初始化为最大值
        minDist[start] = 0;  // 起点到自己的距离为0
        
        Queue<Integer> queue = new LinkedList<>();
        queue.add(start);
        isInQueue[start] = true;
        
        // SPFA算法主循环
        while (!queue.isEmpty()) {
            int node = queue.poll();
            isInQueue[node] = false; // 节点出队列，标记为不在队列中
            
            // 遍历当前节点的所有邻接边
            for (Edge edge : grid.get(node)) {
                int from = node;
                int to = edge.to;
                int value = edge.val;
                
                // 松弛操作
                if (minDist[to] > minDist[from] + value) {
                    minDist[to] = minDist[from] + value;
                    // 如果目标节点不在队列中，则加入队列
                    if (!isInQueue[to]) {
                        queue.add(to);
                        isInQueue[to] = true;
                    }
                }
            }
        }
        
        // 输出结果
        if (minDist[end] == Integer.MAX_VALUE) {
            System.out.println("unconnected"); // 不可达
        } else {
            System.out.println(minDist[end]); // 最短路径值
        }
    }
}
```

## bellman_ford之判断负权回路

```java
import java.util.*;

public class BellmanFord {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int n = scanner.nextInt(); // 节点数量
        int m = scanner.nextInt(); // 边数量
        
        // 存储所有边的列表，每个边用长度为3的数组表示[起点, 终点, 权值]
        List<int[]> edges = new ArrayList<>();
        
        // 读取所有边
        for (int i = 0; i < m; i++) {
            int p1 = scanner.nextInt();
            int p2 = scanner.nextInt();
            int val = scanner.nextInt();
            edges.add(new int[]{p1, p2, val});
        }
        
        int start = 1; // 起点
        int end = n;    // 终点
        
        int[] minDist = new int[n + 1];
        Arrays.fill(minDist, Integer.MAX_VALUE); // 初始化为最大值
        minDist[start] = 0; // 起点到自己的距离为0
        
        boolean hasNegativeCycle = false; // 标记是否存在负权环
        
        // Bellman-Ford算法主循环
        for (int i = 1; i <= n; i++) {
            for (int[] edge : edges) {
                int from = edge[0];
                int to = edge[1];
                int price = edge[2];
                
                if (i < n) { // 前n-1次正常松弛
                    if (minDist[from] != Integer.MAX_VALUE && 
                        minDist[to] > minDist[from] + price) {
                        minDist[to] = minDist[from] + price;
                    }
                } else { // 第n次检查负权环
                    if (minDist[from] != Integer.MAX_VALUE && 
                        minDist[to] > minDist[from] + price) {
                        hasNegativeCycle = true;
                    }
                }
            }
        }
        
        // 输出结果
        if (hasNegativeCycle) {
            System.out.println("circle"); // 存在负权环
        } else if (minDist[end] == Integer.MAX_VALUE) {
            System.out.println("unconnected"); // 不可达
        } else {
            System.out.println(minDist[end]); // 最短路径值
        }
    }
}
```

```java
import java.util.*;

class Edge {
    int to;     // 目标节点
    int val;    // 边权重
    
    public Edge(int t, int w) {
        to = t;
        val = w;
    }
}

public class SPFAWithNegativeCycle {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int n = scanner.nextInt();  // 节点数量
        int m = scanner.nextInt();  // 边数量
        
        // 邻接表表示图
        List<List<Edge>> grid = new ArrayList<>();
        for (int i = 0; i <= n; i++) {
            grid.add(new LinkedList<>());
        }
        
        // 读取所有边
        for (int i = 0; i < m; i++) {
            int p1 = scanner.nextInt();
            int p2 = scanner.nextInt();
            int val = scanner.nextInt();
            grid.get(p1).add(new Edge(p2, val));
        }
        
        int start = 1;  // 起点
        int end = n;    // 终点
        
        int[] minDist = new int[n + 1];
        Arrays.fill(minDist, Integer.MAX_VALUE);
        minDist[start] = 0;
        
        Queue<Integer> queue = new LinkedList<>();
        queue.add(start);
        
        int[] count = new int[n + 1]; // 记录节点入队次数
        count[start]++;
        
        boolean hasNegativeCycle = false;
        
        // SPFA算法主循环
        while (!queue.isEmpty() && !hasNegativeCycle) {
            int node = queue.poll();
            
            for (Edge edge : grid.get(node)) {
                int from = node;
                int to = edge.to;
                int value = edge.val;
                
                // 松弛操作
                if (minDist[to] > minDist[from] + value) {
                    minDist[to] = minDist[from] + value;
                    queue.add(to);
                    count[to]++;
                    
                    // 如果节点入队次数超过n次，说明存在负权环
                    if (count[to] == n) {
                        hasNegativeCycle = true;
                        queue.clear(); // 清空队列以立即退出循环
                        break;
                    }
                }
            }
        }
        
        // 输出结果
        if (hasNegativeCycle) {
            System.out.println("circle");
        } else if (minDist[end] == Integer.MAX_VALUE) {
            System.out.println("unconnected");
        } else {
            System.out.println(minDist[end]);
        }
    }
}
```

## bellman_ford之单源有限最短路

```java
import java.util.*;

public class LimitedStepShortestPath {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int n = scanner.nextInt(); // 节点数量
        int m = scanner.nextInt(); // 边数量
        
        // 存储所有边的列表，每个边用长度为3的数组表示[起点, 终点, 权值]
        List<int[]> edges = new ArrayList<>();
        
        // 读取所有边
        for (int i = 0; i < m; i++) {
            int p1 = scanner.nextInt();
            int p2 = scanner.nextInt();
            int val = scanner.nextInt();
            edges.add(new int[]{p1, p2, val});
        }
        
        int src = scanner.nextInt(); // 起点
        int dst = scanner.nextInt(); // 终点
        int k = scanner.nextInt();  // 最大中转次数
        
        int[] minDist = new int[n + 1];
        Arrays.fill(minDist, Integer.MAX_VALUE);
        minDist[src] = 0;
        //使用 minDistCopy 的主要原因是为了保证每次迭代都基于前一次完整迭代的结果，而不是在单次迭代中就混合使用已经更新和未更新的值。
        int[] minDistCopy = new int[n + 1]; // 记录上一次迭代的结果
        
        // 最多进行k+1次松弛（因为k次中转意味着k+1条边）
        for (int i = 1; i <= k + 1; i++) {
            System.arraycopy(minDist, 0, minDistCopy, 0, n + 1); // 复制当前状态
            
            for (int[] edge : edges) {
                int from = edge[0];
                int to = edge[1];
                int price = edge[2];
                
                // 使用minDistCopy的值来更新minDist
                if (minDistCopy[from] != Integer.MAX_VALUE && 
                    minDist[to] > minDistCopy[from] + price) {
                    minDist[to] = minDistCopy[from] + price;
                }
            }
        }
        
        // 输出结果
        if (minDist[dst] == Integer.MAX_VALUE) {
            System.out.println("unreachable");
        } else {
            System.out.println(minDist[dst]);
        }
    }
}
```

