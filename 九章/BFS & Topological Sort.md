# BFS & Topological Sort
流程

- queue; 放入第一个元素
- while(!q.isEmpty)
- temp = q.poll() 
- 然后操作，其中补充q.offer();
- 如果是求路径的问题还需要visitedSet

BFS 应用场景

- graph
	- 最短路径（BFS可以但是DFS不行的）
	- clone graph
	- 找联通的岛屿
- 层序遍历
	- Serialize and Deserialize Binary Tree
- 拓扑排序

## 最短路径
### Knight Shortest Path

## 拓扑排序
用一个map存每个点的inDegree  
queue用来bfs

	/**
	 * Definition for Directed graph.
	 * class DirectedGraphNode {
	 *     int label;
	 *     ArrayList<DirectedGraphNode> neighbors;
	 *     DirectedGraphNode(int x) { label = x; neighbors = new ArrayList<DirectedGraphNode>(); }
	 * };
	 */
	
	public class Solution {
	    /*
	     * @param graph: A list of Directed graph node
	     * @return: Any topological order for the given graph.
	     */
	    public ArrayList<DirectedGraphNode> topSort(ArrayList<DirectedGraphNode> graph) {
	        ArrayList<DirectedGraphNode> res = new ArrayList<DirectedGraphNode>();
	        HashMap<DirectedGraphNode, Integer> indegreeMap = new HashMap<DirectedGraphNode, Integer>();
	        Queue<DirectedGraphNode> q = new LinkedList<DirectedGraphNode>();
	        // calculate indegree
	        for (DirectedGraphNode node : graph) {
	            for(DirectedGraphNode neig : node.neighbors) {
	                if(indegreeMap.containsKey(neig)) {
	                    indegreeMap.put(neig, indegreeMap.get(neig) + 1);
	                } else {
	                    indegreeMap.put(neig, 1);
	                }
	            }
	        }
	        
	        // if indegree == 0, put in the queue
	        for (DirectedGraphNode node : graph) {
	            if(!indegreeMap.containsKey(node)) {
	                q.offer(node);
	                res.add(node);
	            }
	        }
	        
	        // 每个点的neighbor，减少indgree
	        while(!q.isEmpty()) {
	            DirectedGraphNode d = q.poll();
	            for(DirectedGraphNode neig : d.neighbors) {
	                indegreeMap.put(neig, indegreeMap.get(neig) - 1);
	                if(indegreeMap.get(neig) == 0) {
	                    q.offer(neig);
	                    res.add(neig);
	                }
	            }
	        }
	        
	        
	        return res;
	    }
	}