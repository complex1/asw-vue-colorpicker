<template>
  <span>
    <span @click="openPicker">
      <slot></slot>
    </span>
    <span id="color_picker"></span>
  </span>
</template>

<script>
import './index.scss'
import Picker from './vanilla-picker'
export default {
  props: {
    value: {
      type: String,
      default: '#ffffff'
    },
    colorList: {
      type: Array,
      default: () => { return [] }
    },
    popup: {
      type: Boolean,
      default: true
    }
  },
  data: () => {
    return {
      colorPicker: '',
      content: '',
      isMounting: false
    }
  },
  watch: {
    value (newVal, oldVal) {
      if (newVal !== oldVal) {
        if (this.colorPicker) {
          this.content = newVal === 'transparent' ? '#00000000' : newVal
          this.colorPicker.setColor(this.content, 'hex')
        }
      }
    }
  },
  methods: {
    openPicker () {
      this.colorPicker.setColor(this.content, 'hex')
      this.colorPicker.show()
      let saveColorDiv = document.createElement('div')
      saveColorDiv.setAttribute('class', 'saved_color')
      this.colorList.forEach(e => {
        const colorBox = document.createElement('div')
        colorBox.setAttribute('class', 'color_box')
        colorBox.style.backgroundColor = e
        colorBox.addEventListener('click', () => this.colorPicker.setColor(e))
        saveColorDiv.append(colorBox)
      })
      this.$el.children[1].children[0].append(saveColorDiv)
      saveColorDiv = null
    },
    changeColor (color) {
      if(!this.isMounting) {
        this.content = color.hex
        this.$emit('input', this.content)
      }
    },
    saveColor (color) {
      this.$emit('saveColor', color.hex)
      const colorBox = document.createElement('div')
      colorBox.setAttribute('class', 'color_box')
      colorBox.style.backgroundColor = color.hex
      colorBox.addEventListener('click', () => this.colorPicker.setColor(color.hex))
      console.log(this.$el.getElementsByClassName('saved_color'))
      this.$el.getElementsByClassName('saved_color')[0].append(colorBox)
    },
    closePicker (color) {
      this.changeColor(color)
    }
  },
  mounted () {
    this.isMounting = true
    this.content = this.value === 'transparent' ? '#00000000' : this.value
    const config = {
      parent: this.$el.children[1],
      popup: 'bottom',
      onChange: e => this.changeColor(e),
      onSave: e => this.saveColor(e),
      onClose: e => this.closePicker(e)
    }
    if (!this.popup) {
      config.popup = false
    }
    this.colorPicker = new Picker(config)
    this.colorPicker.setColor(this.content, 'hex')
    this.isMounting = false
  },
  beforeDestroy () {
    this.colorPicker.destroy()
  }
}
</script>

<style>
</style>
