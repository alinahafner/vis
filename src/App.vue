<template>
  <div id="app">
    <div id="header">
      <div id="div1">
        <h1>BookVisualizer - Anne of Green Gables </h1>
      </div>
    </div>
    <h3>Book Statistics</h3>
    <div id="main1"><img :src="anne" :width="150" :height="150"/>
      <div id="textinfo">
        <div id="list1">
          <ul>
            <li>Title: Anne of Green Gables</li>
            <li>Author: Lucy Maud Montgomery</li>
            <li>Published in 1908</li>
            <li>Suitable for 5th and 6th grade</li>
            <li>Estimated reading time: 9 hours</li>
          </ul>
        </div>
        <div id="list2">
          <ul>
            <li>Pages: 381</li>
            <li>Total number of words: 108321</li>
            <li>Chapter length (avg.): 10 pages</li>
            <li>Sentence length (avg.): 16 words</li>
            <li>Main settings: Avonlea, Green Gables and White Sands</li>
          </ul>
        </div>
      </div>
      <div id="difflevel"><h4>Readability</h4>
        <ReadabilityPieChart></ReadabilityPieChart>
      </div>
      <div id="conversational"><h4>Conversational Activity</h4>
        <QuotationPieChart></QuotationPieChart>
      </div>
      <div id="fantasy"><h4>Fantasy Level</h4>
        <FantasyScore></FantasyScore>
      </div>
    </div>
    <h3>Characters</h3>
    <div id="main2">
      <BarChart></BarChart>
      <NetworkDiagram v-on:changeCharacter="characterChange($event)"></NetworkDiagram>
      <WordCloudCharacter :characterKey="characterKey" @changeCharacter="characterChange"></WordCloudCharacter>
    </div>
    <h3>Topics</h3>
    <div id="main3">
      <div id="submain3">
        <div id="submain3-buttons">
          <button
              class="toggle"
              @click="toggle"
              :class="[showBubbleChart ? 'btn-active' : 'btn-inactive']"
          >Intertopic Distance Map
          </button>
          <button
              class="toggle"
              @click="toggle"
              :class="[!showBubbleChart ? 'btn-active' : 'btn-inactive']"
          >Top Terms per Topic
          </button>
        </div>
        <div id="bubble-chart-container" :class="[showBubbleChart ? 'chart-active' : 'chart-inactive']">
          <BubbleChart :chapterKey="chapterKey" @changeChapter="chapterChange"
                       v-on:topic="getTopic($event)"></BubbleChart>
        </div>
        <div id="circle-pack-container" :class="[!showBubbleChart ? 'chart-active' : 'chart-inactive']">
          <CirclePack :chapterKey="chapterKey" @changeChapter="chapterChange"
                      @changeTopicFromCirclePack="getTopic($event)"></CirclePack>
        </div>
      </div>
      <WordCloudChapter v-on:changeChapter="chapterChange($event)" :topicKey="topicKey"
                        @changeTopicFromWordCloud="getTopic($event)"
                        ></WordCloudChapter>
    </div>
  </div>
</template>

<script>
import NetworkDiagram from "@/components/NetworkDiagram";
import BarChart from "@/components/BarChart";
import WordCloudChapter from "@/components/WordCloudChapter";
import BubbleChart from "@/components/BubbleChart";
import CirclePack from "./components/CirclePack.vue";
import WordCloudCharacter from "@/components/WordCloudCharacter";
import ReadabilityPieChart from "@/components/ReadabilityPieChart";
import QuotationPieChart from "@/components/QuotationPieChart";
import FantasyScore from "@/components/FantasyScore";

export default {
  name: 'App',
  props: {selected: String},
  data() {
    return {
      anne: require('./assets/anne.svg'),
      showBubbleChart: true,
      chapterKey: "",
      characterKey: {},
      topicKey: "",
    };
  },
  components: {
    WordCloudChapter,
    BarChart,
    BubbleChart,
    NetworkDiagram,
    CirclePack,
    WordCloudCharacter,
    ReadabilityPieChart,
    QuotationPieChart,
    FantasyScore

  },
  methods: {
    toggle(e) {
      if (!e.target.classList.contains('btn-active')) {
        this.showBubbleChart = !this.showBubbleChart;
      }
    },
    chapterChange(event) {
      this.chapterKey = event;
    },
    characterChange(event) {
      this.characterKey = event;
    },
    getTopic(event) {
      this.topicKey = event;
    }
  },
}
</script>

<style>
@import url('https://fonts.googleapis.com/css?family=PT+Sans');

body {
  /* font-family: 'PT Sans', sans-serif; */
  background-color: #eee;
}

#app {
  background: #F7F0F0;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #18A999;
  overflow-x: hidden;
  overflow-y: hidden;
}


#div1 {
  background: #109648;
  padding: 5px;
  width: auto;
  height: 6px;
  display: inline-block;
  flex-direction: column;
  justify-content: left;
  text-align: center;
  line-height: 4px;
  margin-bottom: 10px;
}

#header {
  padding: 10px;
  background: #109648;
  margin-bottom: 10px;
}

h1 {
  color: #F7F0F0;
  text-align: center;
  font-size: 28px;
}

h3 {
  color: #18A999;
  text-align: left;
  font-size: 20px;
  margin-left: 20px;
  margin-bottom: 0px;
}

h4 {
  margin-bottom: 0px;
  margin-top: 0px;
}

h5 {
  margin: 0 auto 10px auto;
}
#main1 {
  display: grid;
  row-gap: 0px;
  grid-template-columns: repeat(5, 1fr);
  grid-gap: 0px;
  grid-auto-rows: minmax(100px, auto);
  grid-column: 3/3;
  grid-row: 1;
  margin-bottom: 10px;
  margin-top: 4px;
  border-bottom: 2px solid #bdbdbd;
  padding: 5px;
}

#main2 {
  display: grid;
  row-gap: 0px;
  grid-template-columns: repeat(3, 1fr);
  grid-gap: 0px;
  grid-auto-rows: minmax(100px, auto);
  grid-column: 3/3;
  grid-row: 1;
  /* display: flex;
  flex-direction: row;*/
  margin-bottom: 10px;
  margin-top: 4px;
  border-bottom: 2px solid #bdbdbd;
  padding: 5px;
}

#main3 {
  display: grid;
  row-gap: 5px;
  row-gap: 5px;
  grid-template-columns: repeat(2, 1fr);
  grid-gap: 5px;
  grid-auto-rows: minmax(100px, auto);
  grid-column: 2/2;
  grid-row: 1;
  margin-bottom: 10px;
  margin-top: 4px;
  padding: 5px;
}

#submain3 {
  margin-bottom: 5px;
}

#submain3-buttons button {
  border: 1px solid #bbc1be;
  font-size: 14px;
  font-weight: bold;
  padding: 5px 12px;
  cursor: pointer;
  transition: 0.3s;
  box-shadow: 5px 5px 10px rgba(0, 0, 0, 0.05);
}

#submain3-buttons button:first-child {
  border-radius: 5px 0 0 5px;
}

#submain3-buttons button:last-child {
  border-radius: 0 5px 5px 0;
}

#submain3-buttons button:not(:last-child) {
  border-right: none;
}

#submain3-buttons button:hover {
  background-color: #18A999; /* #3e8e41; */
  color: white;
}

#bubble-chart-container {
  padding: 5px 0;
}

.btn-active {
  background-color: #18A999; /* #3e8e41; */
  color: white;
}

.btn-inactive {
  background-color: white;
  color: #18A999; /* #3e8e41; */
}

.chart-active {
  display: block;
}

.chart-inactive {
  display: none;
}


.arrow {
  width: 120px;
  position: relative;
}

.line {
  margin-top: 14px;
  width: 90px;
  background: blue;
  height: 10px;
  float: left;
}

.point {
  width: 0;
  height: 0;
  border-top: 20px solid transparent;
  border-bottom: 20px solid transparent;
  border-left: 30px solid blue;
  float: right;
}

ul {
  list-style-type: "📗";
  margin-top: 0px;
  margin-left: 0px;
  margin: 0px;
}

li {
  text-align: left;
  padding-left: 4px;
  font-size: 14px;
}

#textinfo {
  display: grid;
  row-gap: 1px;
  grid-template-columns: repeat(2, 1fr);
  grid-gap: 0px;
  grid-auto-rows: minmax(100px, auto);
  grid-column: 2/2;
  grid-column-gap: 0px;
  grid-row: 1;
  margin-bottom: 0px;
  margin-top: 0px;
  width: 700px;
  margin-left: 0px;
  margin-right: 0px;
  padding-left: 0px;

}

#list1 {
  margin-left: 0px;
  margin-right: 0px;
  width: 250px;
}

#list2 {
  margin-left: 0px;
  margin-right: 0px;
  width: 500px;
}

#difflevel {
  margin-top: 0px;
}

#conversational {
  margin-top: 0px;
}

text {
  /* font-family: 'PT Sans', sans-serif; */
  font-weight: bold;
}
#fantasy {
  margin-top: 0px;
}


</style>