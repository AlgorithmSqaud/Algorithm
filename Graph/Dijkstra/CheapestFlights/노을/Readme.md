# K 경유지 내 가장 저렴한 항공권

- 분류: 최단 경로 문제  < 파이썬 알고리즘 인터뷰 - 379p > 

https://leetcode.com/problems/cheapest-flights-within-k-stops/



```
시작점에서 도착점까지의 가장 저렴한 가격을 계산하되, K개의 경유지 이내에 도착하는 가격을 리턴하라. 
경로가 존재하지 않을 경우 -1을 리턴한다.
```





![](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/02/16/995.png)

```java
Input: n = 3,

flights = [[0,1,100],[1,2,100],[0,2,500]], 
src = 0,   // 출발지
dst = 2,   // 목적지
k = 1   // 경유 가능 횟수

Output: 200
```



### 다익스트라 알고리즘 풀이

- 기본 테스트 케이스는 통과했지만......제출하면 `시간초과`로 실패함 😂😂
- 교재 답안이나 구글 풀이를 모두  참고해보았지만 역시 `시간초과`...
- Discuss를 보니 최근에 케이스가 더 추가됐는지 `시간초과`를 많이 겪는 것 같다.

```java
public class Solution {

    public int findCheapestPrice(int n, int[][] flights, int src, int dst, int K) {

        // 길이 없을 경우
        if (flights.length == 0) {
            return -1;
        }

        HashMap<Integer, List<int[]>> graph = new HashMap<>();

        // 그래프 build
        for (int[] flight : flights) {

            // 그래프에 노드가 존재하지 않는 경우, 그래프 초기화

            if (!graph.containsKey(flight[0])) {
                graph.put(flight[0], new ArrayList<int[]>());
            }

            // 해당 노드에서 다른 노드로 가는 비용에 대한 정보 그래프 삽입
            graph.get(flight[0]).add(new int[]{flight[1], flight[2]});
        }


        // 인접 노드로 갈 때 최소비용인 곳으로 가야하므로, 정렬이 가능한 PriorityQueue 사용
        // 비용 기준 오름차순 정렬
        PriorityQueue<Node> q = new PriorityQueue<Node>((a, b) -> (a.cost - b.cost));

        // [목적지, 비용, 경유횟수] 초깃값 정하기, 자기자신은 0비용이고, 경유를 -1부터 시작해야 한번이라도 움직일 수 있음.
        q.add(new Node(src, 0, -1));


        while (!q.isEmpty()) {

            Node current = q.poll();

            // 큐에서 꺼낸 현재 도시가 목적지와 같다면, 현재 비용을 리턴하고 끝낸다.
            if (current.city == dst) {
                return current.cost;
            }

            // 경유할 수 있는 횟수가 남았다면, 현재 노드의 인접 노드 그래프 탐색 (BFS)
            if (current.stop < K) {
                List<int[]> nexts = graph.getOrDefault(current.city, new ArrayList<int[]>());

                // 키 값으로 그래프에서 나온 value 이므로, 인근 노드의 {목적지 노드, 비용} 리스트가 나올거임. 
                for (int[] next : nexts) {
                    q.add(new Node(next[0], current.cost + next[1], current.stop + 1));
                }
            }
        }
        return -1;
    }

}

class Node {
    int city;
    int cost;
    int stop;

    public Node(int city, int cost, int stop) {
        this.city = city;
        this.cost = cost;
        this.stop = stop;
    }
}


```



### 파이썬 코드 

- Python 3: BFS only visit a city twice if it is cheaper to avoid TLE
- https://leetcode.com/problems/cheapest-flights-within-k-stops/discuss/1222396/Python-3%3A-BFS-only-visit-a-city-twice-if-it-is-cheaper-to-avoid-TLE

```python
class Solution:
    def findCheapestPrice(self, n: int, flights: List[List[int]], src: int, dst: int, k: int) -> int:
        
        graph = defaultdict(set)  # set ??
        
        for u, v, w in flights: 
            graph[u].add((v, w))
            
        W = defaultdict(lambda: inf)
            
        res = inf
        q = [(src, 0, 0)]
        
        for loc, price, stops in q: 
            if stops > k + 1: 
                break
            if loc == dst: 
                res = min(res, price)
            for to, w in graph[loc]: 
                if W[to] > price + w: 
                    W[to] = price + w
                    q.append((to, price + w, stops + 1))
                
        return res if res < inf else -1
```

