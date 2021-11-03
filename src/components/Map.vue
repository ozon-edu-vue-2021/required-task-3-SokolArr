<template>
  <div class="map">
    <h3>Карта офиса</h3>

    <div
      v-if="!isLoading"
      class="map-root"
    >
      <!-- map -->
      <MapSVG ref="svg" />

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

    this.svg = d3.select(this.$refs.svg);
    this.tableSVG = d3.select(this.$refs.table);

    this.g = this.svg.select("g"); //получили <g>
    if (this.g) {
      this.drawTables();
    } else {
      console.log("Error no <g>");
    }
    console.log(this.svg, this.g);
  },
  methods: {
    drawTables() {
      //Создаем группу для рабочих мест:
      const svgTablesGroup = this.g.append("g").classed("groupPlaces", true);
      //Добавили новый <g> в конец svg и дали класс = groupPlaces
      //-------------------
      console.log(svgTablesGroup);

      this.tables.map((table) => {


        //Этап добавления и размещения рабочего места:
        const tmpTable = svgTablesGroup
          .append("g")
          .attr("transform", `translate(${table.x},${table.y}),scale(0.5)`)
          .attr("id", table._id)
          .classed("employer-place", true);

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
    }
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
