# Under the Hood

## Trivium API

Sublimate uses the Trivium API to get the `td.systems.actor` objects in the diagram, which denote the Start and End nodes in the graph. The following code snippet contains the API call:

```python
print("Reading attack and victim from Trivium...")
print()
            

diagramData = trivium.api.element.get(args.model, element=args.diagram)
ids = list(diagramData["custom"]["diagramContents"].keys())
params = {'ids' : ','.join(ids)}
elements = trivium.api.element.get(args.model, params=params)
actorNodes = [e for e in elements if e['type'] == 'td.systems.actor']
```

Here, we get the IDs of all the objects in the diagram. Then, we create a new list `actorNodes` based on the elements that have type `td.systems.actor`.

## Scoring

The path scoring uses the NetworkX library to output a list of all simple paths (a path through the network from a Start node to an End node where every visited node is distinct), and then multiply the Distill Scores of each node in that path. The paths are then ordered by the score, with the weights then normalized using the following formula:

$$
p_i' = \frac{p_i - \min(p)}{\max(p) - \min(p)}
$$

where $p$ is the list of paths and $p_i$ is the current path being normalized. The relevant code is below:

```python
paths = nx.all_simple_paths(self.G, source=ipToTid(self.attackingNode), target=ipToTid(self.victimNodes[0].ip))

pathWeightPairs = []
max_weight = float("-inf")
min_weight = float("inf")
for path in paths:
    weight = math.prod(float(self.G.nodes[node]['distill_score']) for node in path)
    pathWeightPairs.append((path,weight))
    
    if weight > max_weight:
        max_weight = weight
    if weight < min_weight:
        min_weight = weight

pathWeightPairs.sort(key=lambda p: p[1], reverse=True)
pathWeightPairs = pathWeightPairs[:number_of_paths]

for victim in self.victimNodes:
    trivium_id = ipToTid(victim.ip)
    for p,w in pathWeightPairs:
        w = float((w - min_weight) / (max_weight - min_weight))
        if p[-1] != trivium_id: continue
        path_to_victim = compromisePath()
        path_to_victim.addToWeight(w)
        ipPath = list(map(tidToIp, p))
        path_to_victim.path = ipPath
        victim.addPath(path_to_victim)
```
