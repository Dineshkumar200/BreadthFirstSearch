## EX NO : 02
## DATE : 28-04-2022
# <p align="center">Breadth First Search</p>


## AIM

To develop an algorithm to find the route from the source to the destination point using breadth-first search.

## THEORY
Explain the problem statement

## DESIGN STEPS

### STEP 1:
Identify a location in the google map:

### STEP 2:
Select a specific number of nodes with distance
### STEP 3:
Import the required packages and create some methods to define and understand breadthfirstsearch.

### STEP 4:
Include the nodes(locations) and values(distance) in dictionary as keys and values.

### STEP 5:
Pass the required location it will return the distance and destination.



## <br/><br/><br/><br/><br/>ROUTE MAP


![ai1](https://user-images.githubusercontent.com/75235789/166147843-ff5c2518-cadc-479d-9905-41f698f9b7c7.png)



## PROGRAM
```
Student name : Dineshkumar V
Reg.no : 212220230013
```
```python
%matplotlib inline
import matplotlib.pyplot as plt
import random
import math
import sys
from collections import defaultdict, deque, Counter
from itertools import combinations

class Problem(object):
   def __init__(self, initial=None, goal=None, **kwds): 
       self.__dict__.update(initial=initial, goal=goal, **kwds) 
       
   def actions(self, state):        
       raise NotImplementedError
   def result(self, state, action): 
       raise NotImplementedError
   def is_goal(self, state):        
       return state == self.goal
   def action_cost(self, s, a, s1): 
       return 1
   
   def __str__(self):
       return '{0}({1}, {2})'.format(
           type(self).__name__, self.initial, self.goal)
           
class Node:
   def __init__(self, state, parent=None, action=None, path_cost=0):
       self.__dict__.update(state=state, parent=parent, action=action, path_cost=path_cost)

   def __str__(self): 
       return '<{0}>'.format(self.state)
   def __len__(self): 
       return 0 if self.parent is None else (1 + len(self.parent))
   def __lt__(self, other): 
       return self.path_cost < other.path_cost
       
failure = Node('failure', path_cost=math.inf) 
cutoff  = Node('cutoff',  path_cost=math.inf)

def expand(problem, node):
   "Expand a node, generating the children nodes."
   s = node.state
   for action in problem.actions(s):
       s1 = problem.result(s, action)
       cost = node.path_cost + problem.action_cost(s, action, s1)
       yield Node(s1, node, action, cost)
       

def path_actions(node):
   "The sequence of actions to get to this node."
   if node.parent is None:
       return []  
   return path_actions(node.parent) + [node.action]


def path_states(node):
   "The sequence of states to get to this node."
   if node in (cutoff, failure, None): 
       return []
   return path_states(node.parent) + [node.state]
   
FIFOQueue = deque

def breadth_first_search(problem):
   "Search shallowest nodes in the search tree first."
   node = Node(problem.initial)
   if problem.is_goal(problem.initial):
       return node
   frontier = FIFOQueue([node])
   reached = {problem.initial}
   while frontier:
       node = frontier.pop()
       for child in expand(problem, node):
           s = child.state
           if problem.is_goal(s):
               return child
           if s not in reached:
               reached.add(s)
               frontier.appendleft(child)
   return failure
   
class RouteProblem(Problem):
   """A problem to find a route between locations on a `Map`.
   Create a problem with RouteProblem(start, goal, map=Map(...)}).
   States are the vertexes in the Map graph; actions are destination states."""
   
   def actions(self, state): 
       """The places neighboring `state`."""
       return self.map.neighbors[state]
   
   def result(self, state, action):
       """Go to the `action` place, if the map says that is possible."""
       return action if action in self.map.neighbors[state] else state
   
   def action_cost(self, s, action, s1):
       """The distance (cost) to go from s to s1."""
       return self.map.distances[s, s1]
   
   def h(self, node):
       "Straight-line distance between state and the goal."
       locs = self.map.locations
       return straight_line_distance(locs[node.state], locs[self.goal])
       
class Map:
   """A map of places in a 2D world: a graph with vertexes and links between them. 
   In `Map(links, locations)`, `links` can be either [(v1, v2)...] pairs, 
   or a {(v1, v2): distance...} dict. Optional `locations` can be {v1: (x, y)} 
   If `directed=False` then for every (v1, v2) link, we add a (v2, v1) link."""

   def __init__(self, links, locations=None, directed=False):
       if not hasattr(links, 'items'): # Distances are 1 by default
           links = {link: 1 for link in links}
       if not directed:
           for (v1, v2) in list(links):
               links[v2, v1] = links[v1, v2]
       self.distances = links
       self.neighbors = multimap(links)
       self.locations = locations or defaultdict(lambda: (0, 0))

       
def multimap(pairs) -> dict:
   "Given (key, val) pairs, make a dict of {key: [val,...]}."
   result = defaultdict(list)
   for key, val in pairs:
       result[key].append(val)
   return result
   

krishnakiri_to_vellore = Map(
    {('Krishnagiri', 'Burgur'):  16, ('Burgur', 'Natrampalli'): 21, ('Burgur', 'Thirupathur'):31, ('Natrampalli', 'Thekupattu'): 8, ('Thekupattu', 'Vaniyampadi'): 6, 
     ('Thirupathur', 'Jolarpett'): 8, ('Jolarpett', 'Vaniyampadi'):  11, ('Vaniyampadi', 'Meisannapo'): 12, ('Vaniyampadi', 'aambur'): 18, ('aambur', 'Kilmurungai'): 9, 
     ('Kilmurungai', 'Madhanur'): 9, ('Madhanur', 'Pallikonda'):  11, ('Meisannapo', 'Oomerabad'): 13, ('Oomerabad', 'Gudiyatham'): 30,
    ('Gudiyatham', 'Pallikonda'): 12, ('Pallikonda', 'Kalanipakkam'):  5, ('Kalanipakkam', 'Tharvazhi'): 6, ('Thirupathur', 'Mettur'): 21, ('Mettur', 'Alangayam'): 9, 
    ('Alangayam', 'Odugathur'): 23, ('Odugathur', 'Kovindpattamkottai'):  9, ('Kovindpattamkottai', 'Tharvazhi'): 12, ('Tharvazhi', 'Poigai'): 16, ('Tharvazhi', 'Vellore'): 16,
    ('Kalanipakkam', 'Poigai'): 11, ('Poigai', 'Vellore'): 17,})


r0 = RouteProblem('Vaniyampadi', 'Pallikonda', map=krishnakiri_to_vellore)
r1 = RouteProblem('Krishnagiri', 'aambur', map=krishnakiri_to_vellore)
r2 = RouteProblem('Thirupathur', 'Vellore', map=krishnakiri_to_vellore)
r3 = RouteProblem('Burgur', 'Vaniyampadi', map=krishnakiri_to_vellore)
r4 = RouteProblem('Pallikonda', 'Thirupathur', map=krishnakiri_to_vellore)



goal_state_path=breadth_first_search(r0)
print("GoalStateWithPath:{0}".format(goal_state_path))
print(path_states(goal_state_path) )
print("Total Distance={0} Kilometers".format(goal_state_path.path_cost))

goal_state_path=breadth_first_search(r1)
print("\n\nGoalStateWithPath:{0}".format(goal_state_path))
print(path_states(goal_state_path) )
print("Total Distance={0} Kilometers".format(goal_state_path.path_cost))

goal_state_path=breadth_first_search(r2)
print("\n\nGoalStateWithPath:{0}".format(goal_state_path))
print(path_states(goal_state_path) )
print("Total Distance={0} Kilometers".format(goal_state_path.path_cost))

goal_state_path=breadth_first_search(r3)
print("\n\nGoalStateWithPath:{0}".format(goal_state_path))
print(path_states(goal_state_path) )
print("Total Distance={0} Kilometers".format(goal_state_path.path_cost))

goal_state_path=breadth_first_search(r4)
print("\n\nGoalStateWithPath:{0}".format(goal_state_path))
print(path_states(goal_state_path) )
print("Total Distance={0} Kilometers".format(goal_state_path.path_cost))

```


## OUTPUT:

![aii](https://user-images.githubusercontent.com/75235789/166147747-ff8a912a-38d6-4409-a3fc-b324f1511be3.jpg)


## SOLUTION JUSTIFICATION:
The Route solutions are found by Breadth First Search algorithm(following FIFO and routes travelling from left to right).
## RESULT:
Thus,an algorithm developed to find the route from the source to the destination point using breadth-first search.
