# 代码随想录算法训练营第57天|prim算法精讲、kruskal算法精讲

## prim算法精讲

prim算法核心

1. 第一步，选距离生成树最近节点
2. 第二步，最近节点加入生成树
3. 第三步，更新非生成树节点到生成树的距离（即更新minDist数组）

**minDist数组用来记录每一个节点距离最小生成树的最近距离**。

```java
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int v = scanner.nextInt();
        int e = scanner.nextInt();
        
        // 初始化邻接矩阵，默认值为10001（题目描述val最大为10000）
        int[][] grid = new int[v + 1][v + 1];
        for (int i = 1; i <= v; i++) {
            for (int j = 1; j <= v; j++) {
                grid[i][j] = 10001;
            }
        }
        
        // 读取边信息
        while (e-- > 0) {
            int x = scanner.nextInt();
            int y = scanner.nextInt();
            int k = scanner.nextInt();
            grid[x][y] = k;
            grid[y][x] = k; // 无向图，双向赋值
        }
        
        // 所有节点到最小生成树的最小距离
        int[] minDist = new int[v + 1];
        for (int i = 1; i <= v; i++) {
            minDist[i] = 10001;
        }
        
        // 标记节点是否在最小生成树中
        boolean[] isInTree = new boolean[v + 1];
        
        // 从节点1开始构建最小生成树
        minDist[1] = 0;
        
        for (int i = 1; i < v; i++) {
            // 1. 选取距离生成树最近的节点
            int cur = -1;
            int minVal = Integer.MAX_VALUE;
            for (int j = 1; j <= v; j++) {
                if (!isInTree[j] && minDist[j] < minVal) {
                    minVal = minDist[j];
                    cur = j;
                }
            }
            
            // 2. 将该节点加入生成树
            isInTree[cur] = true;
            
            // 3. 更新非生成树节点到生成树的距离
            for (int j = 1; j <= v; j++) {
                if (!isInTree[j] && grid[cur][j] < minDist[j]) {
                    minDist[j] = grid[cur][j];
                }
            }
        }
        
        // 统计结果（从节点2开始，因为节点1没有对应的边）
        int result = 0;
        for (int i = 2; i <= v; i++) {
            result += minDist[i];
        }
        
        System.out.println(result);
    }
}
```



## kruskal算法精讲

kruscal的思路：

- 边的权值排序，因为要优先选最小的边加入到生成树里
- 遍历排序后的边
  - 如果边首尾的两个节点在同一个集合，说明如果连上这条边图中会出现环
  - 如果边首尾的两个节点不在同一个集合，加入到最小生成树，并把两个节点加入同一个集合

```java
import java.util.*;

class Edge {
    int l, r, val;
    Edge(int l, int r, int val) {
        this.l = l;
        this.r = r;
        this.val = val;
    }
}

public class Main {
    static int[] father;
    static int n = 10001; // 节点数量上限

    // 并查集初始化
    static void init() {
        father = new int[n];
        for (int i = 0; i < n; ++i) {
            father[i] = i;
        }
    }

    // 并查集的查找操作（带路径压缩）
    static int find(int u) {
        return u == father[u] ? u : (father[u] = find(father[u]));
    }

    // 并查集的合并操作
    static void join(int u, int v) {
        u = find(u);
        v = find(v);
        if (u == v) return;
        father[v] = u;
    }

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        int v = scanner.nextInt();
        int e = scanner.nextInt();
        List<Edge> edges = new ArrayList<>();
        
        // 读取边信息
        while (e-- > 0) {
            int v1 = scanner.nextInt();
            int v2 = scanner.nextInt();
            int val = scanner.nextInt();
            edges.add(new Edge(v1, v2, val));
        }

        // 按边的权值从小到大排序
        edges.sort((a, b) -> a.val - b.val);

        // 初始化并查集
        init();

        int result_val = 0;
        // 遍历所有边
        for (Edge edge : edges) {
            int x = find(edge.l);
            int y = find(edge.r);
            if (x != y) {
                result_val += edge.val;
                join(x, y);
            }
        }
        System.out.println(result_val);
    }
}
```

