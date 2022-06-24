<style>

</style>

<template>
    <div>
        <panel
            v-if="displayPanel"
            icon="mdi-dip-switch"
            :title="$t('Tuning.MiscCalibrationPanel.PanelHeader')"
            :collapsible="true"
            card-class="misccalibration-panel"
        >
            <v-card v-if='displayPanel' class='mb-6'>
                <v-card-text class='px-0 py-0'>
                    <v-container>
                        <v-row dense class='mb-1'>
                            <v-col class='pb-0 text-center'>
                               
                            </v-col>
                            <v-col class='pb-0 text-center'>
                            </v-col>
                        </v-row>
                        <v-row dense class='mb-1'>
                            <v-col class='pb-0 text-center'>
                               
                            </v-col>
                            <v-col class='pb-0 text-center'>
                            </v-col>
                        </v-row>
                        <v-row dense class='mb-1'>
                            <v-col class='pb-0 text-center'>
                            </v-col>
                        </v-row>
                    </v-container>
                </v-card-text>
            </v-card>
        </panel>
    </div>
</template>

<script lang='ts'>

import {Component, Mixins} from 'vue-property-decorator'
import BaseMixin from '../mixins/base'
import Panel from '@/components/ui/Panel.vue'
@Component({
    components: {Panel}
})

export default class MiscCalibrationPanel extends Mixins(BaseMixin) {

    private hotendPIDTemp: any = 205
    private bedPIDTemp: any = 55

    get displayPanel() {
        if (!this.klippyIsConnected) return false

        return (['standby', 'complete', 'cancelled'].includes(this.printer_state))
    }

    doSendGCode(gcode: string) {
        this.doSend(gcode)
    }

    doCalibrate(pidToolName: string) {
        if(pidToolName == 'Bed')
        {
            this.doSend('_PIDTUNE HEATER=heater_bed TARGET=' + this.bedPIDTemp)
        }
        else
        {
            this.doSend('_PIDTUNE HEATER=extruder TARGET=' + this.hotendPIDTemp)
        }
    }

    doSend(gcode: string) {
        this.$store.dispatch('server/addEvent', { message: gcode, type: 'command' })
        this.$socket.emit('printer.gcode.script', { script: gcode })
    }
}
</script>