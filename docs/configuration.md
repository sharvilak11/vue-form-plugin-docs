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

## Tree Shaking Usage

You can also import it separately in each component and use.

```js
import FormFor from '@e9ine/vue-form-plugin/lib/FormFor';
import FieldFor from '@e9ine/vue-form-plugin/lib/FieldFor';

export default {
    components: {
        FieldFor,
        FormFor
    }
}
```

