This is a [select2](https://select2.org/) component for [nuxt3](https://v3.nuxtjs.org/) based on godbasin work here: [vue3-select2-component](https://github.com/godbasin/vue-select2/tree/npm-publish-code-for-vue3)  credits belong to them for making all these availble for us for free.


## How to use
---
### Install
``` cmd
// npm install
npm install nuxt3-select2 --save
```

### Use as component
1. make a plugin
``` javascript
// plugins/select2.client.ts
import Select2 from 'nuxt3-select2';

export default defineNuxtPlugin((nuxtApp) => {
  nuxtApp.vueApp.component("Select2", Select2, {});
});
```

2. now it's available on your template
``` javascript
// use it
<template>
  <div>
    <Select2 v-model="myValue" :options="myOptions" :settings="{ settingOption: value, settingOption: value }" @change="myChangeEvent($event)" @select="mySelectEvent($event)" />
    <h4>Value: {{ myValue }}</h4>
  </div>
</template>
<script setup lang="ts">
  const myChangeEvent = (event) => {
    console.log("myChangeEvent: ", event);
  }
  const mySelectEvent = (e) => {
    console.log("mySelectEvent: ", event);
  }
  const myOptions = [
    {id: 1, text: 'apple'},
    {id: 2, text: 'berry'},
    {id: 3, text: 'cherry'},
  ]
  const myValue = ref();
</script>
```

### Options
- `options`: `option[]`
  - select options for select2
  - `option`: `{id: key, text: value}` or `string`
- `v-model`: option value that is selected
  - `id` or `string` while multiple is disable
  - `id[]` or `string[]` while multiple is enable
- `disabled`
  - if select is disabled
- `placeholder`
  - placeholder attribute for select element
- `id`
  - id attribute for select element
- `name`
  - name attribute for select element
- `settings`
  - configurable settings, see [Select2 options API](https://select2.org/configuration/options-api)
  - `setting`: `{ settingOption: value, settingOption: value }`

### Supported Events
currently supporting these events:
- `change` := change
- `closing` := select2:closing
- `close` := select2:close
- `opening` := select2:opening
- `open` := select2:open
- `selecting` := select2:selecting
- `select` := select2:select
- `unselecting` := select2:unselecting
- `unselect` := select2:unselect
- `clearing` := select2:clearing
- `clear` := select2:clear


For the complete documentation, see on [Select2 Event API](https://select2.org/programmatic-control/events)



### Misc

- problem with select2 inside input-group class
Somehow this select2 doesn't work well with bootstrap input-group. Has googled it and found many people facing the same problem.

Here is how I manage it works:

```html
  <div class="input-group">
    <Datepicker class="my-control" />
    <Select2 class="my-control"/>
  </div>
```

Putting form-control class is ok, but it will create borders, while I don't want to have that border. So I create my own class: my-control. It's optional of course.

here is the css:
``` css
  .input-group .select2-container {
    height: 37px;
  }

  .input-group .select2-container .selection .select2-selection--single {
      height: 100%;
  }

  /* optional, you can use form-control if you prefer */
  .input-group .my-control {
    position: relative;
    flex: 1 1 auto;
    width: 1%;
    min-width: 0;
  }
```

- problem with select2 inside modal class
this is also a weird behaviour.

there are 30 solutions (wow :) here: [https://stackoverflow.com/questions/18487056/select2-doesnt-work-when-embedded-in-a-bootstrap-modal](https://stackoverflow.com/questions/18487056/select2-doesnt-work-when-embedded-in-a-bootstrap-modal)

but sadly few top answers just doesn't work for me. Instead, I did a little trick, and it worked.
just override the z-index
```css
.select2-container.select2-container--default.select2-container--open {
  z-index: 9999;
}
```

you don't have to make it 9999. at least the modal z-index + 1 is enough.
