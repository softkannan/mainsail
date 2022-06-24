<style>

</style>

<template>
    <div>
        <panel
            v-if="displayPanel"
            icon="mdi-dip-switch"
            :title="$t('Tuning.EstepsCalibrationPanel.PanelHeader')"
            :collapsible="true"
            card-class="misccalibration-panel"
        >
            <v-card v-if='displayPanel && initPanel' class='mb-6'>
                <v-card-text class='px-0 py-0'>
                    <v-container>
                        <v-row dense class='mb-1'>
                            <v-col class='pb-0 text-center'>
                                <v-text-field
                                    label='Current "rotation_distance"'
                                    suffix='mm'
                                    type='number'
                                    v-model='previous_rotation_distance'
                                    outlined
                                    dense
                                    :readonly='true'
                                    hide-details='auto'
                                ></v-text-field>
                            </v-col>
                        </v-row>
                        <v-row dense class='mb-1'>
                            <v-col class='pb-0 text-center'>
                                <v-text-field
                                        label='Mark filament distance'
                                        suffix='mm'
                                        type='number'
                                        v-model='initial_mark_distance'
                                        :rules='[() => initial_mark_distance > requested_extrude_distance || "initial mark distance must be greater than the requested extrude distance"]'
                                        outlined
                                        dense
                                        hide-details='auto'
                                        hint='using marker mark the filament in given distance from known distance'
                                    ></v-text-field>
                            </v-col>
                            <v-col class='pb-0 text-center'>
                                <v-text-field
                                        label='Request extrude distance'
                                        suffix='mm'
                                        type='number'
                                        v-model='requested_extrude_distance'
                                        :rules='[() => requested_extrude_distance < initial_mark_distance || "value must be less than initial mark distance"]'
                                        outlined
                                        dense
                                        hide-details='auto'
                                        hint='must be smaller than marked distance'
                                    ></v-text-field>
                            </v-col>
                        </v-row>
                        <v-row dense class='mb-1'>
                            <v-col class='pb-0 text-center'>
                                <v-combobox
                                    v-model='linewidth'
                                    :items='[0.4,0.5,0.6,0.8,1]'
                                    label='Linewidth'
                                    outlined
                                    dense
                                    ></v-combobox>
                            </v-col>
                            <v-col class='pb-0 text-center'>
                                <v-combobox
                                    v-model='layer_height'
                                    :items='[0.1,0.2,0.24,0.28,0.3,0.32,0.4]'
                                    label='Layer Height'
                                    outlined
                                    dense
                                    ></v-combobox>
                            </v-col>
                        </v-row>
                        <v-row dense class='mb-1'>
                            <v-col class='pb-0 text-center'>
                                <v-slider
                                    dense
                                    label='Extrusion volumetric speed (mm3/sec):'
                                    v-model='extrude_volumetric_speed'
                                    thumb-label='always'
                                    min='1'
                                    max='50'
                                    :hint='filamentSpeed'
                                    :persistent-hint='true'
                                    ></v-slider>
                            </v-col>
                        </v-row>
                        <v-row dense class='mb-1'>
                            <v-col class='pb-0 text-center'>
                                <v-spacer></v-spacer>
                            </v-col>
                        </v-row>
                        <v-row dense class='mb-1'>
                            <v-col class='pb-0 text-center'>
                                <v-text-field
                                        label='Temperature'
                                        suffix='°C'
                                        type='number'
                                        v-model='filament_temperature'
                                        outlined
                                        dense
                                        hide-details
                                    ></v-text-field>
                            </v-col>
                            <v-col class='pb-0 text-center'>
                                <v-btn @click='doPrepare()' >
                                    <v-icon>mdi-thermometer-lines</v-icon>
                                    Prepare
                                </v-btn>
                            </v-col>
                            <v-col class='pb-0 text-center'>
                                <v-btn @click='doExtrude()' >
                                    <v-icon>mdi-tune</v-icon>
                                    Extrude
                                </v-btn>
                            </v-col>
                        </v-row>
                        <v-row dense class='mb-1'>
                            <v-col class='pb-0 text-center'>
                                <v-text-field
                                        label='Remaining filament distance'
                                        suffix='mm'
                                        type='number'
                                        v-model='subsequent_mark_distance'
                                        outlined
                                        dense
                                        hide-details='auto'
                                        hint='after extude is completed then measure the remaing filament length till the mark and enter'
                                    ></v-text-field>
                            </v-col>
                            <v-col class='pb-0 text-center'>
                                <v-text-field
                                        label='Actual extruded distance'
                                        suffix='mm'
                                        type='number'
                                        :readonly='true'
                                        :value='actual_extrude_distance'
                                        outlined
                                        dense
                                        hide-details
                                    ></v-text-field>
                            </v-col>
                        </v-row>
                        <v-row dense class='mb-1'>
                            <v-col class='pb-0 text-center'>
                                <v-text-field
                                        label='New "rotation_distance"'
                                        suffix='mm'
                                        type='number'
                                        :readonly='true'
                                        :value='rotation_distance'
                                        outlined
                                        dense
                                        hide-details='auto'
                                        hint='note down this value and update the klipper config / use it on SET_EXTRUDER_STEP_DISTANCE'
                                    ></v-text-field>
                            </v-col>
                        </v-row>
                        <v-row dense class='mb-1'>
                            <v-col class='pb-0 text-center'>
                                <v-text-field
                                        label='Max printing speed for given linewidth and layerheight'
                                        suffix='mm/sec'
                                        type='number'
                                        :readonly='true'
                                        :value='maxSpeed'
                                        outlined
                                        dense
                                        hide-details='auto'
                                        hint='note down this value and update the klipper config / use it on SET_EXTRUDER_STEP_DISTANCE'
                                    ></v-text-field>
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
//import Componenthooks from 'vue-class-component/hooks'

@Component({
    components: {Panel}
})

export default class EstepsCalibrationPanel extends Mixins(BaseMixin) {

    private previous_rotation_distance: number = 0
    private initial_mark_distance: number = 120
    private requested_extrude_distance: number = 100
    private subsequent_mark_distance: number = 0
    private filament_temperature: number = 205
    private extrude_volumetric_speed: number = 4
    private linewidth: number = 0.4
    private layer_height: number = 0.2

    get actual_extrude_distance() {
        if(this.subsequent_mark_distance > 0 && this.requested_extrude_distance > 0 && this.initial_mark_distance > 0)
        {
            return this.initial_mark_distance - this.subsequent_mark_distance
        }
        else
        {
            return 0
        }
    }

    get rotation_distance() {

        if(this.previous_rotation_distance > 0 && this.initial_mark_distance > 0 && this.requested_extrude_distance > 0 && this.subsequent_mark_distance > 0)
        {
            var tempActualDistance = this.initial_mark_distance - this.subsequent_mark_distance
            return (this.previous_rotation_distance * (tempActualDistance / this.requested_extrude_distance)).toFixed(6)
        }
        else
        {
            return 0
        }
    }

    get displayPanel() {
        if (!this.klippyIsConnected) return false

        return (['standby', 'complete', 'cancelled'].includes(this.printer_state))
    }

    get initPanel()
    {
        this.previous_rotation_distance = this.$store.getters['printer/getPrinterConfigObjects']('extruder').extruder.rotation_distance

        return true
    }

    LinearSpeedToVolumetricSpeed(linearSpeed:number, filament_diameter: number)
    {
        var filament_radius = filament_diameter / 2
        var filament_area = Math.PI * (filament_radius * filament_radius)
        return linearSpeed * filament_area
    }

    VolumetricSpeedToLinearSpeed(volumetricSpeed:number, filament_diameter:number)
    {
        var filament_radius = filament_diameter / 2
        var filament_area = Math.PI * (filament_radius * filament_radius)
        return (1/filament_area) * volumetricSpeed
    }

    get filamentSpeed()
    {
        var filament_diameter = this.$store.getters['printer/getPrinterConfigObjects']('extruder').extruder.filament_diameter
        //var nozzle_diameter = this.$store.getters['printer/getPrinterConfigObjects']('extruder').extruder.nozzle_diameter
        var linearSpeedPerSecond = this.VolumetricSpeedToLinearSpeed(this.extrude_volumetric_speed,filament_diameter)
        var linearSpeedPerMinute = linearSpeedPerSecond * 60

        return 'Linear speed: ' + linearSpeedPerSecond.toFixed(0) + 'mm/sec ' + linearSpeedPerMinute.toFixed(0) + 'mm/min'
    }

    get maxSpeed()
    {
        var maxSpeed =  this.extrude_volumetric_speed / this.layer_height / this.linewidth
        return maxSpeed.toFixed(0)
    }

    get displayBedScrewPanel()
    {
        //console.log('Kannan:' + this.$store.getters['printer/checkConfig']('bed_screws') ?? false )
        return  this.$store.getters['printer/checkConfig']('bed_screws') ?? false
    }

    doSendGCode(gcode: string) {
        this.doSend(gcode)
    }
    
    doPrepare() {
        this.doSend('_ESTEP_CALIBRATION_PREPARE TEMP=' + this.filament_temperature.toFixed(0))
    }

    doExtrude() {
        
        var filament_diameter = this.$store.getters['printer/getPrinterConfigObjects']('extruder').extruder.filament_diameter
        var linearSpeedPerSecond = this.VolumetricSpeedToLinearSpeed(this.extrude_volumetric_speed,filament_diameter)
        var linearSpeedPerMinute = linearSpeedPerSecond * 60 

        this.doSend('_ESTEP_CALIBRATION_EXTRUDE LENGTH=' + this.requested_extrude_distance.toFixed(0) +' SPEED=' + linearSpeedPerMinute.toFixed(0))
    }

    doSend(gcode: string) {
        this.$store.dispatch('server/addEvent', { message: gcode, type: 'command' })
        this.$socket.emit('printer.gcode.script', { script: gcode })
    }
}
</script>