<style>

</style>

<template>
    <div>
        <panel
            v-if="displayPanel"
            icon="mdi-thermometer-lines"
            :title="$t('Tuning.PidCalibrationPanel.PanelHeader')"
            :collapsible="true"
            card-class="pidcalibration-panel"
        >
            <v-card v-if='displayPanel && existpid_calibrate' class='mb-6'>
                <v-card-text class='px-0 py-0'>
                    <v-container>
                        <v-row dense class='mb-1'>
                            <v-col class='pb-0 text-center'>
                                <v-text-field
                                        :label='selectedHeater != null ? "[" + selectedHeater.name + "] PID Tune" : "PID Tune"'
                                        :rules='[()=> heaterPIDTemp > selectedHeater.min_temp && heaterPIDTemp < selectedHeater.max_temp || "temperature value must be supported by heater" ]'
                                        suffix='°C'
                                        type='number'
                                        v-model='heaterPIDTemp'
                                        outlined
                                        dense
                                        hide-details='auto'
                                    ></v-text-field>
                            </v-col>
                            <v-col>
                                <v-select
                                    :items='getHeaters'
                                    label='heaters'
                                    v-model='selectedHeater'
                                    item-text='name'
                                    return-object
                                    single-line
                                    outlined
                                    dense
                                    ></v-select>
                            </v-col>
                            <v-col class='pb-0 text-center'>
                                <v-btn @click='doCalibrate' >
                                    <v-icon>mdi-tune</v-icon>
                                    Calibrate
                                </v-btn>
                            </v-col>
                        </v-row>
                        <v-row dense class='mb-1'>
                            <v-col class='pb-0 text-center'>
                                <v-btn small @click='doSendGCode("_PID_CALIBRATION_SAVE")' ><v-icon small class='mr-2'>mdi-content-save</v-icon> Save</v-btn>
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
import {CommandHelp} from '@/store/printer/types'
import Panel from '@/components/ui/Panel.vue'
@Component({
    components: {Panel}
})

export default class PidCalibrationPanel extends Mixins(BaseMixin) {

    private computedHeaterPIDTemp: any = 205
    private selectedHeater: any = null

    created() {
        
    }

    get heaterPIDTemp() {
        return this.computedHeaterPIDTemp
    }
    set heaterPIDTemp(value) {
        console.log(value)    
    }
    
    get filamentNames() 
    {
        return [
            'Programming',
            'Design',
            'Vue',
            'Vuetify'
        ]
    }

    get displayPanel() {
        if (!this.klippyIsConnected) return false

        return (['standby', 'complete', 'cancelled'].includes(this.printer_state))
    }

    get helplist() {
        return this.$store.state.printer.helplist ?? []
    }

    get existpid_calibrate() {
        return this.helplist.findIndex((gcode: CommandHelp) => gcode.commandLow === 'pid_calibrate') !== -1
    }

    get getHeaters()
    {
        //var retVal: string[] = ['extruder','heater_bed']
        // this.$store.getters['printer/getHeaters'].forEach(element: => {
        //     retVal.concat(element.name)
        // })
        var heaters = this.$store.getters['printer/getHeaters']
        if(heaters.length > 0 && this.selectedHeater == null)
        {
            console.log(heaters[0])
            this.selectedHeater = heaters[0]
        }
        return heaters
    }

    doSendGCode(gcode: string) {
        this.doSend(gcode)
    }

    doCalibrate(){

    }

    doCaluibrate(pidToolName: string) {
        if(pidToolName == 'Bed') {
            //this.doSend('_PIDTUNE HEATER=heater_bed TARGET=' + this.bedPIDTemp)
        }
        else {
            //this.doSend('_PIDTUNE HEATER=extruder TARGET=' + this.hotendPIDTemp)
        }
    }

    doSend(gcode: string) {
        this.$store.dispatch('server/addEvent', { message: gcode, type: 'command' })
        this.$socket.emit('printer.gcode.script', { script: gcode })
    }
}
</script>