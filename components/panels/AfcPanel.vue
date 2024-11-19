<template>
    <div class="afc-panel">
        <h1>AFC Spools</h1>
        <div id="spools-container">
            <div v-for="(unitData, unit, index) in spoolData" :key="index" :id=unit class="unit">
                <h4>Unit {{ unit }}</h4>
                <div v-for="(laneData, lane, index2) in unitData" :key="index2" :id=lane class="spool">
                    <h4>Lane {{ lane }}</h4>
                    <p><strong>Material:</strong> {{ laneData.material || "N/A" }}</p>
                    <p><strong>Color:</strong> {{ laneData.color || "N/A" }}</p>
                    <p><strong>Status:</strong> {{ laneData.load ? "Loaded" : "Not Loaded" }}</p>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import Component from 'vue-class-component'
    import { Mixins } from 'vue-property-decorator'
    import BaseMixin from '../mixins/base'
    import ConnectionStatus from '../ui/ConnectionStatus.vue'
    import Panel from '@/components/ui/Panel.vue'
    import {
        mdiRestart,
        mdiDownload,
        mdiMessageOutline,
        mdiAlertOutline,
        mdiRocketLaunch,
        mdiConnection,
        mdiPrinter3d,
        mdiPower,
    } from '@mdi/js'
    
    export default {
        name: 'AfcPanel',
        data() {
            return {
                spoolData: [],
                intervalId: null,
            };
        },
        async mounted() {
            await this.fetchSpoolData();
            this.intervalId = setInterval(this.fetchSpoolData, 10000); // Refresh data every 10 seconds
        },
        beforeDestroy() {
            if (this.intervalId) {
                clearInterval(this.intervalId);
            }
        },
        methods: {
            async fetchSpoolData() {
                this.spoolData = this.extractLaneData(this.$store.state.printer.AFC);
                console.log(this.spoolData);
            },
            extractLaneData(spools) {
                const { ['system']: removedKey, ...units } = spools;
                for (const key in units) {
                    delete units[key]['system'];
                }
                return units;
            },
        },
    };
</script>

<style scoped>
.afc-panel {
  font-family: Arial, sans-serif;
  background-color: #f0f0f0;
  color: #333;
  line-height: 1.6;
  margin: 0;
  padding: 20px;
}

h1 {
  text-align: center;
  margin-bottom: 20px;
}

#spools-container {
  display: flex;
  gap: 10px;
}

.spool {
  flex: 1;
  padding: 15px;
  background-color: #fff;
  border-radius: 5px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

.spool h4 {
  margin-bottom: 10px;
  color: #333;
}

.spool p {
  margin: 5px 0;
  color: #666;
}

.unit h4 {
    margin-bottom: 10px;
    color: #333;
}

.unit p {
    margin: 5px 0;
    color: #666;
}
</style>
