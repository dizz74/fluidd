<template>
  <collapsable-card
    :title="$t('app.timelapse.title.timelapse_settings')"
    icon="$cog"
    class=""
    :draggable="false"
  >
    <app-setting
      :title="$t('app.timelapse.setting.enable')"
      :sub-title="subtitleIfBlocked(enabledBlocked)"
      :r-cols="2"
    >
      <v-switch
        v-model="enabled"
        hide-details
        :disabled="enabledBlocked"
        @click.native.stop
      />
    </app-setting>

    <v-divider />
    <app-setting
      :title="$t('app.timelapse.setting.auto_render')"
      :sub-title="subtitleIfBlocked(autoRenderBlocked)"
      :r-cols="2"
    >
      <v-switch
        v-model="autoRender"
        hide-details
        :disabled="autoRenderBlocked"
        @click.native.stop
      />
    </app-setting>

    <v-divider />
    <app-setting :title="$tc('app.timelapse.title.render_settings')">
      <app-btn
        outlined
        small
        color="primary"
        @click="$emit('openRenderDialog', false)"
      >
        <v-icon
          small
          left
        >
          $pencil
        </v-icon>
        {{ $t('app.general.btn.edit') }}
      </app-btn>
    </app-setting>
  </collapsable-card>
</template>

<script lang="ts">
import { Component, Mixins } from 'vue-property-decorator'
import StateMixin from '@/mixins/state'
import AppSetting from '@/components/ui/AppSetting.vue'
import { TimelapseSettings } from '@/store/timelapse/types'
import { SocketActions } from '@/api/socketActions'

@Component({
  components: { AppSetting }
})
export default class TimelapseSettingsCard extends Mixins(StateMixin) {
  get enabledBlocked () {
    return this.$store.getters['timelapse/isBlockedSetting']('enabled')
  }

  get enabled () {
    return this.settings?.enabled
  }

  set enabled (value: boolean) {
    SocketActions.machineTimelapseSetSettings({ enabled: value })
  }

  get autoRenderBlocked () {
    return this.$store.getters['timelapse/isBlockedSetting']('autorender')
  }

  get autoRender () {
    return this.settings?.autorender
  }

  set autoRender (value: boolean) {
    SocketActions.machineTimelapseSetSettings({ autorender: value })
  }

  get frameCount () {
    return this.$store.getters['timelapse/getLastFrame']?.count
  }

  get settings (): TimelapseSettings {
    return this.$store.getters['timelapse/getSettings']
  }

  subtitleIfBlocked (blocked: boolean): string {
    return blocked ? this.$tc('app.general.tooltip.managed_by_moonraker') : ''
  }
}
</script>
