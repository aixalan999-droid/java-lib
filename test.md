
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;
import java.util.StringTokenizer;
import java.util.Queue;
import java.util.LinkedList;

class Solution {

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(
                new InputStreamReader(System.in));

        int n = Integer.parseInt(br.readLine().trim());

        StringTokenizer st = new StringTokenizer(br.readLine());
        int q = Integer.parseInt(st.nextToken());
        int k = Integer.parseInt(st.nextToken());

        // 建立有向图：user1 -> user2
        List<Integer>[] graph = new ArrayList[n];

        for (int i = 0; i < n; i++) {
            graph[i] = new ArrayList<>();
        }

        for (int i = 0; i < q; i++) {
            st = new StringTokenizer(br.readLine());

            int user1 = Integer.parseInt(st.nextToken());
            int user2 = Integer.parseInt(st.nextToken());

            graph[user1].add(user2);
        }

        int bestUser = 0;
        int maxWalls = -1;

        // 分别计算每个用户能够传播到的用户数量
        for (int start = 0; start < n; start++) {
            boolean[] visited = new boolean[n];
            Queue<Integer> queue = new LinkedList<>();

            queue.offer(start);
            visited[start] = true;

            int count = 0;

            while (!queue.isEmpty()) {
                int current = queue.poll();
                count++;

                for (int friend : graph[current]) {
                    if (!visited[friend]) {
                        visited[friend] = true;
                        queue.offer(friend);
                    }
                }
            }

            // 如果传播范围更大，则更新答案
            // 相同数量时保留较小的用户 ID
            if (count > maxWalls) {
                maxWalls = count;
                bestUser = start;
            }
        }

        System.out.println(bestUser);
    }
}
