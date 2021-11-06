<template>
  <div id="app">
    <div class="office">
      <Map
        @tappedOnTable="tappedOnTable"
        @clickedOutside="closeMe"
        v-if="true"
      />

      <SideMenu
        v-if="true"
        :is-user-openned="isTapped"
        :person="person"
        @closeMe="closeMe"
        ref="sideMenu"
      />
    </div>
    <!--    <div v-click-outside="closeMe"></div>-->
  </div>
</template>

<script>
import Map from "./components/Map.vue";
import SideMenu from "./components/SideMenu.vue";
import people from "@/assets/data/people.json";

import ClickOutside from "vue-click-outside";

export default {
  name: "App",
  data() {
    return {
      isTapped: false,
      person: null,
      people: null
    };
  },
  components: {
    Map,
    SideMenu
  },
  mounted() {
    this.people = people;
  },
  methods: {
    tappedOnTable(tableId) {
      //console.log(tableId);
      if (tableId <= 12) {
        this.person = this.people.find((person) => person._id === tableId);
      } else {
        this.person = null;
      }
      this.isShowChart = false;
      this.isTapped = true;
    },
    closeMe() {
      this.isTapped = false;
      //this.$refs.sideMenu.makeChart();
    }
  },
  directives: {
    ClickOutside
  }

};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  color: #2c3e50;
  background-color: #fafafa;
  padding: 24px;
  box-sizing: border-box;
}

html,
body,
#app {
  height: 100%;
}

* {
  box-sizing: border-box;
}

h3 {
  margin-top: 0;
}

.office {
  display: grid;
  grid-template-columns: 1fr 320px;
  border-radius: 6px;
  border: 1px solid #ccd8e4;
  height: 100%;
  background: white;
  max-width: 1500px;
  margin: 0 auto;
}
</style>
