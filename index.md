---
layout: default
---

<img src="static/graph_seal.png" width="500" height="500" />

## What are the most influential United States patents?


This blog was written as part of the CS-401 Applied Data Analysis course at EPFL,
taught in the autumn of 2017. 

The purpose of this blog is to present an analysis of the patent citations provided by
the [patentsview.org](http://www.patentsview.org) website of the United States Patent
and Trademark Office ([USPTO](https://www.uspto.gov/)). 

The content of this blog is intentionally high-level. Please refer to our
[jupyter notebook](https://nbviewer.jupyter.org/github/qantik/prayingmantissa/blob/master/project/patents.ipynb), if you are interested in viewing a more thorough and complete analysis.


### What is a patent and what are the above mentioned citations?

The USTPO defines a patent as a property right granted by the Government of
the United States of America to an inventor "to exclude others from making,
using, offering for sale, or selling the invention throughout the United States or
importing the invention into the United States" for a limited time in exchange for
public disclosure of the invention when the patent is granted.

The process of obtaining a patent from the USPTO is a lengthy and rigorous procedure.
While applying for a patent a list of cited patents must be provided. From patentsview.org we got the complete list of all patent citations from 1976 up to 2016.

### How can a list of citations be analyzed?

An important subject in mathematics is the field of graph theory. The introductory
paragraph of the Wikipedia article on graph theory says the following: 

>In mathematics, graph theory is the study of graphs, which are mathematical structures 
used to model pairwise relations between objects. A graph in this context is made up
of vertices, nodes, or points which are connected by edges, arcs, or lines. A graph may
be undirected, meaning that there is no distinction between the two vertices associated
with each edge, or its edges may be directed from one vertex to another

In our model the nodes are represented by patents and the edges by citations. Our
citations graph is directed, because it is important that one node cites the other
and not vice versa.

### What was the goal of this project?

The innovation of technology is a fast paced and intertwined process. During our
undergraduate education in computer science and communication systems we learnt about
landmark inventions and innovations, that led to the connected world we live in. Upon
looking into the data, we quickly found these inventions in the citations graph. For example
Stephen Wozniak’s [Microcomputer for use with video display](https://www.google.com/patents/US4136359), which led to the
famous Apple II computer, or the [PageRank](https://www.google.com/patents/US6285999) algorithm, which lies at the
foundation of Google's rise to power. 

These findings led us to the question around which this project evolves, which is:

"Using graph theoretic tools, can we give an educated opinion on what we think were
the most influential United States patents in recent history?"

## Some preliminary facts

To perform analysis on the graph we used the [graph tool library](https://graph-tool.skewed.de/).
A first interesting insight is the number of edges and the number of vertices:

<table>
  <tr>
   <td style="text-align:center;font-size:200%;width:50%;">
     <div id="counter1">1.</div>
   </td>
   <td style="text-align:center;font-size:200%;width:50%;">
     <div id="counter2">
     hello
     </div>
   </td>
  </tr>
</table>


The network graph is massive. The data from patentsview.org contains the date, when the patent was granted. How many patents were granted per year? The following plot visualizes the answer to this question. A clear tendency of growth is made visible. More and more patents are being granted, more and more is being innovated. 

<div id="wrapper" style="height: 750px; width: 750px;">
  <canvas id="plot-patents-year" width="300px" height="300px"></canvas>
</div>

Continuing with the analysis of the provided date, we wanted to find out, if patents are granted more often in specific months, or on specific days of the year. On the green colored plot below on the left, the total number of patents per month is shown. The number of granted patents per month is distributed fairly evenly over the 12 months. Below on the right in red is the number of patents granted per day of the year. Here again one can notice, that the number of patents granted is distributed evenly over all the days, except for the 31st of the month. Can you guess, why there are less patents granted on the 31st? 

<div style="overflow:auto;">
  <div style="width: 450px; height: 400px; float:left;">
    <canvas id="plot-patents-months" width="350px" height="300px"></canvas>
  </div>

  <div style="width:450px; height: 400px; float:left;">
    <canvas id="plot-patents-days" width="350px" height="300px"></canvas>
  </div>
</div>

So far we know, that the network graph containing the patent citations is massive, that the number of patents granted per year has grown steadily and that the patents are granted throughout the year. Let's start using the graph tool.

Insight about a graph can be gained by looking at the connected components it contains. It is of interest to know if a graph is split up into many smaller disjoint graphs, or if for example the entire graph is connected. In the graph tool library there is a function called [label_largest_component](https://graph-tool.skewed.de/static/doc/topology.html#graph_tool.topology.label_largest_component), which runs in $$\mathcal{O}(V + E)$$ and labels the nodes, which are connected to the largest component. Please follow [this](https://en.wikipedia.org/wiki/Complexity) link, if you would like to get an introduction to the big-O notation and complexity theory. We ran the algorithm and were stunned by the result. The number of connected nodes in our graph is 8261059, this means only 13932 of the nodes are not connected to the largest component! It follows, that $$99.998\% $$ of the nodes are connected. The below visualization tempts to give a feeling of what this implies:

<div style="height:650px">
  <div style="float:left; margin-right: 60px;">
    <svg id="grid" width="550" height="600"></svg>
  </div>
  <div style="height: 350px; width: 150px; float:left;text-align:center;">
    <div id="counter3" style="position: relative; top:60%; font-size: 400%"></div>
  </div>
</div>

Confident that we are onto something, we dig deeper. A basic algorithm on graphs is to calculate all shortest paths between a subset of the nodes. As a side note we would like to mention, that due to the size of the graph, it is not possible for us to run algorithms with $$\mathcal{O}(V^2)$$, which would take days to finish. As a consequence we only run algorithms with complexity lower than squared. Another important side note is, that it needs to be kept in mind, that our graph is directed. Even though almost all the nodes of the graph are connected, there isn't a path from all these nodes to each other, because of the directed edges. Some nodes do not have any incoming edges, so they cannot be accessed by graph traversal from other nodes. More about this later, but for now let's go back to the shortest paths.

We handpicked three patents, that we find historically relevant and three new patents, that have received media attention:

<table id="icon-grid">
  <tr>
   <td>
     <div class="outer" id="o1-11">
     <i class="fa fa-bolt fa-5x"></i>
     </div>
     <div class="inner" id="i1-11">
      <ul>
        <li>  
	  <a href="https://www.google.com/patents/US2981877" target="_blank">Semiconductor device-and-lead structure
	  </a>
	</li>
        <li>Apr 25, 1961</li>
      </ul>
     </div>
   </td>
   <td>
     <div class="outer" id="o1-12">
     <i class="fa fa-mobile fa-5x"></i>
     </div>
     <div class="inner" id="i1-12">
      <ul>
        <li>  
	  <a href="https://www.google.com/patents/US20090241072" target="_blank">Unlocking a Device by Performing Gestures on an Unlock Image
	  </a>
	</li>
        <li>	Sep 24, 2009</li>
      </ul>
     </div>
   </td>
  </tr>
  <tr>
   <td>
     <div class="outer" id="o1-21">
     <i class="fa fa-plane fa-5x"></i>
     </div>
     <div class="inner" id="i1-21">
      <ul>
        <li>  
	  <a href="https://www.google.com/patents/US821393" target="_blank">Flying machine
	  </a>
	</li>
        <li>May 22, 1906</li>
      </ul>
     </div>
   </td>
   <td>
     <div class="outer" id="o1-22">
     <i class="fa fa-file fa-5x"></i>
     </div>
     <div class="inner" id="i1-22">
      <ul>
        <li>  
	  <a href="https://www.google.com/patents/US6285999" target="_blank">Method for node ranking in a linked database
	  </a>
	</li>
        <li>Sep 4, 2001</li>
      </ul>
     </div>
   </td>
  </tr>
  <tr>
   <td>
     <div class="outer" id="o1-31">
     <i class="fa fa-wifi fa-5x"></i>
     </div>
     <div class="inner" id="i1-31">
      <ul>
        <li>  
	  <a href="https://www.google.com/patents/US676332" target="_blank">Apparatus for wireless telegraphy
	  </a>
	</li>
        <li>Jun 11, 1901s</li>
      </ul>
     </div>
   </td>
   <td>
     <div class="outer" id="o1-32">
     <i class="fa fa-medkit fa-5x"></i>
     </div>
     <div class="inner" id="i1-32">
      <ul>
        <li>  
	  <a href="https://www.google.ch/patents/US8930044" target="_blank">Multi-part navigation process by an unmanned aerial vehicle for navigating to a medical situatiion
	  </a>
	</li>
        <li>6 Jan 2015</li>
      </ul>
     </div>
   </td>
  </tr>
</table>

We calculated their shortest paths and Eureka, they are all interconnected connected. The below graph shows these connections. In 16 steps you can get from the PageRank algorithm to the flying machine of the Wright brothers.

<div id="parent" style="width: 1000px; height: 400px;">
  <div id="network-sinks" style="position: absolute; height: 650px; width: 1000px"></div>
</div>

We are fascinated by the PageRank patent and keep going. Below the shortest paths of the PageRank patent to 7 handpicked patents are shown. PageRank, the source of the graph, is in red and the 7 sinks are shown in black. The farther away from PageRank the lighter the color of the node. This visualization shows how complexly intertwined the net spanning around PageRank is. 

<div id="parent" style="width: 900px; height: 750px;">
  <div id="network-shortestpaths" style="position: absolute; height: 675px; width: 950px"></div>
</div>

One more step. One of the classic ways to traverse a graph is using the breadth-first search algorithm and runs in $$\mathcal{O}(V + E)$$. The below gif shows the nodes, which are discovered when starting at the PageRank node. The breadth-first search is limited to distance 3 in order to allow a visualization. Every still of the gif represents a year, starting in 2001, when the PageRank patent was granted, and ending in 2016. 

<img src="static/pagerank_bfs.gif" style="border-style: none" />

Mesmerized, by the result, we decided to do the same thing for Steve Wozniak's above mentioned patent, which led to the Apple II computer. As above every frame represents a year, but this time the gif starts in 1979:

<img src="static/apple2.gif" style="border-style: none" />

## In-degrees

So far the patents, that we looked at where hand curated. How could we find important patents methodologically? In our opinion a natural way of ranking the most import patents is by the number of incoming citations. Graph tool provides a function [get_in_degrees](https://graph-tool.skewed.de/static/doc/graph_tool.html#graph_tool.Graph.get_in_degrees), that returns an array containing the in-degrees of the vertex list. Below on the x-axis, the number of incoming citations is displayed and on the y-axis, the number of patents, which have the corresponding number of incoming citations. 

<div id="wrapper" style="height: 750px; width: 750px;">
  <canvas id="plot-citations-indegree" width="300px" height="300px"></canvas>
</div>

More than 1.4 million patents have not been cited so far! The data does not fall exponentially however. There is a very heavy tail, but the data is not distributed according to a power law. The math and reasoning behind these conclusions can be found in the above cited python notebook. 

Here are the 3 patents with the highest in-degrees and their corresponding number of in-degrees:
<table>
  <tr>
   <td style="text-align:center;font-size:350%;width:30%;">
     <div>3709</div>
   </td>
   <td style="width:70%;">
     <div>
     <a target="_blank" href="https://www.google.com/patents/US4683202">Process for amplifying nucleic acid sequences</a>
     </div>
   </td>
  </tr>
  <tr>
    <td style="text-align:center;font-size:350%;width:30%;">
      <div>3342</div>
    </td>
    <td style="width:70%;">
      <div>
        <a target="_blank" href="https://www.google.ch/patents/US5523520">Mutant dwarfism gene of petunia</a>
      </div>
    </td>
  </tr>
  <tr>
    <td style="text-align:center;font-size:350%;width:30%;">
      <div>3188</div>
    </td>
    <td style="width:70%;">
      <div>
        <a target="_blank" href="https://www.google.com/patents/US4683195">Process for amplifying, detecting, and/or-cloning nucleic acid sequences</a>
      </div>
    </td>
  </tr>
</table>

All along this blog we have been talking about PageRank. Let’s apply PageRank to our graph! But first a short description of PageRank. PageRank was developed by Sergey Brin and Larry Page at Stanford and was influenced by citation analysis developed by Eugene Garfield at the University of Pennsylvania. Similarly to looking at in-degrees, the underlying assumption is that more important websites are likely to receive more links from other websites, but PageRank additionally tries to assess the importance of the node, from which the citation is coming from. For more details please refer to the [Wikipedia](https://en.wikipedia.org/wiki/PageRank) article on PageRank. The complexity of the PageRank algorithm is $$\mathcal{O}(V + E)$$.

Here are the 3 patents with the highest score using PageRank:

<table>
  <tr>
   <td style="text-align:center;font-size:350%;width:30%;">
     <div>1.</div>
   </td>
   <td style="width:70%;">
     <div>
     <a target="_blank" href="https://www.google.com/patents/US4237224">Process for producing biologically functional molecular chimeras</a>
     </div>
   </td>
  </tr>
  <tr>
    <td style="text-align:center;font-size:350%;width:30%;">
      <div>2.</div>
    </td>
    <td style="width:70%;">
      <div>
        <a target="_blank" href="https://www.google.ch/patents/US3813316">Microorganisms having multiple compatible degradative energy-generating plasmids and preparation thereof</a>
      </div>
    </td>
  </tr>
  <tr>
    <td style="text-align:center;font-size:350%;width:30%;">
      <div>3.</div>
    </td>
    <td style="width:70%;">
      <div>
        <a target="_blank" href="https://www.google.ch/patents/US4309756">Method of automatically evaluating source language logic condition sets and of compiling machine executable instructions directly therefrom</a>
      </div>
    </td>
  </tr>
</table>

Let’s compare the two methods of assessing importance of patents. Looking solely at the in-degrees doesn’t take into account the importance of the incoming citation. One could imagine, that being cited by 1000 nodes, which all have zero in-degree would imply, that the innovation provided is actually not that great. On the other hand PageRank favors older patents, because new patents are cited by patents, that are maybe too young to have incoming citations and therefore have low PageRank. In any case the two methods produce very different results. In the first 1000 patents only 98 appear in both list and in the first hundred merely 9 appear in both lists.

<table>
  <tr>
   <td style="text-align:center;font-size:350%;width:40%;">
     <div id="comp1">1.</div>
   </td>
   <td style="text-align:center;font-size:350%;width:60%;">
     <div id="comp2">
     hello
     </div>
   </td>
  </tr>
</table>

Let's take a look at the patents that score high in both chosen methods of assessing importance. We decided to take their mean, that is to say the rank from the in-degree method plus the rank from PageRank divided by two. Here are the six highest rated patents, that appear in both lists:

$$ \text{mean} = \frac{(\text{rank in-degrees} + \text{rank PageRank})}{2}$$



<table id="icon-grid">
  <tr>
   <td>
     <div class="outer" id="o2-11">
     <i class="fa fa-plus fa-5x"></i>
     </div>
     <div class="inner" id="i2-11">
      <ul>
        <li>  
	  <a href="https://www.google.com/patents/US4683202" target="_blank">Process for amplifying nucleic acid sequences
	  </a>
	</li>
        <li>Apr 25, 1961</li>
      </ul>
     </div>
   </td>
   <td>
     <div class="outer" id="o2-12">
     <i class="fa fa-search fa-5x"></i>
     </div>
     <div class="inner" id="i2-12">
      <ul>
        <li>  
	  <a href="https://www.google.com/patents/US4683195" target="_blank">Process for amplifying, detecting, and/or-cloning nucleic acid sequences
	  </a>
	</li>
        <li>	Sep 24, 2009</li>
      </ul>
     </div>
   </td>
  </tr>
  <tr>
   <td>
     <div class="outer" id="o2-21">
     <i class="fa fa-leaf fa-5x"></i>
     </div>
     <div class="inner" id="i2-21">
      <ul>
        <li>  
	  <a href="https://www.google.ch/patents/US5523520" target="_blank">Mutant dwarfism gene of petunia
	  </a>
	</li>
        <li>May 22, 1906</li>
      </ul>
     </div>
   </td>
   <td>
     <div class="outer" id="o2-22">
     <i class="fa fa-flask fa-5x"></i>
     </div>
     <div class="inner" id="i2-22">
      <ul>
        <li>  
	  <a href="https://www.google.ch/patents/US4849774" target="_blank">Bubble jet recording method and apparatus in which a heating element generates bubbles in a liquid flow path to project droplets
	  </a>
	</li>
        <li>Sep 4, 2001</li>
      </ul>
     </div>
   </td>
  </tr>
  <tr>
   <td>
     <div class="outer" id="o2-31">
     <i class="fa fa-file-image-o fa-5x"></i>
     </div>
     <div class="inner" id="i2-31">
      <ul>
        <li>  
	  <a href="https://www.google.ch/patents/US5572643" target="_blank">Web browser with dynamic display of information objects during linking
	  </a>
	</li>
        <li>Jun 11, 1901s</li>
      </ul>
     </div>
   </td>
   <td>
     <div class="outer" id="o2-32">
     <i class="fa fa-tint fa-5x"></i>
     </div>
     <div class="inner" id="i2-32">
      <ul>
        <li>  
	  <a href="https://www.google.com/patents/US4463359" target="_blank">Droplet generating method and apparatus thereof
	  </a>
	</li>
        <li>6 Jan 2015</li>
      </ul>
     </div>
   </td>
  </tr>
</table>

The overall highest scoring patent was filed by the biochemist Kary Banks Mullis, who later won a Nobel Prize in recognition of his improvement of the [polymerase chain reaction](https://en.wikipedia.org/wiki/Polymerase_chain_reaction).

This is the end of our short tour around the United States patents graph. We would like to emphasize, that uniquely patents filed in the United States were considered. We also in no way want to imply, that all important human innovations get patented and thus appear in this graph. 
Penicillin is a famous example of a discovery, that never got patented and it has brought enormous change to human kind.


To round of this blog we would like to show you one last gif. This time it is the patent containing the Diffie-Hellman key exchange called [Cryptographic apparatus and method](https://www.google.com/patents/US4200770), which is at the foundation of modern information security. This patent is so deeply connected, that it was impossible to show three layers of breadth-first search. So here are two layers:

<img src="static/diffie_hellman.gif" style="border-style: none" />

And as a nugget for the reader who stayed with us until the end, check out this [patent](https://docs.google.com/viewer?url=patentimages.storage.googleapis.com/pdfs/US4022227.pdf).
Thanks for visiting !

## Authors

* Roman Bachmann
* Michael Allemann
* Andrea Caforio
