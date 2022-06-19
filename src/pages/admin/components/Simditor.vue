<template>
  <div>
    <textarea ref="editor"></textarea>
    <div style="display: flex; width: 100%; height: 64px; flex-flow: row nowrap; justify-content: space-between">
      <textarea ref="raw-editor" style="flex: 1 1 auto; resize: none" v-model="rawEditorValue"></textarea>
      <el-button size="small" v-on:click="syncToSimditor">Synchronize</el-button>
    </div>

  </div>

</template>

<script>
  import Simditor from 'tar-simditor'
  import 'tar-simditor/styles/simditor.css'
  import 'tar-simditor-markdown'
  import 'tar-simditor-markdown/styles/simditor-markdown.css'
  import './simditor-file-upload'

  export default {
    name: 'Simditor',
    props: {
      toolbar: {
        type: Array,
        default: () => ['title', 'bold', 'italic', 'underline', 'fontScale', 'color', 'ol', 'ul', '|', 'blockquote', 'code', 'link', 'table', 'image', 'uploadfile', 'hr', '|', 'indent', 'outdent', 'alignment', '|', 'markdown']
      },
      value: {
        type: String,
        default: ''
      }
    },
    data () {
      return {
        editor: null,
        currentValue: this.value,
        rawEditorValue: this.value
      }
    },
    mounted () {
      this.editor = new Simditor({
        textarea: this.$refs.editor,
        toolbar: this.toolbar,
        pasteImage: true,
        markdown: false,
        upload: {
          url: '/api/admin/upload_image/',
          params: null,
          fileKey: 'image',
          connectionCount: 3,
          leaveConfirm: this.$i18n.t('m.Uploading_is_in_progress')
        },
        allowedStyles: {
          span: ['color']
        }
      })
      this.editor.on('valuechanged', (e, src) => {
        this.currentValue = this.editor.getValue()
        this.rawEditorValue = this.currentValue
      })
      this.editor.on('decorate', (e, src) => {
        this.currentValue = this.editor.getValue()
        this.rawEditorValue = this.currentValue
      })

      this.editor.setValue(this.value)
    },
    methods: {
      syncToSimditor () {
        this.editor.setValue(this.rawEditorValue)
      }
    },
    watch: {
      'value' (val) {
        if (this.currentValue !== val) {
          this.currentValue = val
          this.editor.setValue(val)
        }
      },
      'currentValue' (newVal, oldVal) {
        if (newVal !== oldVal) {
          this.$emit('change', newVal)
          this.$emit('input', newVal)
        }
      }
    }
  }
</script>

<style lang="less" scoped>
</style>
