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

![](/blog/assets/images/posts/path-finding1.png)

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

## C++ Implementation of A*

```
/*
 Sample code from https://www.redblobgames.com/pathfinding/a-star/
 Copyright 2014 Red Blob Games <redblobgames@gmail.com>

 Feel free to use this code in your own projects, including commercial projects
 License: Apache v2.0 <http://www.apache.org/licenses/LICENSE-2.0.html>
*/

#include <iostream>
#include <iomanip>
#include <map>
#include <set>
#include <array>
#include <vector>
#include <utility>
#include <queue>
#include <tuple>
#include <algorithm>
#include <cstdlib>
#include <unordered_map>

struct SimpleGraph {
    std::map<char, std::vector<char> > edges;

    std::vector<char> neighbors(char id) {
        return edges[id];
    }
};

SimpleGraph example_graph {{
                                   {'A', {'B'}},
                                   {'B', {'A', 'C', 'D'}},
                                   {'C', {'A'}},
                                   {'D', {'E', 'A'}},
                                   {'E', {'B'}}
                           }};

struct GridLocation {
    int x, y;
};

struct SquareGrid {
    static std::array<GridLocation, 4> DIRS;

    int width, height;
    std::set<GridLocation> walls;

    SquareGrid(int width_, int height_)
            : width(width_), height(height_) {}

    bool in_bounds(GridLocation id) const {
        return 0 <= id.x && id.x < width
               && 0 <= id.y && id.y < height;
    }

    bool passable(GridLocation id) const {
        return walls.find(id) == walls.end();
    }

    std::vector<GridLocation> neighbors(GridLocation id) const {
        std::vector<GridLocation> results;

        for (GridLocation dir : DIRS) {
            GridLocation next{id.x + dir.x, id.y + dir.y};
            if (in_bounds(next) && passable(next)) {
                results.push_back(next);
            }
        }

        if ((id.x + id.y) % 2 == 0) {
            // aesthetic improvement on square grids
            std::reverse(results.begin(), results.end());
        }

        return results;
    }
};

std::array<GridLocation, 4> SquareGrid::DIRS =
        {GridLocation{1, 0}, GridLocation{0, -1}, GridLocation{-1, 0}, GridLocation{0, 1}};

// Helpers for GridLocation

bool operator == (GridLocation a, GridLocation b) {
    return a.x == b.x && a.y == b.y;
}

bool operator != (GridLocation a, GridLocation b) {
    return !(a == b);
}

bool operator < (GridLocation a, GridLocation b) {
    return std::tie(a.x, a.y) < std::tie(b.x, b.y);
}

std::basic_iostream<char>::basic_ostream& operator<<(std::basic_iostream<char>::basic_ostream& out, const GridLocation& loc) {
    out << '(' << loc.x << ',' << loc.y << ')';
    return out;
}

// This outputs a grid. Pass in a distances map if you want to print
// the distances, or pass in a point_to map if you want to print
// arrows that point to the parent location, or pass in a path vector
// if you want to draw the path.
template<class Graph>
void draw_grid(const Graph& graph, int field_width,
               std::map<GridLocation, double>* distances=nullptr,
               std::map<GridLocation, GridLocation>* point_to=nullptr,
               std::vector<GridLocation>* path=nullptr) {
    for (int y = 0; y != graph.height; ++y) {
        for (int x = 0; x != graph.width; ++x) {
            GridLocation id {x, y};
            std::cout << std::left << std::setw(field_width);
            if (graph.walls.find(id) != graph.walls.end()) {
                std::cout << std::string(field_width, '#');
            } else if (point_to != nullptr && point_to->count(id)) {
                GridLocation next = (*point_to)[id];
                if (next.x == x + 1) { std::cout << "> "; }
                else if (next.x == x - 1) { std::cout << "< "; }
                else if (next.y == y + 1) { std::cout << "v "; }
                else if (next.y == y - 1) { std::cout << "^ "; }
                else { std::cout << "* "; }
            } else if (distances != nullptr && distances->count(id)) {
                std::cout << (*distances)[id];
            } else if (path != nullptr && find(path->begin(), path->end(), id) != path->end()) {
                std::cout << '@';
            } else {
                std::cout << '.';
            }
        }
        std::cout << '\n';
    }
}

void add_rect(SquareGrid& grid, int x1, int y1, int x2, int y2) {
    for (int x = x1; x < x2; ++x) {
        for (int y = y1; y < y2; ++y) {
            grid.walls.insert(GridLocation{x, y});
        }
    }
}

SquareGrid make_diagram1() {
    SquareGrid grid(30, 15);
    add_rect(grid, 3, 3, 5, 12);
    add_rect(grid, 13, 4, 15, 15);
    add_rect(grid, 21, 0, 23, 7);
    add_rect(grid, 23, 5, 26, 7);
    return grid;
}

struct GridWithWeights: SquareGrid {
    std::set<GridLocation> forests;
    GridWithWeights(int w, int h): SquareGrid(w, h) {}
    double cost(GridLocation from_node, GridLocation to_node) const {
        return forests.find(to_node) != forests.end()? 5 : 1;
    }
};

GridWithWeights make_diagram4() {
    GridWithWeights grid(10, 10);
    add_rect(grid, 1, 7, 4, 9);
    typedef GridLocation L;
    grid.forests = std::set<GridLocation> {
            L{3, 4}, L{3, 5}, L{4, 1}, L{4, 2},
            L{4, 3}, L{4, 4}, L{4, 5}, L{4, 6},
            L{4, 7}, L{4, 8}, L{5, 1}, L{5, 2},
            L{5, 3}, L{5, 4}, L{5, 5}, L{5, 6},
            L{5, 7}, L{5, 8}, L{6, 2}, L{6, 3},
            L{6, 4}, L{6, 5}, L{6, 6}, L{6, 7},
            L{7, 3}, L{7, 4}, L{7, 5}
    };
    return grid;
}

template<typename T, typename priority_t>
struct PriorityQueue {
    typedef std::pair<priority_t, T> PQElement;
    std::priority_queue<PQElement, std::vector<PQElement>,
            std::greater<PQElement>> elements;

    inline bool empty() const {
        return elements.empty();
    }

    inline void put(T item, priority_t priority) {
        elements.emplace(priority, item);
    }

    T get() {
        T best_item = elements.top().second;
        elements.pop();
        return best_item;
    }
};

template<typename Location, typename Graph>
void dijkstra_search
        (Graph graph,
         Location start,
         Location goal,
         std::map<Location, Location>& came_from,
         std::map<Location, double>& cost_so_far)
{
    PriorityQueue<Location, double> frontier;
    frontier.put(start, 0);

    came_from[start] = start;
    cost_so_far[start] = 0;

    while (!frontier.empty()) {
        Location current = frontier.get();

        if (current == goal) {
            break;
        }

        for (Location next : graph.neighbors(current)) {
            double new_cost = cost_so_far[current] + graph.cost(current, next);
            if (cost_so_far.find(next) == cost_so_far.end()
                || new_cost < cost_so_far[next]) {
                cost_so_far[next] = new_cost;
                came_from[next] = current;
                frontier.put(next, new_cost);
            }
        }
    }
}

template<typename Location>
std::vector<Location> reconstruct_path(
        Location start, Location goal,
        std::map<Location, Location> came_from
) {
    std::vector<Location> path;
    Location current = goal;
    while (current != start) {
        path.push_back(current);
        current = came_from[current];
    }
    path.push_back(start); // optional
    std::reverse(path.begin(), path.end());
    return path;
}

inline double heuristic(GridLocation a, GridLocation b) {
    return std::abs(a.x - b.x) + std::abs(a.y - b.y);
}

template<typename Location, typename Graph>
void a_star_search
        (Graph graph,
         Location start,
         Location goal,
         std::map<Location, Location>& came_from,
         std::map<Location, double>& cost_so_far)
{
    PriorityQueue<Location, double> frontier;
    frontier.put(start, 0);

    came_from[start] = start;
    cost_so_far[start] = 0;

    while (!frontier.empty()) {
        Location current = frontier.get();

        if (current == goal) {
            break;
        }

        for (Location next : graph.neighbors(current)) {
            double new_cost = cost_so_far[current] + graph.cost(current, next);
            if (cost_so_far.find(next) == cost_so_far.end()
                || new_cost < cost_so_far[next]) {
                cost_so_far[next] = new_cost;
                double priority = new_cost + heuristic(next, goal);
                frontier.put(next, priority);
                came_from[next] = current;
            }
        }
    }
}


int main() {
    GridWithWeights grid = make_diagram4();
    GridLocation start{1, 4};
    GridLocation goal{8, 5};
    std::map<GridLocation, GridLocation> came_from;
    std::map<GridLocation, double> cost_so_far;
    a_star_search(grid, start, goal, came_from, cost_so_far);
    draw_grid(grid, 2, nullptr, &came_from);
    std::cout << '\n';
    draw_grid(grid, 3, &cost_so_far, nullptr);
    std::cout << '\n';
    std::vector<GridLocation> path = reconstruct_path(start, goal, came_from);
    draw_grid(grid, 3, nullptr, nullptr, &path);
}
```
