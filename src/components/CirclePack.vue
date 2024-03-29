<template>
  <div id="circle-pack"></div>
</template>

<script>
import * as d3 from "d3";
import termsData from "@/data/bubblechart/top_terms_arr_per_topic_7.json";

export default {
  props: {
    chapterKey: String
  },
  data: () => ({
    settings: {
      margin: { 
          top: 10, 
          right: 10, 
          bottom: 10, 
          left: 10
        },
      width:      200,
      height:     110,
      opacityCircles: 0.9,
      topicKey: ""
    }
  }),
  watch: {
    chapterKey: {
      deep: true,
      handler() {
        const chapter = parseInt(this.chapterKey);
        
        // If word cloud for the entire book is being shown:
        if(chapter === 0) {
          termsData.forEach(topic => d3.select(`#topicPack-${topic.topic}`)
                                                       .transition()
                                                       .duration(300)
                                                       .style("opacity", this.opacityCircles))
        } else {
            const matchTopic = termsData.find(topic => topic.chapters.includes(chapter)).topic;

            const noMatchTopics = termsData.filter(topic => !topic.chapters.includes(chapter));
            d3.select(`#topicPack-${matchTopic}`)
              .transition()
              .duration(300)
              .style("opacity", this.opacityCircles);

            noMatchTopics.forEach(topic => d3.select(`#topicPack-${topic.topic}`)
                        .transition()
                        .duration(300)
                        .style("opacity", 0.3))
        }
      }
    }
  },
  mounted() {
    this.init()
  },
  methods: {
    onChange(event, data) {
      d3.select(`#topicPack-${data}`)
        .style("opacity", this.opacityCircles);
      const noMatchTopics = termsData.filter(topic => topic.topic !== data);
      noMatchTopics.forEach(topic => d3.select(`#topicPack-${topic.topic}`)
                                       .style("opacity", 0.3))
          
      // Sync with Circle Packs
      d3.select(`#topic-${data}`)
        .style("opacity", this.settings.opacityCircles);
      noMatchTopics.forEach(topic => d3.select(`#topic-${topic.topic}`)
                                       .style("opacity", 0.3))
      
      this.$emit("changeTopicFromCirclePack", data);
    },

    init() {
      const packData = {name: "packData", children: []} 
      for (const topic of termsData) {
        packData.children.push({name: topic.topic, children: topic.terms.slice(0, 15)})
      }

      const pack = data => d3.pack()
                            .size([this.settings.width, this.settings.height])
                            .padding(1)(d3.hierarchy(data)
                                        // sort circle packs descendingly according to sum of all children's values
                                          .sum(d => d.value)
                                          .sort((a, b) => b.value - a.value)) 

      const root = pack(packData);
      let focus = root;
      let view;

      const circlePackSvg = d3.select("#circle-pack").append("svg")
      .attr("viewBox", `-${this.settings.width/2} -${this.settings.height/2} ${this.settings.width} ${this.settings.height}`)
      .style("display", "block")
      .style("margin", "0 -5px")
      .style("cursor", "pointer")
      .on("click", (event) => zoom(event, root));

      const node = circlePackSvg.append("g")
      .selectAll("circle")
      .data(root.descendants().slice(1))
      .join("circle")
        .attr("id", d => `topicPack-${d.data.name}`)
        .attr("fill", d => d.children ? "#bbc1be" : "white")
        .attr("pointer-events", d => !d.children ? "none" : null)
        .on("mouseover", function() { d3.select(this).attr("stroke", "gray"); })
        .on("mouseout", function() { d3.select(this).attr("stroke", null); })
        .on("click", (event, d) => {
          focus !== d && (zoom(event, d), event.stopPropagation())

          this.onChange(event, d.data.name)
        });

    const topicNames = {
      1: "Friends & love", 
      2:"School activities", 
      3: "Fashion & shows", 
      4: "Anne's emotional world", 
      5: "Life at Green Gables", 
      6: "Incidents with the Barrys", 
      7: "Mistakes & apologies"
    }
    
    const label = circlePackSvg.append("g")
        .attr("id", "circle-labels")
        .style("font", "5px sans-serif")
        .attr("pointer-events", "none")
        .attr("text-anchor", "middle")
      .selectAll("text")
      .data(root.descendants())
      .join("text")
        .style("fill-opacity", d => d.parent === root ? 1 : 0)
        .style("display", d => d.parent === root ? "inline" : "none")
        .text(d => Object.keys(topicNames).includes(d.data.name.toString()) ? 
                               topicNames[d.data.name] : `${d.data.name} (${d.data.value})` );

    zoomTo([root.x, root.y, root.r * 2]);

    function zoomTo(v) {
      const k = 120 / v[2];

      view = v;

      label.attr("transform", d => `translate(${(d.x - v[0]) * k},${(d.y - v[1]) * k})`);
      node.attr("transform", d => `translate(${(d.x - v[0]) * k},${(d.y - v[1]) * k})`);
      node.attr("r", d => d.r * k);
    }

    function zoom(event, d) {
      // eslint-disable-next-line no-unused-vars
      const focus0 = focus;

      focus = d;

      const transition = circlePackSvg.transition()
          .duration(event.altKey ? 7500 : 750)
          // eslint-disable-next-line no-unused-vars
          .tween("zoom", d => {
            const i = d3.interpolateZoom(view, [focus.x, focus.y, focus.r * 2]);
            return t => zoomTo(i(t));
          });

      label
        .filter(function(d) { return d.parent === focus || this.style.display === "inline"; })
        .transition(transition)
          .style("fill-opacity", d => d.parent === focus ? 1 : 0)
          .on("start", function(d) { if (d.parent === focus) this.style.display = "inline"; })
          .on("end", function(d) { if (d.parent !== focus) this.style.display = "none"; });
    }
    }
  }
}
</script>

<style>
#circle-pack {
  overflow: hidden;
}
 #circle-labels text {
    font-size: 60%;
  }
</style>