<template>
  <p>
    Graph goes here
  </p>
  <div id="my_dataviz"></div>
</template>

<script>
import * as d3 from "d3";

export default {
  name: "GraphView",
  mounted() {
    console.log("GraphView mounted");
    // alert("here 1")
    // set the dimensions and margins of the graph
    var margin = {top: 10, right: 30, bottom: 30, left: 40},
      width = 400 - margin.left - margin.right,
      height = 400 - margin.top - margin.bottom;

    // append the svg object to the body of the page
    var svg = d3.select("#my_dataviz")
    .append("svg")
      .attr("width", width + margin.left + margin.right)
      .attr("height", height + margin.top + margin.bottom)
    .append("g")
      .attr("transform",
            "translate(" + margin.left + "," + margin.top + ")");

    console.log("d3 compute start");
    console.log(d3);

    d3.json("data_network.json").then(data => {


//create a network from an edgelist

var simulation = d3.forceSimulation()
					//add nodes
					.nodes(data.nodes);

simulation
    .force("charge", d3.forceManyBody())
    .force("center", d3.forceCenter(width / 2, height / 2));

//draw circles for the nodes 
var node = svg
        .selectAll("circle")
        .data(data.nodes)
        .enter()
        .append("circle")
        .attr("r", 10)
        .attr("fill", "green");  

const drag = d3
    .drag()
    .on("start", dragstart)
    .on("drag", dragged);

node.call(drag).on("click", click);

//add tick instructions: 
simulation.on("tick", tickActions );

simulation.force("link", d3.forceLink()                               // This force provides links between nodes
                .id(function(d) { return d.id; })                     // This provide  the id of a node
                .links(data.links).distance(100));                                    // and this the list of links

//draw lines for the links 
var link = svg
    .selectAll("line")
    .data(data.links)
    .enter().append("line")
      .attr("stroke-width", 2)
      .style("stroke", "#aaa");        

function tickActions() {
    //update circle positions each tick of the simulation 
    node
        .attr("cx", function(d) { return d.x+6; })
        .attr("cy", function(d) { return d.y-6; });
        
    //update link positions 
    //simply tells one end of the line to follow one node around
    //and the other end of the line to follow the other node around
    link
        .attr("x1", function(d) { return d.source.x; })
        .attr("y1", function(d) { return d.source.y; })
        .attr("x2", function(d) { return d.target.x; })
        .attr("y2", function(d) { return d.target.y; });

  }  
  
  function click(event, d) {
    console.log("click called")
    delete d.fx;
    delete d.fy;
    //d3.select(this).classed("fixed", false);
    simulation.alpha(1).restart();
  }

  function dragstart() {
    console.log("dragstart called")
    // d3.select(this).classed("fixed", true);
  }

  function dragged(event, d) {
    console.log("dragged called")
    d.fx = clamp(event.x, 0, width);
    d.fy = clamp(event.y, 0, height);
    simulation.alpha(1).restart();
  }

 function clamp(x, lo, hi) {
   return x < lo ? lo : x > hi ? hi : x;
 }

    console.log("d3 compute end");
    });
 
  },
  unmounted() {
    console.log("GraphView unmounted")
    // alert("here 2")
  },
  created() {
    console.log("GraphView created")
    // alert("here 3")
  },
}
</script>

<style scoped>
a {
  color: #42b983;
}
</style>
