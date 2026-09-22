# A*寻路算法 
在一般的BFS求最短路中，在最坏情况下我们会漫无目的（无方向的）去向四面八方遍历整个图，在最坏情况下要在遍历全部图才能得到答案。因此，我们可以引入代价。每次遍历图时，我们可以优先取代价最小的图块进行遍历。  
代价分为两个部分：当前已经走过的路程和预估从当前方块到目标方块的大致路程。   
这个预估代价通常是曼哈顿距离（最常用最简单）和欧拉距离（勾股定理斜边）  
```python
def a_star_search(graph,start,goal):

    frontier = PriorityQueue()
    frontier.put(start,0)
    came_from = {}
    came_so_far = {}
    came_from[start]=None
    cost_so_far[start]=0
    while not frontier.empty():
        current = frontier.get()
        if current == goal:
            break
        for next in graph.neighbors(current):
            new_cost = cost_so_far[current]+graph.cost(current,next)
            if next not in cost_so_far or new_cost < cost_so_far[next]:
                cost_so_far[next]=new_cost
                priority = new_cost + heuristic(goal,next)
                frontier.put(next,priority)
                came_from[next]=current
    return came_from,cost_so_far



```