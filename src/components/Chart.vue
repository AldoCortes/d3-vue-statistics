<template>
    <div class="chart">
        <Tile v-for="item in tiles" :tile=item :key=item.title />
    </div>
</template>

<script>
import Tile from "./Tile.vue";

export default {
    name: "Chart",
    props: [
        'statistics',
    ],
    components: {
        Tile
    },
    data() {
        return {
            height: 600,
            width: 600,
            tiles: []
        }
    },
    created() {
        if(this.statistics.tiles)
            this.tiles = this.statistics.tiles;
    }
}
</script>

<style>
.chart {
    display: flex;
    width: 100%;
    height: auto;
    align-items: center;
    justify-content: center;
    flex-wrap: wrap;
}
</style>