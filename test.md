
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.StringTokenizer;
import java.util.Arrays;

public class Solution {

    static class Edge {
        int u;
        int v;
        long weight;

        Edge(int u, int v, long weight) {
            this.u = u;
            this.v = v;
            this.weight = weight;
        }
    }

    static class DSU {
        int[] parent;
        int[] size;
        boolean[] hasOn;

        DSU(int[] states) {
            int n = states.length;
            parent = new int[n];
            size = new int[n];
            hasOn = new boolean[n];

            for (int i = 0; i < n; i++) {
                parent[i] = i;
                size[i] = 1;
                hasOn[i] = states[i] == 1;
            }
        }

        int find(int x) {
            if (parent[x] != x) {
                parent[x] = find(parent[x]);
            }
            return parent[x];
        }

        // 合并两个连通分量
        void union(int a, int b) {
            int ra = find(a);
            int rb = find(b);

            if (ra == rb) {
                return;
            }

            if (size[ra] < size[rb]) {
                int temp = ra;
                ra = rb;
                rb = temp;
            }

            parent[rb] = ra;
            size[ra] += size[rb];
            hasOn[ra] = hasOn[ra] || hasOn[rb];
        }
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(
                new InputStreamReader(System.in));

        int n = Integer.parseInt(br.readLine().trim());

        int[] states = new int[n];
        StringTokenizer st = new StringTokenizer(br.readLine());

        for (int i = 0; i < n; i++) {
            states[i] = Integer.parseInt(st.nextToken());
        }

        int m = Integer.parseInt(br.readLine().trim());

        long[] dist = new long[m];
        st = new StringTokenizer(br.readLine());

        for (int i = 0; i < m; i++) {
            dist[i] = Long.parseLong(st.nextToken());
        }

        // 每台相邻设备之间建立一条候选电缆边
        Edge[] edges = new Edge[n - 1];

        for (int i = 0; i < n - 1; i++) {
            edges[i] = new Edge(
                    i,
                    i + 1,
                    dist[i + 1] - dist[i]
            );
        }

        // 优先考虑电缆长度较短的连接
        Arrays.sort(edges,
                (a, b) -> Long.compare(a.weight, b.weight));

        DSU dsu = new DSU(states);
        long answer = 0;

        for (Edge edge : edges) {
            int u = dsu.find(edge.u);
            int v = dsu.find(edge.v);

            if (u == v) {
                continue;
            }

            // 两个连通分量都已经有通电设备，
            // 无须再连接它们
            if (dsu.hasOn[u] && dsu.hasOn[v]) {
                continue;
            }

            answer += edge.weight;
            dsu.union(u, v);
        }

        System.out.println(answer);
    }
}
