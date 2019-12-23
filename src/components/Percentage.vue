<template>
  <div class="perc-holder" :style="cssVars">
      <div v-for="(item, key) in items" :key=key class="item">
        <div class="title">
            {{ item.name}}
        </div>
        <div class="numbers">
            <div class="percentage">{{ getPercent(item.quantity) }}%</div>
            <div class="current">{{ item.quantity }}</div>
        </div>
        <div class="spacer"></div>
      </div>
  </div>
</template>

<script>
export default {
    name: "Percentage",
    props: [
        'items',
        'color',
        'total'
    ],
    methods: {
        getPercent(num){
            return Math.floor((parseInt(num) / this.total)*100);
        }
    },
    computed: {
        cssVars() {
            return {
                '--primary-color': this.color.PRIMARY_COLOR,
                '--secondary-color': this.color.SECONDARY_COLOR
            };
        }
    }
}
</script>

<style lang="scss">
.perc-holder{
    display: block;
    justify-content: space-between;
    width: 100%;
    height: auto;
    padding: 6px 0 10px;
    list-style: none;

    .spacer {
        width:100%;
        height: 2px;
        clear: both;
    }

    .item {
        text-align: left;
        font-family: 'Roboto', sans-serif;
        font-size: 0.6em;
        float: left;
        width: 50%;

        .title {
            font-weight: 800;
            width:100%;
            display: flex;
            color: var(--secondary-color);
        }

        .numbers {
            display: flex;
            border-bottom: 1px solid #e3e3e3;
            padding-bottom: 5px;
            padding-top: 3px;
            
            .percentage {
                display: inline-block;
                width:25%;
                font-weight: 600;
            }
            
            .current {
                display: inline-block;
                width:75%;
                font-family: 'Roboto Condensed', sans-serif;
                font-weight: 200;
                font-size: 0.3em;
                padding-left: 5px;
            }
        }

        &:last-child {
            text-align: right;
            .title {
                justify-content: flex-end;
                color: var(--primary-color);
            }
        }
    }
}
</style>