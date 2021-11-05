<template>
  <div class="map">
    <h3>Карта офиса</h3>

    <div
      v-if="!isLoading"
      class="map-root"
    >
      <!-- map -->
      <MapSVG ref="svg"

      />
      <!--      v-click-outside="clickedOutside"-->

      <!-- test table -->
      <TableSVG
        v-show="false"
        ref="table"
      />

    </div>
    <div v-else>Loading...</div>
  </div>
</template>

<script>
import MapSVG from "@/assets/images/map.svg";
import TableSVG from "@/assets/images/workPlace.svg";

import tables from "@/assets/data/tables.json";
import legend from "@/assets/data/legend.json";

import ClickOutside from "vue-click-outside";

import * as d3 from "d3";

export default {
  components: {
    MapSVG,
    TableSVG
  },
  data() {
    return {
      isLoading: false,
      svg: null,
      tableSVG: null,
      g: null,
      tables: [],
      legend: []
    };
  },
  mounted() {
    this.tables = tables;
    this.legend = legend;

    this.svg = d3.select(this.$refs.svg).classed("map-svg", true);

    this.tableSVG = d3.select(this.$refs.table);

    this.g = this.svg.select("g"); //получили <g> из svg

    if (this.g) {
      this.drawTables();
    } else {
      console.log("Error no <g>");
    }

    // document.addEventListener("click", () => {
    //   const empPlace = document.getElementsByClassName("wrapper-table");
    //   let arrEmpPlaces = [];
    //   for (let index = 0; index < empPlace.length; index++) {
    //     arrEmpPlaces[index] = empPlace.item(index);
    //   }
    //
    //   // if (!(arrEmpPlaces.includes(evt.target))) {
    //   //   console.log("click outside");
    //   // } else {
    //   //   console.log("click inside");
    //   // }
    // });

    document.addEventListener("click", (evt) => {
      const el = document.getElementsByClassName("map-svg");
      let tmpArr = [];

      for (let index = 0; index < el.length; index++) {
        tmpArr[index] = el.item(index);
      }

      if (tmpArr.includes(evt.target)) {
        //console.log("clicked on map");
        this.clickedOutside();
      } else {
        //console.log("another click");
      }
    });

  },
  methods: {
    drawTables() {
      //Создаем группу для рабочих мест:
      const svgTablesGroup = this.g.append("g").classed("groupPlaces", true);
      //Добавили новый <g> в конец svg и дали класс = groupPlaces
      //-------------------

      this.tables.map((table) => {
        //Этап добавления и размещения рабочего места:
        const tmpTable = svgTablesGroup
          .append("g")
          .attr("transform", `translate(${table.x},${table.y}),scale(0.5)`)
          .attr("id", table._id)
          .classed("employer-place", true)
          .on("click", () => {
            this.$emit("tappedOnTable", table._id);
          });
        //Этап добавления и размещения рабочего стола:
        tmpTable
          .append("g")
          .attr("transform", `rotate(${table.rotate || 0})`)
          .attr("group_id", table.group_id)
          .html(this.tableSVG.html());

        // Этап изменения рабочего стола:
        tmpTable
          .attr("fill", legend.find((legendEl) => legendEl.group_id === table.group_id)?.color ?? "transparent");
      });
    },

    clickedOutside() {
      this.$emit("clickedOutside");
    }
  },
  directives: {
    ClickOutside
  }
};
</script>

<style scoped>
.map {
  height: 100%;
  width: 100%;
  padding: 24px;
  overflow: hidden;
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
}

.map-root {
  height: 100%;
  width: 100%;
  overflow: hidden;
  box-sizing: border-box;
}

h3 {
  margin-top: 0px;
}

::v-deep svg {
  height: 100%;
  width: 100%;
}

::v-deep .table {
  cursor: pointer;
}
</style>
