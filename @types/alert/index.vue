<style lang="sass">
  @import "~fancy_style"
  @import "~fancy_mixins"

  .fancy-alert
    &.fc-mask
      @include mixin-mask()
    &:focus
      outline: 0
    > div
      position: absolute
      top: 50%
      left: 50%
      max-width: 90%
      max-height: 90%
      min-width: 16rem
      display: flex
      flex-direction: column
      border-radius: $borderRadius
      overflow: hidden
      background-color: #fff
      transform: translate3d( -50%, -50%, 0)
      .fc-msg
        text-align: center
        padding: $space * 4 0
      .fc-btn
        cursor: pointer
        text-align: center
        margin: 0
        line-height: $form-height
        background: $colorTheme
        color: #fff
        &:hover
          opacity: 0.8

    @media #{$device-phone}
      > div
        min-width: 60%
    @media #{$device-pad}
      > div
        min-width: 40%

</style>

<template lang="pug">
  .fancy-alert(tabindex="1",:class="{'fc-mask':cfg.ismask}")
    div
      .fc-msg(v-html="cfg.message")
      .fc-btn(v-if="cfg.confirm" @click="onClick()") {{cfg.confirm}}
</template>

<script lang="ts">
import { Component, Vue, Prop } from 'vue-property-decorator'

export interface IFancyAlert {
  message?: string
  confirm?: string
  ismask?: boolean
  onConfirm?: Function,
  __name?: string,
}

const options: IFancyAlert = {
  message: '',
  confirm: 'ok',
  ismask: true,
  onConfirm() {},
  __name: 'alert'
}

export default class Alert extends Vue {
  @Prop()
  cfg?: any

  created() {
    Object.keys(options).forEach((i: string) => this.cfg.hasOwnProperty(i) || this.$set(this.cfg, i, (<any>options)[i]))
    document.addEventListener('keydown', this.keydown, false)
  }

  protected mounted() {
    this.$el.focus()
  }

  protected destroyed() {
    document.removeEventListener('keydown', this.keydown, false)
  }

  private keydown(e: KeyboardEvent) {
    // enter space center esc
    if ([13, 32, 100, 27].includes(e.keyCode)) {
      e.preventDefault()
      e.stopPropagation()
      return this.onClick()
    }
  }
  private async onClick() {
    try {
      this.cfg.onConfirm && (await this.cfg.onConfirm())
      this.cfg.__name && ((<any>this).$parent[this.cfg.__name] = false)
    } catch (e) {}
  }
}
</script>

