# Location Network Fingerprinting

This is a project undertaken at the University of California, Berkeley Fall 2018 Challenge Lab: Amazoogle. 

## Overview

The aim is to extract fingerprints from location patterns. One of the algorithms to do this involves locating bridges in force-directed graphs. Bridges are nodes in a connected graph that connect multiple subgraphs, where if the bridge node was removed, then the subgraphs would be disconnected. Hence if we identify all the bridge nodes, we can infer patterns of nodes needed to connect sub-networks of nodes. 

## Documentation
A. Install the required libraries
<b>Required libraries:</b>

pandas | matplotlib | networkx | re | math | gmplot 

B. The notebooks contain the entire pipeline for running the algorithm, visualizing the fingerprints on the map, and exporting the coordinates and html map. 
1. To view the run the coordinates, ensure there is a dataframe and you know the column names for latitude and longitude. 
Open implementation_notebook and modify Function(dataframe, int_or_float, samplesize, col1_name, col2_name, magicnumber, variable_index, html) accordingly. 
2. Running the whole notebook will return the network graph and map plot. 
3. For export of the coordinates, run the coordinate_export notebook. 

## Further details
In our particular use case, we have datasets of (latitudes, longitudes) of successful and failed chains/franchises. We have additional datasets manually gathered for these successful and failed chains dependent on region (particular states in the US) and on industry (e.g. burger, pizza, coffee, clothing, etc). For the repository, we have provided a test dataset for testing the system, since the actual dataset is relatively large. 

The main objective is to pass the location coordinate data through the algorithm that operates as follows:

There are 8 key variables: [ dataframe, int_or_float, samplesize, col1_name, col2_name, magicnumber, variable_index, html ]. Dataframe is the dataset we would like to pass through as a dataframe, int_or_float can be 0 (int) or 1 (float), where we can decide to pass the coordinates as either integers or floats – this decision matters as sometimes the network can be very large and it may take a long time to generate edges between many floats, thus converging to integers saves time during graph generation. Samplesize is a variable intended for sampling the dataframe, as we understand it may be time-consuming to extract fingerprints from the whole dataset at once, so we may extract fingerprints at a sampled rate. Col1_name refers to the latitude column in the dataset, col2_name refers to the longitude column. Magicnumber refers to the maximum weight allowed to form edges (i.e. if we set the max as 10, then edge weight formed between two coordinates cannot be greater than 10). Variable_index refers to the type of algorithm used in fingerprinting; in this repository, we provided the option to use either central_nodes in each sub-network (variable_index=0) or bridge_nodes in the network (variable_index=1). 

Recommended sample input is: Function(industry_frame, 1, 1500, "F1", "F2", 5, 1, "success_map_testing.html")

Within the function, we first define the edge_weight calculation. Edge weights are calculated by obtaining the coordinate distance between the two coordinates of each node. 

Then we define sampling by first sorting the dataframe by longitude, followed by taking a sample at given samplesize value. 

We then convert coordinates to integers if int is selected; floats if otherwise. 

Afterwards, we generate a network graph, and for each set of coordinates in the dataset, we input nodes to obtain a node list. This node list is needed to iterate through a new graph again. 

We generate a completely new graph (different from the prior one, as we are setting custom edge weights unlike the previous graph); in this graph, we are connecting each node to every other node in the nodelist, and setting edge weights based on the weight calculation (i.e. distance between the node’s coordinates). The edge weight must be smaller than a certain value (magicnumber), otherwise the edge will not be formed; this helps to narrow down the network to specific regions or networks. 

For reference, we then generate a network graph (with/without labels). 
 
For the fingerprint identification, we have provided the means to do so with central_nodes and bridges. 

For central_nodes, we obtain the giant component of the graph (most connected subnetwork), and obtain the central nodes of the giant component. 

For bridges, we use networkx’s bridges function to obtain bridge coordinates. 

We then append the latitudes and longitude coordinates of the fingerprint nodes for export. 

For each iteration that we undergo the function, we can flatten into a larger list of latitudes and longitudes, turn them into a dataframe, then export as .csv/.json for further use.

In our use case, we use gmplot (a Google Maps plotting library) with a default location set to the US. We plot scatter points of the latitudes and longitudes across the map, plot lines between connecting points, and add on small heatmap colors over each scatter point to clarify the location. The html variable is the name of the html file for export, and we can obtain an html file of the coordinates and lines plotted out, i.e. fingerprints on the map. 
 
