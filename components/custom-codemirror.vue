<template>
    <div class="sofy-codemirror">
        <div style="border:1px solid #DDD;position: relative;">
            <codemirror
              ref="cm"
              class="higher"
              v-model="text"
              :options="cmOptions"
              @input="$emit('input', text)"
              @changes="onChange"
            />
        </div>
    </div>
</template>

<script>
import Vue from 'vue';

import VueCodeMirror from 'codemirror';
import * as codeMaster from 'codemirror';
import 'codemirror/mode/sql/sql.js';
import 'codemirror/addon/dialog/dialog.js';
import 'codemirror/lib/codemirror.css';
import 'codemirror/addon/dialog/dialog.css';

import 'codemirror/theme/solarized.css';
import "codemirror/addon/hint/show-hint";
import "codemirror/addon/hint/show-hint.css";
import "codemirror/addon/hint/sql-hint";

import VueCodemirror from 'vue-codemirror';

Vue.use(VueCodemirror);

export default {
  props: {
    value: String,
    sqlEditorEnabled: Boolean,
    options: Object,
  },

  components: { VueCodeMirror },

  data() {
    return {
      text: this.value,
      fullScreen: false,
      cm: null,
      cmOptions: {},
    }
  },

  watch: {
    value(nv) {
      this.text = nv
    },
    options(nv) {
      this.cmOptions = nv
    },
  },

  created() {
    const self = this
    this.cmOptions = this.options || {
      autoRefresh: true,
      tabSize: 4,
      styleActiveLine: true,
      lineNumbers: true,
      line: true,
      mode: 'text/x-mysql',
      theme: 'solarized dark',
      height: '400px'
    }
  },

  mounted() {
    this.cm = codeMaster
  },

  methods: {
    onChange(cm, change) {
      const { text, origin } = change[0]
      if (origin === "+input" && text[0].trim()) {
        cm.execCommand("autocomplete")
      }
    }
  }
}
</script>

<style scoped>
.higher {
    height: 300px;
    z-index: 2;
}
</style>
