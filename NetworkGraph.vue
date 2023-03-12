<template>
    <h2>Network Graph for Country FR on 01-01-2023</h2>
    <i>click on any Node for more details</i>
    <div ref="container">
    </div>

    <div class="details">
        <label v-if="nodeSelected">Details for <b>{{ nodeSelected.srcElement.__data__.name }}</b></label>
        <h3 v-if="nodeSelected">In : </h3>
        <div class="indiv" v-if="nodeSelected">
            <table v-if="inLinks.length > 0">
                <tr>
                    <th>Source</th>
                    <th>Target</th>
                </tr>
                <tr v-for="link in inLinks">
                    <td>{{ link.source.name }}</td>
                    <td>{{ link.target.name }}</td>
                </tr>
            </table>
            <p v-else>No links found for {{ nodeSelected.srcElement.__data__.name }}</p>
        </div>
        <h3 v-if="nodeSelected">Out : </h3>
        <div class="outdiv" v-if="nodeSelected">
            <table v-if="outLinks.length > 0">
                <tr>
                    <th>Source</th>
                    <th>Target</th>
                </tr>
                <tr v-for="link in outLinks">
                    <td>{{ link.source.name }}</td>
                    <td>{{ link.target.name }}</td>
                </tr>
            </table>
            <p v-else>No links found for {{ nodeSelected.srcElement.__data__.name }}</p>
        </div>
    </div>
</template>

<script>
import data from '../data.json';
import * as d3 from 'd3';



export default {
    data() {
        return {
            nodeSelected: null,
            inLinks: [],
            outLinks: [],
        }
    },
    mounted() {
        this.initGraph();
    },
    methods: {
        initGraph() {
            const width = 1000;
            const height = 500;
            const svg = d3.select(this.$refs.container)
                .append("svg")
                .attr("width", width)
                .attr("height", height);
            const simulation = d3.forceSimulation()
                .force("link", d3.forceLink().id(d => d.id))
                .force("charge", d3.forceManyBody().strength(-1500))
                .force("center", d3.forceCenter(width / 2, height / 2));
            const link = svg.append("g")
                .attr("stroke", "#999")
                .attr("stroke-opacity", 0.6)
                .selectAll("line")
                .data(data.links)
                .join("line");
            const node = svg.append("g")
                // .attr('stroke', '#fff')
                .attr("stroke-width", 1.5)
                .selectAll("g")
                .data(data.nodes)
                .join("g")
                .on('click', this.nodeClicked);
            node.append("circle")
                .attr("r", 20)
                .attr("cursor", 'pointer')
                .attr("fill", "rgb(59, 89, 152)");
            node.append("text")
                .text(d => d.name)
                .attr("font-size", 14)
                .attr("x", 12)
                .attr("y", 4);
            simulation.nodes(data.nodes).on("tick", () => {
                node.attr("transform", d => `translate(${d.x},${d.y})`);
                link.attr("x1", d => d.source.x)
                    .attr("y1", d => d.source.y)
                    .attr("x2", d => d.target.x)
                    .attr("y2", d => d.target.y);
            });
            simulation.force("link").links(data.links);
        },
        nodeClicked(d) {
            console.log(d.srcElement.__data__.id);
            const inLinks = data.links.filter(link => link.target.id === d.srcElement.__data__.id);
            const outLinks = data.links.filter(link => link.source.id === d.srcElement.__data__.id);
            this.nodeSelected = d;
            this.inLinks = inLinks;
            this.outLinks = outLinks;
            console.log(inLinks);
            console.log(outLinks);
        }

    }
};



</script>

<style>
.container h2 {
    font-style: italic;
}

.link.selected {
    stroke: red;
    stroke-width: 3px;
}
.indiv{
    display: flex;
    justify-content: center;
    margin-bottom: 50px;
}
.outdiv{
    display: flex;
    justify-content: center;
}
table, th, td {
  border: 1px solid white;
  border-collapse: collapse;
  padding: 10px;
}
th, td {
  background-color: #96D4D4;
}
label{
    font-size: 2rem;
}
</style>