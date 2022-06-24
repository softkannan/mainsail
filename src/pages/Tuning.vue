<template>
    <v-row>
        <v-col class='col-sm-12 col-md-5'>
            <status-panel></status-panel>
            <min-settings-panel></min-settings-panel>
            <moonraker-state-panel></moonraker-state-panel>
            <klippy-state-panel></klippy-state-panel>
            <manual-bed-calibration-panel></manual-bed-calibration-panel>
            <bed-screws-adjust-calibration-panel></bed-screws-adjust-calibration-panel>
            <z-endstop-calibration-panel></z-endstop-calibration-panel>
            <pid-calibration-panel></pid-calibration-panel>
            <esteps-calibration-panel></esteps-calibration-panel>
            <misc-calibration-panel></misc-calibration-panel>
        </v-col>
        <v-col class='col-sm-12 col-md-7'>
            <miniconsole-panel></miniconsole-panel>
        </v-col>
    </v-row>
</template>

<script lang='ts'>

import Component from 'vue-class-component'
import {Mixins} from 'vue-property-decorator'
import BaseMixin from '@/components/mixins/base'
import MinSettingsPanel from '@/components/panels/MinSettingsPanel.vue'
import KlippyStatePanel from '@/components/panels/KlippyStatePanel.vue'
import MoonrakerStatePanel from '@/components/panels/MoonrakerStatePanel.vue'
import StatusPanel from '@/components/panels/StatusPanel.vue'
import MiniconsolePanel from '@/components/panels/MiniconsolePanel.vue'
import ZEndstopCalibrationPanel from '@/components/panels/ZEndstopCalibrationPanel.vue'
import ManualBedCalibrationPanel from '@/components/panels/ManualBedCalibrationPanel.vue'
import BedScrewsAdjustCalibrationPanel from '@/components/panels/BedScrewsAdjustCalibrationPanel.vue'
import MiscCalibrationPanel from '@/components/panels/MiscCalibrationPanel.vue'
import PidCalibrationPanel from '@/components/panels/PidCalibrationPanel.vue'
import EstepsCalibrationPanel from '@/components/panels/EstepsCalibrationPanel.vue'

@Component({
    components: {
        MiniconsolePanel,
        StatusPanel,
        MoonrakerStatePanel,
        KlippyStatePanel,
        MinSettingsPanel,
        ZEndstopCalibrationPanel,
        ManualBedCalibrationPanel,
        BedScrewsAdjustCalibrationPanel,
        PidCalibrationPanel,
        EstepsCalibrationPanel,
        MiscCalibrationPanel
    }
})
export default class PageTuning extends Mixins(BaseMixin) {

   
}
</script>