<style>

</style>

<template>
    <div>
        <panel
            v-if="displayPanel"
            icon="mdi-arrow-collapse-vertical"
            :title="$t('Tuning.BedScrewsAdjustCalibrationPanel.PanelHeader')"
            :collapsible="true"
            card-class="bedscrewsadjustcalibration-panel"
        >
            <v-card v-if='displayPanel &&  displayBedScrewPanel' class='mb-6'>
                <v-card-text class='px-0 py-0'>
                    <v-container>
                        <v-row dense class='mb-1'>
                            <v-col class='pb-0 text-center'>
                                <v-btn small @click='doSendGCode("_BEDSCREW_CALIBRATION_START")' ><v-icon small class='mr-2'>mdi-play-circle</v-icon> Start</v-btn>
                            </v-col>
                            <v-col class='pb-0 text-center'>
                                <v-btn small @click='doSendGCode("_BEDSCREW_CALIBRATION_ACCEPT")' ><v-icon small class='mr-2'>mdi-check-circle</v-icon> Accept</v-btn>
                            </v-col>
                            <v-col class='pb-0 text-center'>
                                <v-btn small @click='doSendGCode("_BEDSCREW_CALIBRATION_ADJUSTED")' ><v-icon small class='mr-2'>mdi-adjust</v-icon> Adjusted</v-btn>
                            </v-col>
                            <v-col class='pb-0 text-center'>
                                <v-btn small @click='doSendGCode("_BEDSCREW_CALIBRATION_ABORT")' ><v-icon small class='mr-2'>mdi-close-circle</v-icon> Abort</v-btn>
                            </v-col>
                        </v-row>
                        <!-- <v-row dense class='mb-1'>
                            <v-col class='col text-center d-flex flex-column align-center flex-sm-row justify-center'>
                                <v-btn-toggle dense no-gutters class='mx-2 mt-3 mt-sm-0 order-last flex-nowrap order-sm-first' >
                                    <v-btn small @click='doSendGCode("_BEDSCREW_TESTZ Z=-.01")' ><v-icon small class='mr-2'>mdi-arrow-collapse-down</v-icon> -0.01mm</v-btn>
                                    <v-btn small @click='doSendGCode("_BEDSCREW_TESTZ Z=-.1")' >-0.1mm</v-btn>
                                    <v-btn small @click='doSendGCode("_BEDSCREW_TESTZ Z=-1")' >-1mm</v-btn>
                                </v-btn-toggle>
                                <v-btn-toggle dense no-gutters class='mx-2 order-first flex-nowrap order-sm-last' >
                                    <v-btn small @click='doSendGCode("_BEDSCREW_TESTZ Z=.01")' ><v-icon small class='mr-2'>mdi-arrow-expand-up</v-icon> +0.01mm</v-btn>
                                    <v-btn small @click='doSendGCode("_BEDSCREW_TESTZ Z=.1")' >+0.1mm</v-btn>
                                    <v-btn small @click='doSendGCode("_BEDSCREW_TESTZ Z=1")' >+1mm</v-btn>
                                </v-btn-toggle>
                            </v-col>
                        </v-row> -->
                        <v-row dense class='mb-1'>
                            <v-col class='pb-0 text-center'>
                                <v-btn small @click='doSendGCode("_BEDSCREW_CALIBRATION_SAVE")' ><v-icon small class='mr-2'>mdi-content-save</v-icon> Save</v-btn>
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

export default class BedScrewsAdjustCalibrationPanel extends Mixins(BaseMixin) {

    get displayPanel() {
        if (!this.klippyIsConnected) return false
        return (['standby', 'complete', 'cancelled'].includes(this.printer_state))
    }

    get displayBedScrewPanel()
    {
        //console.log('Kannan:' + this.$store.getters['printer/checkConfig']('bed_screws') ?? false )
        return  this.$store.getters['printer/checkConfig']('bed_screws') ?? false
    }

    doSendGCode(gcode: string) {
        this.doSend(gcode)
    }

    doSend(gcode: string) {
        this.$store.dispatch('server/addEvent', { message: gcode, type: 'command' })
        this.$socket.emit('printer.gcode.script', { script: gcode })
    }
}
</script>