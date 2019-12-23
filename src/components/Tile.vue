<template>
  <div class="meter-container" :style="cssVars">
    <div :id=id></div>
    <Percentage :items=items :color=hexColor :total=total />
  </div>
</template>

<script>
import * as d3 from "d3";
import Percentage from './Percentage.vue';

const amountTypes = {
    NUMBER: 'number',
    MONEY: 'money'
};

export default {
    name: "Tile",
    props: {
        tile: Object
    },
    components: {
        Percentage
    },
    data() {
        return {
            id: 'tile-container-'+ this.getRandomNumberBetween(1000, 9999),
            title: '',
            color: '',
            hexColor: '#ffffff',
            total: 0,
            amountType: amountTypes.NUMBER,
            items: [],
            width: 100,
            height: 100,
            twoPi: 2 * Math.PI,
            progress: 0,
            allocated: 0,
            formatPercent: d3.format(".0%")
        };
    },
    mounted() {
        this.title = this.tile.title;
        this.color = this.tile.color || 'green';
        this.amountType = Object.values(amountTypes).find(o => o === this.tile.amountType) || amountTypes.NUMBER;
        this.items = this.tile.amounts;
        this.getColors();
        
        const radius = Math.floor((this.width - 1)/2);

        const arc = d3.arc()
            .startAngle(0)
            .innerRadius(radius-3)
            .outerRadius(radius);
        
        const selection = d3.select('#'+this.id);

        const svg = selection.append("svg")
            .attr("width", this.width)
            .attr("height", this.height)
            .append("g")
            .attr("transform", "translate(" 
                + this.width / 2 
                + "," 
                + this.height / 2 
                + ")");

        const meter = svg.append("g")
            .attr("class", "funds-allocated-meter");

        if(this.items){
            this.items.map((item, index) => {
                this.total += Math.round(item.quantity);
                if(index > 0) this.allocated = item.quantity;
            });
        }

        meter.append("path")
            .attr("class", "background")
            .attr("d", arc.endAngle(this.twoPi));
        
        const foreground = meter.append("path")
            .attr("class", "foreground");
        
        meter.append("text")
            .attr("text-anchor", "middle")
            .attr("class", "percent-complete")
            .attr("dy", "-0.75em")
            .text(this.title);

        meter.append("text")
            .attr("text-anchor", "middle")
            .attr("class", "description")
            .attr("dy", "0.7em")
            .text(this.formatNum(this.total, this.amountType));

        const i = d3.interpolate(this.progress, this.allocated / this.total);

        selection.transition().duration(1000).tween("progress", () => {
            return (t) => {
                this.progress = i(t);
                foreground.attr("d", arc.endAngle(this.twoPi * this.progress));
            };
        });

    },
    methods: {
        getRandomNumberBetween(min, max) {
            return Math.floor(Math.random() * (max - min) + min);
        },
        formatNum(num, amountType = amountTypes.NUMBER) {
            let suffix = '';
            switch(amountType){
                case amountTypes.MONEY:
                    suffix = '€';
                    break;
                default:
                    suffix = '';
                    break;
            }
            return String(Math.floor(num)).replace(/(?<!\..*)(\d)(?=(?:\d{3})+(?:\.|$))/g, '$1.') + suffix;
        },
        getColors() {
            switch(this.color) {
                case 'blue':
                    this.hexColor = { 
                        PRIMARY_COLOR: '#294758',
                        SECONDARY_COLOR: '#71c2d5'
                    };
                    break;
                case 'red':
                    this.hexColor = { 
                        PRIMARY_COLOR: '#a9512f',
                        SECONDARY_COLOR: '#e8ba4f'
                    };
                    break;
                default: // green
                    this.hexColor = { 
                        PRIMARY_COLOR: '#3c5924',
                        SECONDARY_COLOR: '#8fcc57'
                    };
                    break;
            }
        }
    },
    computed: {
        cssVars() {
            return {
                '--primary-color': this.hexColor.PRIMARY_COLOR,
                '--secondary-color': this.hexColor.SECONDARY_COLOR
            };
        }
    }
}
</script>

<style lang="scss">
.meter-container {
    max-width: 160px;
    width: 100%;
    display: flex-item;
    padding: 15px 46px;
}

.funds-allocated-meter {
  .background {
    fill: var(--secondary-color);
  }
  .foreground {
    fill: var(--primary-color);
  }
  text {
    &.percent-complete {
      font-family: 'Roboto', sans-serif;
      font-weight: 800;
      font-size: 0.7em;
      fill: #a7a7a7;
      letter-spacing: -.03em;
      text-transform: uppercase;
    }
    &.description {
      font-family: 'Roboto', sans-serif;
      font-weight: 600;
      font-size: 0.9em;
      fill: #5d5d5d;
    }
  }
}
</style>