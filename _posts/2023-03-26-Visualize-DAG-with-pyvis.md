---
layout: post
title: Visualize DAG with pyvis
tags: [DAG, visualization, pyvis]
---

In previous post [Visualize DAG with Graphviz](/2023/03/25/visualize-DAG-with-graphviz/), I talked about how to use Graphviz to display DAG. Graphviz is greate but it can only generate pictures in svg. For nodes with large amount of data, it will be hard to show all the data in a single picture. 

With today's javascript, it will be nice to show and hide node infomation dynamically. Rowland has created a internal repo called gexplorer, which runs on a flask server with vis.js on the front end. The problem with this implementation is that it is hard to share the data. The other people has to have access to your server, or have to spin up his own server. Or he can only receive screenshot of the picture. I think it would be nice if we have this kinda of dynamic implementation with jupyterlab. 

Last Friday(03/24/2023), a colleage Tzintzuni Garcia from bio team of GDC, did a presentation about his attempt. He used [bokeh](https://docs.bokeh.org/en/latest/) to build the visulaizaiton. Network is shown in the example pictures of bokeh but I could not find good tutorial about how to build network with bokeh on its offical website. I think network is not one of their main focus. And in the presentation, the library is not hendling hierarchy for DAG very well.

Recently I found a python library called [pyvis](https://pyvis.readthedocs.io/en/latest/). Which also use [vis.js](https://visjs.org/). vis.js is a <!--break-->

>A dynamic, browser based visualization library.
>The library is designed to be easy to use, to handle large amounts of dynamic data, and to enable manipulation of and interaction with the data.
>The library consists of the components DataSet, Timeline, Network, Graph2d and Graph3d. 

It has rich implementation for [networks](https://visjs.github.io/vis-network/examples/). Pyvis is mainly the python wrapper for the network part of vis.js. 


# Examples from yaml file

## Examples
The examples from previous psot [Visualize DAG with Graphviz](/2023/03/25/visualize-DAG-with-graphviz/) will look like the following:

![1585_vis](/images/pyvis_gdc/gdc_dev_1585_vis.png)

The gdc samples from the previous post will look like:

![gdc_samples](/images/pyvis_gdc/gdc_samples_vis.png)


## Code

Here is the code for this visualization with pyvis:

{% highlight python %}
import json
import os
import uuid
from functools import lru_cache
from typing import Dict, List

import requests
import yaml
from graphviz import Digraph

from pyvis.network import Network

from IPython.core.display import display, HTML


def draw(nodes: List[Dict[str, str]], edges: List[Dict[str, str]], reverse=False):
    
    got_net = Network(
        notebook=True, 
        cdn_resources='in_line', 
        layout=True, 
        directed=True, 
        height='1111px', 
        width='90%'
    )

    node_id_to_submitter_id = {}
    for node in nodes:
        submitter_id = node.get("submitter_id")
        node_id = node.get("node_id")
        suffix = f"：{node['gencode_version']}" if node.get("gencode_version") else ''

        if submitter_id and node_id:
            node_id_to_submitter_id[node_id] = submitter_id + suffix
        elif submitter_id:
            node_id_to_submitter_id[submitter_id] = submitter_id + suffix
        else:
            node_id_to_submitter_id[node_id] = node_id + suffix

    print(node_id_to_submitter_id)

    for node in nodes:
        n_id = node_id_to_submitter_id.get(
            node.get("node_id") or node.get("submitter_id")
        )
        # print(n_id, node.get("submitter_id"), node.get("node_id"))
        got_net.add_node(
            n_id, 
            n_id, 
            title='\n'.join(f"{k}: {v}" for k, v in node.items()), 
            color=get_color(node.get('label'))
        )
        

    for edge in edges: 
        src = node_id_to_submitter_id.get(edge["src"]) or edge["src"]
        dst = node_id_to_submitter_id.get(edge["dst"]) or edge["dst"]
        # print(src, dst, edge['src'], edge['dst'])
        try: 
            if reverse:
                got_net.add_edge(dst, src)
            else:
                got_net.add_edge(src, dst)
        except AssertionError:
            continue
    
    neighbor_map = got_net.get_adj_list()
    
    got_net.show_buttons()
    options = got_net.options.to_json()
    options = options.replace("hubsize", "directed")
    got_net.set_options(options)
    got_net.show("graph.html")
    return display(HTML("graph.html"))



def draw_yaml(yaml_raw: str):
    try:
        yaml_dict = yaml.load(yaml_raw, Loader=yaml.FullLoader)
    except yaml.scanner.ScannerError:
        yaml_dict = json.loads(yaml_raw)

    return draw(yaml_dict['nodes'], yaml_dict['edges'])


def draw_url(url: str):
    response = requests.get(url)
    print(response.status_code)
    return draw_yaml(response.content)



@lru_cache(maxsize=256)
def get_color(label: str) -> str:
    label = str(uuid.uuid5(UUID_NAMESPACE, label))
    label_color = hex(int("".join(map(str, map(ord, label)))) & 0x00FFFFFF)
    return "#{:f<6}".format(label_color[2:])


UUID_NAMESPACE_SEED = os.getenv("UUID_NAMESPACE_SEED", "f0d2633b-cd8b-45ca-ae86-1d5c759ba0d1")
UUID_NAMESPACE = uuid.UUID("urn:uuid:{}".format(UUID_NAMESPACE_SEED), version=4)

{% endhighlight %}

A few things to metion about this code.

1. As you might notice, I used the same color algorithm written by Rowland.
2. `layout=True, directed=True` are needed for a good hierarchical layout. Those are not set by default.
3. `display(HTML("graph.html"))` is needed for google colab to display the chart. It is not neccessary if you are using jupyterlab etc.
4. `options = options.replace("hubsize", "directed")` will replace the `subMethod` of hierarchical of layout from `hubsize` to `directed`, which works better for our DAG. I think `hubsize` might be more suitable for Undireacted Graph. 
5. The Algorithm from Graphviz is better than vis.js. Our DAG in vis.js sometimes have edges crossing each other. Then you have to manually draw the nodes around to seperate them. 


# Examples for our live data

## Examle

![sample_subtree](/images/pyvis_gdc/sample_subtree.png)

