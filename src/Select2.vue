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

    function okGo(that) {
      that.select2 = $(that.$el).find("select");
      that.select2.select2({
        placeholder: that.placeholder,
        ...that.settings,
        data: that.options
      })
      .on('select2:select select2:unselect', ev => {
        that.$emit('update:modelValue', that.select2.val());
        that.$emit('select', ev['params']['data']);
      })
      .on('change', ev => {that.$emit('change', ev);})
      .on('select2:closing', ev => {that.$emit('closing', ev);})
      .on('select2:close', ev => {that.$emit('close', ev);})
      .on('select2:opening', ev => {that.$emit('opening', ev);})
      .on('select2:open', ev => {that.$emit('open', ev);})
      .on('select2:selecting', ev => {that.$emit('selecting', ev);})
      .on('select2:select', ev => {that.$emit('select', ev);})
      .on('select2:unselecting', ev => {that.$emit('unselecting', ev);})
      .on('select2:unselect', ev => {that.$emit('unselect', ev);})
      .on('select2:clearing', ev => {that.$emit('clearing', ev);})
      .on('select2:clear', ev => {that.$emit('clear', ev);});
      
      if(typeof that.select2.setValue === 'function') {
        that.select2.setValue(that.modelValue);
      } else {
        if(typeof that.setValue === 'function') {
          that.setValue(that.modelValue);
        }
      }
    }

    //somehow in nuxt, it is not a very friendly neighborhood
    function waitUntilSelect2Loaded(that) {
      if(typeof $(that.$el).find('select').select2 === 'undefined') {
        setTimeout(() => {
          waitUntilSelect2Loaded(that);
        }, 500);
      } else {
        okGo(that);
      }
    }

    waitUntilSelect2Loaded(this);
  },
  beforeUnmount() {
    this.select2.select2('destroy');
  }
};
</script>
