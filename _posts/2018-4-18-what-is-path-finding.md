---
layout: post
title:  "Introduction to path-finding"
date:   2018-4-15 10:00:00 -0400
tag: cv
---

## [Amit’s A* Pages](http://theory.stanford.edu/~amitp/GameProgramming/)

### Breadth First Search
- Breadth First Search explores equally in all directions. This is an incredibly useful algorithm, not only for regular path finding, but also for procedural map generation, flow field pathfinding, distance maps, and other types of map analysis.

### Dijkstra’s Algorithm
- Instead of exploring all possible paths equally, it favors lower cost paths. We can assign lower costs to encourage moving on roads, higher costs to avoid forests, higher costs to discourage going near enemies, and more. When movement costs vary, we use this instead of Breadth First Search.

### Greedy Best First Search
- In Dijkstra’s Algorithm we used the actual distance from the start for the priority queue ordering. Here instead, in Greedy Best First Search, we’ll use the estimated distance to the goal for the priority queue ordering. The location closest to the goal will be explored first.

### The A* algorithm
- Dijkstra’s Algorithm works well to find the shortest path, but it wastes time exploring in directions that aren’t promising. Greedy Best First Search explores in promising directions but it may not find the shortest path. The A* algorithm uses both the actual distance from the start and the estimated distance to the goal.

```
frontier = PriorityQueue()
frontier.put(start, 0)
came_from = {}
came_from[start] = None

while not frontier.empty():
   current = frontier.get()

   if current == goal:
      break
   
   for next in graph.neighbors(current):
      if next not in came_from:
         priority = heuristic(goal, next)
         frontier.put(next, priority)
         came_from[next] = current
```

![Alt text](/blog/assets/images/posts/path-finding1.png)

-  You’ll find that when Greedy Best-First Search finds the right answer, A* finds it too, exploring the same area. When Greedy Best-First Search finds the wrong answer (longer path), A* finds the right answer, like Dijkstra’s Algorithm does, but still explores less than Dijkstra’s Algorithm does.

-  A* is the best of both worlds. As long as the heuristic does not overestimate distances, A* finds an optimal path, like Dijkstra’s Algorithm does. A* uses the heuristic to reorder the nodes so that it’s more likely that the goal node will be encountered sooner.

### Which algorithm should you use for finding paths on a game map?

- If you want to find paths from or to all all locations, use Breadth First Search or Dijkstra’s Algorithm. Use Breadth First Search if movement costs are all the same; use Dijkstra’s Algorithm if movement costs vary.
- If you want to find paths to one location, use Greedy Best First Search or A*. Prefer A* in most cases. When you’re tempted to use Greedy Best First Search, consider using A* with an “inadmissible” heuristic.
-  As the heuristic becomes smaller, A* turns into Dijkstra’s Algorithm. As the heuristic becomes larger, A* turns into Greedy Best First Search.

## [Map representations](http://theory.stanford.edu/~amitp/GameProgramming/MapRepresentations.html)

- The map representation can make a huge difference in the performance and path quality.
- The map representation used for pathfinding does not have to be the same as the representation used for other things in the game. However, using the same representation is a good starting point, until you find that you need better paths or more performance.

## [A magic website explain how path-finding works visually](http://qiao.github.io/PathFinding.js/visual/)

- The aim of this project is to provide a path-finding library that can be easily incorporated into web games. It may run on Node.js or the browser.
