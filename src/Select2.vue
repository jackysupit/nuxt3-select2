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

/* import 'jquery/dist/jquery.js'; */
/* import 'select2/dist/js/select2.full'; */


// import 'jquery/dist/jquery.js'
// import 'select2/dist/js/select2.full.js';
// console.log("starting the select2");
import 'https://cdnjs.cloudflare.com/ajax/libs/jquery/3.6.1/jquery.min.js';
import 'https://cdn.jsdelivr.net/npm/select2@4.1.0-rc.0/dist/js/select2.min.js';

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
  // head: {
  //   script: [
  //     // { src: 'https://cdnjs.cloudflare.com/ajax/libs/jquery/3.6.1/jquery.min.js'},
  //     // { src: 'https://cdn.jsdelivr.net/npm/select2@4.1.0-rc.0/dist/js/select2.min.js', mode: "client"},
  //     // { src: 'https://cdn.jsdelivr.net/npm/select2@4.1.0-rc.0/dist/js/select2.js'},
  //     {
  //         hid: 'stripe',
  //         src: '<https://cdnjs.cloudflare.com/ajax/libs/jquery/3.6.1/jquery.min.js/>',
  //         defer: true,
  //         // Changed after script load
  //         callback: () => { this.isStripeLoadedJquery = true }
  //     },
  //     {
  //         hid: 'stripe',
  //         src: '<https://cdn.jsdelivr.net/npm/select2@4.1.0-rc.0/dist/js/select2.js/>',
  //         defer: true,
  //         // Changed after script load
  //         callback: () => { this.isStripeLoadedSelect2 = true }
  //     },
  //   ]
  // },
  data() {
    return {
      select2: null,
      isStripeLoadedJquery: false,
      isStripeLoadedSelect2: false,
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
      if(!this.select2) {
        let elSelect;
        if(that.$el) {
          elSelect = $(that.$el).find("select.this-is-select2");
        } else {
          elSelect = $(that).find("select.this-is-select2");
        }
        this.select2 = elSelect;
      }

      // this.select2.empty();
      this.select2.select2({
        placeholder: this.placeholder,
        ...this.settings,
        data: val
      });
      this.setValue(this.modelValue);
    },
    setValue(val) {
      if(!this.select2) {
        let elSelect;
        if(that.$el) {
          elSelect = $(that.$el).find("select.this-is-select2");
        } else {
          elSelect = $(that).find("select.this-is-select2");
        }
        this.select2 = elSelect;
      }
      if (val instanceof Array) {
        this.select2.val([...val]);
      } else {
        this.select2.val([val]);
      }
      this.select2.trigger('change');
    }
  },
  mounted() {
    const self = this;
    // console.log("starting the select2 - mounted");
    let xJquery = 0;
    function waitJquery(that) {
      if(typeof window.jQuery === "undefined") {
        xJquery++;

        // if(xJquery == 3) {
        //   let jqueryScript = document.createElement('script');
        //   jqueryScript.setAttribute('src','https://cdnjs.cloudflare.com/ajax/libs/jquery/3.6.1/jquery.min.js');
        //   document.head.appendChild(jqueryScript);
        // }

        // console.log("starting the select2 - waiting xJquery: ", xJquery);

        setTimeout(() => {
          waitJquery(that);
        }, 1000);
      } else {
        let $ = window.jQuery;
        window.$ = $;

        function okGo(that) {
          if(!that.select2) {
            let elSelect;
            if(that.$el) {
              elSelect = $(that.$el).find("select.this-is-select2");
            } else {
              elSelect = $(that).find("select.this-is-select2");
            }
            that.select2 = elSelect;
          }

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
          if(!that.select2) {
            let elSelect;
            if(that.$el) {
              elSelect = $(that.$el).find("select.this-is-select2");
            } else {
              elSelect = $(that).find("select.this-is-select2");
            }
            that.select2 = elSelect;
          }
          if(typeof that.select2.select2 === 'undefined') {
            setTimeout(() => {
              waitUntilSelect2Loaded(that);
            }, 1000);
          } else {
            okGo(that);
          }
        }
        // waitUntilSelect2Loaded(this);
        // okGo(this);

        let xx = 0;
        function reMountedIfNotYet(that) {
          xx++;

          // if(xx == 3) {
          //   let select2Script = document.createElement('script');
          //   select2Script.setAttribute('src','https://cdn.jsdelivr.net/npm/select2@4.1.0-rc.0/dist/js/select2.min.js');
          //   document.head.appendChild(select2Script);
          // }




          // console.log("exec reMountedIfNotYet " + xx + ': ', xx)
          setTimeout(() => {
            if(!that.select2) {
            let elSelect;
            if(that.$el) {
              elSelect = $(that.$el).find("select.this-is-select2");
            } else {
              elSelect = $(that).find("select.this-is-select2");
            }
            that.select2 = elSelect;
            }
            if(!that.select2.hasClass("select2-hidden-accessible")) {
              waitUntilSelect2Loaded(that);
              reMountedIfNotYet(that);
            }
          }, 1000);
        }

        reMountedIfNotYet(self);
      }
    }

    waitJquery(self);
  },
  beforeUnmount() {
    if(this.select2) {
      if(this.select2.hasClass("select2-hidden-accessible")) {
        this.select2.select2('destroy');
      }
    }
  }
};
</script>
<template>
  <div>
    <select class="form-control this-is-select2" :placeholder="placeholder" :id="id" :name="name" :disabled="disabled" :required="required"></select>
  </div>
</template>
<style>
  /* @import 'select2/dist/css/select2.min.css'; */
  @import 'https://cdn.jsdelivr.net/npm/select2@4.1.0-rc.0/dist/css/select2.min.css';
</style>
