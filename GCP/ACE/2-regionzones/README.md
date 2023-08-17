# Regions and Zones
<li>Imagine that your application is deployed in a data center in London.
<li>What would be the challenges?
<ol>
<li>Slow access for users from other parts of the world(high latency)
<li>What if the data center crashes?
<ul>
<li>Your application goes down(low availability)
</ul>
</ol>

### To improve we can have multiple data centers
<li>Let's add in one more data center in london.
<li>What would be the challenges?
<ol>
<li>Slow access for users from other parts of the world.
<li>(SOLVED) What if the data center crashes?
<ul>
<li>Your application is still available from other data center.
</ul>
<li>What if entire region of london is unavailable?
<ul>
<li>Your application goes down.
</ul>
</ol>

### To improve we can have multiple regions.
<li>Let's add a new region: Mumbai
<li>What would be the challenges?
<ol>
<li>(SOLVED)Slow access for users from other parts of the world.
<li>(SOLVED) What if the data center crashes?
<ul>
<li>Your application is still available from other data center.
</ul>
<li>(SOLVED)What if entire region of london is unavailable?
<ul>
<li>Your application is served from Mumbai.
</ul>
</ol>

## Regions
<li>Imagine setting up data centers in different regions around the world.
<ul>
<li>Would that be easy?
</ul>

<li>(SOLVED)Google provides 20+ regions around the world.(expanding every year)
<li><b>Region</b>:<br>Specific geographical location to host your resources.

<li><b>Advantages</b>:
<ol>
<li>High Availability
<li>Low latency
<li>Global footprint
<li>Adhere to government regulations</ol>

## Zones
<li>How to achieve high availability in the same region(or geographic location)?
<ul><li>Enter zones</ul>
<li>Each region has 3 or more zones
<li>(advantage)Increase availabilit and fault tolerance within same region.
<li>(remember)Each zone has one or more discrete clusters.
<ul><li>Cluster: Distinct physical infrastructure that is hosted in a data center.
</ul>
<li>(Remember)Zones in a region are connected through low-latency links.

### Examples.
<table>
<tr>
<th>Region code</th>
<th>Region</th>
<th>Zones</th>
<th>Zone list</th>
</tr>
<tr>
<td>us-west1</td>
<td>The Dalles, Oregon, North America</td>
<td>3</td>
<td><li>us-west1-a
<li>us-west1-b
<li>us-west1-c
</td>
</tr>
<tr>
<td>europe-north1</td>
<td>Hamina,Finland, Europe</td>
<td>3</td>
<td><li>europe-north1-a
<li>europe-north1-b
<li>europe-north1-c
</td>
</tr>
<tr>
<td>asia-south1</td>
<td>Mumbai, India APAC</td>
<td>3</td>
<td>
<li>asia-south1-a
<li>asia-south1-b
<li>asia-south1-c
</td></tr></table>


<li>What is on-demand resource provisioning?

Provisioning (renting) resources when you want them and releasing them back to the cloud when you do not need them



