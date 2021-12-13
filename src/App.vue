<template>
  <Header :header="this.header" />
  <div class="content-container">
    <section class="section-container" id="missions" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/mission-icon.svg" />
        <h1>Mission Log</h1>
      </div>
      <div class="section-content-container">
        <h3>Current Assignment</h3>
        <Markdown :source="current_md" class="markdown" />
        <h3>Mission List</h3>
        <div class="mission-list-container">
          <Mission
            v-for="item in this.missions"
            :key="item.slug"
            :mission="item"
            :selected="this.mission_slug"
            @click="selectMission(item)"
          />
        </div>
      </div>
    </section>
    <section class="section-container" id="events" style="width:435px; height:714px;">
      <div class="section-header clipped-medium-backward">
        <img src="/icons/events-icon.svg" />
        <h1>Events Log</h1>
      </div>
      <div class="section-content-container">
        <Markdown :source="events" class="markdown" />
      </div>
    </section>
    <section class="section-container" id="pilots" style="width:894px; height:714px;">
      <div style="height:52px; overflow:hidden;">
        <div class="section-header clipped-medium-backward-pilot">
          <img src="/icons/pilot-icon.svg" />
          <h1>Pilot Roster</h1>
        </div>
        <div class="rhombus-back">&nbsp;</div>
      </div>
      <div class="section-content-container">
        <div class="pilot-list-container">
          <Pilot v-for="item in this.pilots" :key="item.slug" :pilot="item" />
        </div>
      </div>
    </section>
  </div>
  <svg
    style="visibility: hidden; position: absolute;"
    width="0"
    height="0"
    xmlns="http://www.w3.org/2000/svg"
    version="1.1"
  >
    <defs>
      <filter id="round">
        <feGaussianBlur in="SourceGraphic" stdDeviation="5" result="blur" />
        <feColorMatrix
          in="blur"
          mode="matrix"
          values="1 0 0 0 0  0 1 0 0 0  0 0 1 0 0  0 0 0 19 -5"
          result="goo"
        />
        <feComposite in="SourceGraphic" in2="goo" operator="atop" />
      </filter>
    </defs>
  </svg>
  <audio autoplay>
    <source src="/startup.ogg" type="audio/ogg" />
  </audio>
  <Footer/>
</template>

<script>
import Header from './components/layout/Header.vue';
import Footer from './components/layout/Footer.vue';
import Mission from './components/Mission.vue';
import Pilot from './components/Pilot.vue';
import Markdown from 'vue3-markdown-it';

export default {
  components: {
    Header,
    Footer,
    Mission,
    Pilot,
    Markdown
  },

  data() {
    return {
      "mission_slug": "itp",
      "current_md": "",
      "events": "",
      "missions": [
        {
          "slug": "itp",
          "name": "Landfall",
          "status": "start"
        },
      ],
      "pilots": [
        {
          "callsign": "Alicanto",
          "alias": "Principe Desremault-Americ of the House of Blades",
          "code": "1a201c0f-5628-4e15-8db8-a698220c18a4//a1b59f43-bd8b-40e4-aff8-ce8e37b35e17",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Sudden Intervention"
        },
        {
          "callsign": "Behemoth",
          "alias": "Rhaegar Barronox",
          "code": "2697cd8a-54ae-4498-b390-db1215f02502//32843f37-cdd5-4483-8bef-afd909c211fb",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Atreska's Wrath"
        },
        {
          "callsign": "Carrion",
          "alias": "Chester Adrias",
          "code": "4d4eaf6a-f7c2-4022-a6a0-5919842713f0//0ee5c210-c102-4bcc-bb25-6daf9ddba552",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Kanaloa"
        },
        {
          "callsign": "Jarl",
          "alias": "Derrick Othel",
          "code": "fa318f0b-4033-4443-93be-b9fbb154127a//1f22fb25-0334-4105-823c-c9890697e63f",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Vulture"
        },
        {
          "callsign": "Wiglaf",
          "alias": "Vaggur Jorgensen",
          "code": "c681b44e-a900-4b5b-a979-69aa0f05524f//63ec3413-d1fb-482d-82f3-4240a9a2bd20",
          "corpro": "GMS",
          "frame": "Everest",
          "mech": "Yggdrasil"
        },
      ],
      "header": {
        "planet": "Hercynia",
        "year": "5014u",
        "system": "Ardennes-3",
        "gate": "Atlas-Quanokrim",
        "ring": "Atlas-Line",
        "headerTitle": "Starspear",
        "headerSubtitle": "Mercenary Company",
        "subheaderTitle": "Crisis Response",
        "subheaderSubtitle": "Sierra-Mike-Charlie",
      },
      "options":{
        "eventsMarkdownPerMission": true
      }
    }
  },

  created() {
    this.loadMissionMarkdown()
    this.loadEventsMarkdown()
  },

  computed: {

  },

  methods: {
    selectMission(mission) {
      this.mission_slug = mission.slug;
      this.loadMissionMarkdown()
      if(this.options.eventsMarkdownPerMission){
        this.loadEventsMarkdown();
      }
    },
    loadMissionMarkdown() {
      let self = this;
      let md = `/missions/${self.mission_slug}.md`
      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.current_md = client.responseText;
      }
      client.send();
    },
    loadEventsMarkdown() {
      let self = this;
      let md = "";

      if(self.options.eventsMarkdownPerMission){
        md = `/events/${self.mission_slug}.md`
      }
      else {
        md = "/events.md"
      }

      var client = new XMLHttpRequest();
      client.open('GET', md);
      client.onreadystatechange = function () {
        self.events = client.responseText;
      }
      client.send();
    }
  }

}
</script>


<style lang="scss">
#app {
  width: 1902px;
  height: 910px;
  overflow: hidden;
}
</style>
