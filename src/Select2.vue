<template>
  <div>
    <select class="form-control" :id="id" :name="name" :disabled="disabled" :required="required"></select>
  </div>
</template>

<script>
/**
 * this is the reason I make this for-nuxt3 version
 * might inspire you to do the same for any library that has the same problem
 *
 * these below are all the fail attemps :)
 */
// import $ from 'jquery';
// import {$,jQuery} from 'jquery';
// import * as jQuery from 'jquery';
// import * as $ from 'jquery'
// import $ from './node_modules/jquery'
// import * as $ from 'jquery/dist/jquery.js'
// import * as $ from 'jquery/dist/jquery'
import 'jquery/dist/jquery.js';
import 'select2/dist/js/select2.full';
import 'select2/dist/css/select2.min.css';

// const jQuery = require('jquery')(dom.window)

/**
 *
change
closing
close
opening
open
selecting
select
unselecting
unselect
clearing
clear
 *
 *
 */
export default {
  name: 'Select2',
  data() {
    return {
      select2: null
    };
  },
  emits: [
    'update:modelValue',
    'change',
    'closing',
    'close',
    'opening',
    'open',
    'selecting',
    'select',
    'unselecting',
    'unselect',
    'clearing',
    'clear',
  ],
  props: {
    modelValue: [String, Array], // previously was `value: String`
    id: {
      type: String,
      default: ''
    },
    name: {
      type: String,
      default: ''
    },
    placeholder: {
      type: String,
      default: ''
    },
    options: {
      type: Array,
      default: () => []
    },
    disabled: {
      type: Boolean,
      default: false
    },
    required: {
      type: Boolean,
      default: false
    },
    settings: {
      type: Object,
      default: () => {}
    },
  },
  watch: {
    options: {
      handler(val) {
        this.setOption(val);
      },
      deep: true
    },
    modelValue: {
      handler(val) {
        this.setValue(val);
      },
      deep: true
    },
  },
  methods: {
    setOption(val = []) {
      this.select2.empty();
      this.select2.select2({
        placeholder: this.placeholder,
        ...this.settings,
        data: val
      });
      this.setValue(this.modelValue);
    },
    setValue(val) {
      if (val instanceof Array) {
        this.select2.val([...val]);
      } else {
        this.select2.val([val]);
      }
      this.select2.trigger('change');
    }
  },
  mounted() {
    let $ = jQuery;

    this.select2 = $(this.$el)
      .find('select')
      .select2({
        placeholder: this.placeholder,
        ...this.settings,
        data: this.options
      })
      .on('select2:select select2:unselect', ev => {
        this.$emit('update:modelValue', this.select2.val());
        this.$emit('select', ev['params']['data']);
      })
      .on('change', ev => {this.$emit('change', ev);})
      .on('select2:closing', ev => {this.$emit('closing', ev);})
      .on('select2:close', ev => {this.$emit('close', ev);})
      .on('select2:opening', ev => {this.$emit('opening', ev);})
      .on('select2:open', ev => {this.$emit('open', ev);})
      .on('select2:selecting', ev => {this.$emit('selecting', ev);})
      .on('select2:select', ev => {this.$emit('select', ev);})
      .on('select2:unselecting', ev => {this.$emit('unselecting', ev);})
      .on('select2:unselect', ev => {this.$emit('unselect', ev);})
      .on('select2:clearing', ev => {this.$emit('clearing', ev);})
      .on('select2:clear', ev => {this.$emit('clear', ev);})
    this.setValue(this.modelValue);
  },
  beforeUnmount() {
    this.select2.select2('destroy');
  }
};
</script>
