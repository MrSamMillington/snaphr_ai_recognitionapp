<template>
  <div>
    <v-text-field class="clicker" prepend-icon="attach_file" single-line
                  v-model="filename" :label="label" :required="required"
                  @click.native="onFocus"
                  :disabled="disabled" ref="fileTextField"></v-text-field>
    <input type="file" :accept="accept" :multiple="multiple" :disabled="disabled"
           ref="fileInput" class="clicker" @change="onFileChange">
  </div>
</template>

<script>
  export default {
    props: {
      value: {
        type: [Array, String]
      },
      accept: {
        type: String,
        default: '*'
      },
      label: {
        type: String,
        default: 'choose a file'
      },
      required: {
        type: Boolean,
        default: false
      },
      disabled: {
        type: Boolean,
        default: false
      },
      multiple: {
        type: Boolean,
        default: false
      }
    },
    data () {
      return {
        filename: ''
      }
    },
    watch: {
      value (v) {
        this.filename = v
      }
    },
    mounted () {
      this.filename = this.value
    },
    methods: {
      getFormData (files) {
        const forms = []
        for (const file of files) {
          const form = new FormData()
          console.log(file)
          form.append('file', file)
          forms.push(form)
        }
        return forms
      },
      onFocus () {
        if (!this.disabled) {
          this.$refs.fileInput.click()
        }
      },
      onFileChange ($event) {
        const files = $event.target.files || $event.dataTransfer.files
        const form = this.getFormData(files)
        if (files) {
          if (files.length > 0) {
            this.filename = [...files].map(file => file.name).join(', ')
          } else {
            this.filename = null
          }
        } else {
          this.filename = $event.target.value.split('\\').pop()
        }
        this.$emit('input', this.filename)
        this.$emit('formData', form)
      }
    }
  }
</script>

<style scoped>
  input[type=file] {
    position: absolute;
    left: -99999px;
  }

  .clicker{
    cursor: pointer;
  }
</style>
