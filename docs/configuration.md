---
title: Plugin configuration 
date: 2018-09-15 07:42:34
slug: configuration
---

## Full Plugin Usage

Even though vue-form-plugin is so simple, you don't really need to set import locally within each component - you can simply set it up by using the plugin on Vue instance for a better developer experience.

To set up a new instance of vue-form-plugin, and start developing just use it like below:

```js
import Vue from 'vue';
import VueFormPlugin from '@e9ine/vue-form-plugin';

Vue.use(VueFormPlugin);
```

**Note**: CSS will be auto imported in this case. No need to manually import

## Tree Shaking Usage

You can also import it separately in each component and use.

```js
import {FormFor, FieldFor} from '@e9ine/vue-form-plugin';

export default {
    components: {
        FieldFor,
        FormFor
    }
}
```

**Note**: CSS will be auto imported in this case. No need to manually import

## SFC Usage

To use the Single File Components, you can import from `src/lib` directory. This should be the preferred way if you are planning to import scss files and vue files.

Manually include SFC components

```js
import FormFor from '@e9ine/vue-form-plugin/src/lib/FormFor';
import FieldFor from '@e9ine/vue-form-plugin/src/lib/FieldFor';
```

Manually include CSS/SCSS in your style.scss. You can either import style.scss or manually pick certain scss files to be imported.

```scss
@import '~@e9ine/vue-form-plugin/src/scss/style.scss';
```

## CDN Usage (Browser)

Include [vue-form-plugin](https://unpkg.com/@e9ine/vue-form-plugin) in the page. Make sure to keep the component name in kebab case as running Vue in browser mode can't detect capitalised component names.

No need to write Vue.use line since plugin will take care of automatically getting installed in Global Vue context.

Look at a simple example below :

```html
<head>
    <title>Browser build</title>
    <script src="https://unpkg.com/vue@2.6.11/dist/vue.js"></script>
    <script src="https://unpkg.com/@e9ine/vue-form-plugin"></script>
</head>
<div id="app">
    <field-for 
            type="Number" 
            label="Counter" 
            :value.sync="counter" 
            display-mode="EDIT">
    </field-for>
</div>
<script>
    new Vue({
        el: '#app',
        data: {
            counter: 0
        }
    });
</script>
```

## CDN Usage (ESM)

```html
<script type="module">
    import Vue from "https://unpkg.com/browse/vue@2.6.11/dist/vue.esm.browser.js"
    import VueFormPlugin from  "https://unpkg.com/@e9ine/vue-form-plugin/dist/vue-form-plugin.esm.js";
</script>
```

