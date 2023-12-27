<template>
  <div class="px-8">
    <v-row>
      <v-col
        cols="4"
        v-for="n in 9"
        :key="n"
      >
        <v-btn text @click="addValue(n)">{{n}}</v-btn>
      </v-col>
      <v-col cols="4"><v-btn text @click="addValue('.')">.</v-btn></v-col>
      <v-col cols="4"><v-btn text @click="addValue(0)">0</v-btn></v-col>
      <v-col cols="4"><v-btn text @click="removeValue()" color="primary"><v-icon style="font-weight: 900; font-size: 26px">mdi-arrow-left</v-icon></v-btn></v-col>
    </v-row>
  </div>
</template>
<script>
export default {
  props: ['value'],
  data () {
    return {
      localvalue: 0,
      textvalue: ''
    }
  },
  watch: {
    value () {
      this.localvalue = this.value
    }
  },
  methods: {
    addValue (value) {
      const newValue = this.textvalue.toString() + '' + value
      this.textvalue = newValue
      console.log(newValue)
      if (newValue === '.') {
        this.localvalue = 0
      } else {
        this.localvalue = parseFloat(newValue)
      }
      this.$emit('input', this.localvalue)
    },
    removeValue () {
      const newValue = this.localvalue.toString()
      if (newValue.length === 1) {
        this.localvalue = 0
        this.textvalue = ''
      } else {
        this.localvalue = parseFloat(newValue.slice(0, -1))
        this.textvalue = newValue.slice(0, -1)
      }
      this.$emit('input', this.localvalue)
    }
  }
}
</script>
