<template>
  <div class="vuep-error" v-if="error">
    {{error}}
  </div>
  <preview
    v-else
    :value="preview"
    :styles="styles"
    :keep-data="keepData"
    :iframe="iframe"
    @error="handleError"
  />
</template>

<script>
/* eslint-disable */
import Editor from './editor'
import Preview from './preview'
import parser from './parser'
import compiler from './compiler'

export default {
  name: 'Renderer',

  components: { Preview },

  props: {
    template: String,
    options: {},
    keepData: Boolean,
    value: String,
    scope: Object,
    iframe: Boolean
  },

  data () {
    return {
      content: '',
      preview: '',
      styles: '',
      error: ''
    }
  },

  watch: {
    value: {
      immediate: true,
      handler (val) {
        val && this.executeCode(val)
      }
    }
  },

  created () {
      /* istanbul ignore next */
    if (this.$isServer) return
    let content = this.template

    if (/^[\.#]/.test(this.template)) {
      const html = document.querySelector(this.template)
      if (!html) throw Error(`${this.template} is not found`)

      /* istanbul ignore next */
      content = html.innerHTML
    }

    if (content) {
      this.executeCode(content)
      this.$emit('input', content)
    }
  },

  methods: {
    handleError (err) {
      /* istanbul ignore next */
      this.error = err
    },

    executeCode (code) {
      this.error = ''
      const result = parser(code)

      /* istanbul ignore next */
      if (result.error) {
        this.error = result.error.message
        return
      }

      const compiledCode = compiler(result, this.scope)

      /* istanbul ignore next */
      if (compiledCode.error) {
        this.error = compiledCode.error.message
        return
      }

      this.content = result.content
      this.preview = compiledCode.result
      if (compiledCode.styles) this.styles = compiledCode.styles
    }
  }
}
</script>

