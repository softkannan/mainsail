<style>

</style>

<template>
    <div>
        <panel
            v-if="displayPanel"
            icon="mdi-arrow-collapse-vertical"
            :title="$t('Tuning.ManualBedCalibrationPanel.PanelHeader')"
            :collapsible="true"
            card-class="manualbedcalibration-panel"
        >
            <v-card v-if='displayPanel' class='mb-6'>
                <v-card-text class='px-0 py-0'>
                    <v-container>
                        <v-row dense class='mb-1'>
                                    <v-col cols='3'>
                                        <v-btn class='btnMinWidthAuto fill-width'
                                                @click='doSendMove("_MANUAL_CALIB_MOVE_LEFTDEEP")'
                                        >
                                            <v-icon>mdi-adjust</v-icon>
                                        </v-btn>
                                    </v-col>
                                    <v-col cols='3'></v-col>
                                    <v-col cols='3'>
                                        <v-btn class='btnMinWidthAuto fill-width'
                                                @click='doSendMove("_MANUAL_CALIB_MOVE_RIGHTDEEP")'
                                        >
                                            <v-icon>mdi-adjust</v-icon>
                                        </v-btn>
                                    </v-col>
                                    <v-col cols='3'>
                                        <v-btn class='btnMinWidthAuto fill-width'
                                                @click='doSendMove("_MANUAL_CALIB_START")'
                                        >
                                            <v-icon>mdi-play-circle</v-icon>
                                            Start
                                        </v-btn>
                                    </v-col>
                                </v-row>
                                <v-row dense>
                                    <v-col cols='3'></v-col>
                                    <v-col cols='3'>
                                        <v-btn class='btnMinWidthAuto fill-width'
                                                @click='doSendMove("_MANUAL_CALIB_MOVE_CENTER")'
                                        >
                                            <v-icon>mdi-adjust</v-icon>
                                        </v-btn>
                                    </v-col>
                                    <v-col cols='3'></v-col>
                                </v-row>
                                <v-row dense class='mb-1'>
                                    <v-col cols='3'>
                                        <v-btn class='btnMinWidthAuto fill-width'
                                                @click='doSendMove("_MANUAL_CALIB_MOVE_LEFTFRONT")'
                                        >
                                            <v-icon>mdi-adjust</v-icon>
                                        </v-btn>
                                    </v-col>
                                    <v-col cols='3'></v-col>
                                    <v-col cols='3'>
                                        <v-btn class='btnMinWidthAuto fill-width'
                                                @click='doSendMove("_MANUAL_CALIB_MOVE_RIGHTFRONT")'
                                        >
                                            <v-icon>mdi-adjust</v-icon>
                                        </v-btn>
                                    </v-col>
                                    <v-col cols='3'>
                                        <v-btn class='btnMinWidthAuto fill-width'
                                                @click='doSendMove("_MANUAL_CALIB_FINISH")'
                                        >
                                            <v-icon>mdi-stop-circle</v-icon>
                                            Finish
                                        </v-btn>
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

export default class ManualBedCalibrationPanel extends Mixins(BaseMixin) {

    get displayPanel() {
        if (!this.klippyIsConnected) return false

        return (['standby', 'complete', 'cancelled'].includes(this.printer_state))
    }

    doSendMove(gcode: string) {
        this.doSend(gcode)
    }

    doSend(gcode: string) {
        this.$store.dispatch('server/addEvent', { message: gcode, type: 'command' })
        this.$socket.emit('printer.gcode.script', { script: gcode })
    }

    doSendMacro(gcode: string) {
        this.$store.dispatch('server/addEvent', { message: gcode, type: 'command' })
        this.$socket.emit('printer.gcode.script', { script: gcode }, { loading: 'macro_'+gcode })
    }
}
</script>