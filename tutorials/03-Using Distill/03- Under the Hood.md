# Under the Hood

## Trivium API

Distill uses the Trivium API in two ways: the first uses the GET method to get the nodes/edges in the network, and the second uses a PATCH call to update the nodes with the Distill Score.

The following code snippet uses the Trivium Python module to GET the nodes from the specified diagram. 

```python
def get_nodes(model_name, diagram_name):
    '''Grabs network model information regarding the graph nodes
    by using the Trivium API.'''

    ALLOWED_NODE_TYPES = ['td.cyber.node']

    # set params
    params = {
            "custom.isNetworkDiagram" : "true"
    }

    diagrams = trivium.api.element.get(model_name, element=diagram_name)
    ids = list(diagrams["custom"]["diagramContents"].keys())
    params = {'ids' : ','.join(ids)}
    elements = trivium.api.element.get(model_name, params=params)
    nodes = [e for e in elements if e['type'] in ALLOWED_NODE_TYPES]
    return nodes
```

Notice the ALLOWED_NODE_TYPES list, which filters the API to only return objects of type `td.cyber.node`. This way the API and parsing runs quicker as we are not recieving extraneous data.

The following code snippet updates the Trivium model with the Distill Scores.

```python
def update_model(model, diagram, ip_val, score_dict):
    '''Passes new Distill Score information into the user's
    Trivium network model.'''

    ALLOWED_NODE_TYPES = ['td.cyber.node']

    # This tells us whats in the diagram.
    diagrams = trivium.api.element.get(model, element=diagram)
    ids = list(diagrams["custom"]["diagramContents"].keys())
    params = {'ids' : ','.join(ids), 'fields': 'id,name,type,source,target,custom'}
    # This grabs the nodes from the diagram.
    elements = trivium.api.element.get(model, params=params)
    nodes = [e for e in elements if e['type'] in ALLOWED_NODE_TYPES]

    for node in nodes:
        ip = node['custom']['properties']['ip']['value']
        for i in range(len(ip_val)):
            if ip == ip_val[i]:
                score = match_ip(ip_val[i], score_dict)
                node['custom']['properties']['score'] = {'type':'string', 'value': str(score), 'units':''}

    trivium.api.element.patch(model, nodes)
```

Similarly to the get_nodes() method, this function filters the objects in the diagram using the ALLOWED_NODE_TYPES list. We then add custom properties (in this case the Distill Scores) to the dictionary returned by the GET API, and then run a PATCH call using the method call on the last line.

The Trivium API allows for a lot of flexibility in how you access and interact with the API and the model data, especially in a Python script.

## Scoring

The scoring in this project is in an intentionally proof-of-concept form. This type of cybersecurity analysis is a cutting edge field of research, and as such we intended to make this as extensible as possible so that better scoring systems can easily be added. The scoring for each node is as follows:

For each node, $n$, with CVE list, $c$:

$\quad$ Let $s_n = 0$ 

$\quad$ For each CVE, $v$ in $c$ with CVSS base score, $b_v$, and CVSS temporal score, $t_v$:

$\quad\quad s_n = s_n + (b_v \times t_v)$

Once we have a list of the $s_n$ for every node $n$, normalize the data value $s_n$ to $s'_n$ using decimal scaling normalization as follows:

$$
s_n' = \frac{s_n}{10^j}
$$

where $j$ is the smallest integer such that $\max(|s'_n|) < 1$

In simpler terms, find the maximum node score and count how many digits there are to the left of the decimal. Let that value equal $j$ in the formula above.
