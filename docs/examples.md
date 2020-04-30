---
title: Examples
date: 2020-04-29 00:30:34
slug: examples

---
## FieldFor

FieldFor component is the last component in the hierarchy over which the developer has control. This component is responsible for rendering all the form elements based on passed props.

You can use FieldFor in two ways.

1. Standalone
2. As a slot to FormFor

For **Standalone** way, the component at-least expects `type`, `label`, `value` and `display-mode` properties. 

Two binding can be done on value variable by putting a `.sync` modifier. Alternatively an event `@changed` can also be listened to, in case you want to listen to any changes being done in form-element's value.

**Note:** If there is already a `display-mode` reactive data variable available in the parent scope, then passing it as a prop is not necessary.

```html
<FieldFor 
    Type="Text" 
    label="Name" 
    value.sync="name" 
    display-mode="EDIT"
    @changed="fetchAllCustomers">
</FieldFor>
```
```js
export default {
    name: 'Details',
    data() => ({ name: '' });
}
```

For using FieldFor inside **FormFor** , let us first see what FormFor is.

## FormFor

FormFor acts as a slot-friendly parent wrapper around all your FieldFor or custom components. It reads the schema passed to it and tries to render the specified element by looking into model properties as well as local props.

Certain Props will always have the higher priority over schema properties. Mandatory properties required to use with FormFor are `model` , `data` and `display-mode`. 

FormFor has a special use case when it comes to validation. It reads through all its children slots and gathers the errors if any. You can use slot-scope which exposes `invalid` flag and `errors` array via `v-slot`.

```html
<FormFor 
    :data="project"
    :display-mode="displayMode" 
    v-slot="form">
    <FieldFor field="Name" label="Customer Name"></FieldFor> 
    <FieldFor field="Description"></FieldFor>
    <FieldFor field="Budget" :min="0" :max="1000000" placeholder="Please enter your age"></FieldFor> 
    
    <Button text="Save" type="primary" v-show="displayMode === 'EDIT'" :action="save" :disabled="form.invalid" :async="true">
        <i class="material-icons">save</i>
    </Button>
</FormFor>
``` 
```js
import Project from '@/models/Project';
export default {
    data() {
        return {
            project: new Project(),
            displayMode: 'EDIT'
        }   
    }
}
```

** Format of the model/schema **

A schema property must have at-least a type which depicts the type of element to be rendered dynamically through FieldFor.
```js
const _schema = {
    Name: {
        type: String,
        required: true
    },
    Description: {
        type: String,
        textarea: 4   
    },
    StartDate: {
        type: Date,
        required: true
    },
    BudgetCost: {
        type: Number,
        filter: 'currency'
    },
    Status: {
        type: String,
        enum: ['Planned', 'Live', 'Dormant']
    },
    IsActive: {
        type: Boolean
    },
    ImageUrl: {
        type: String
    }
}
export default class Project extends Model {
    constructor(data) {
        super();
        Object.assign(this, data);

        // Any virtual properties of the model should be configured here.
        // Virtual properties must start with _

        this._ImageUrl = this.getProjectImageUrl();
    }
    static schema() {   // This is a mandatory method.
        return _schema;
    },
    getProjectImageUrl() {
        if (this.ImageUrl) {
            return window.bucketendpoint + this.ImageUrl;
        } else {
            return require('@/assets/images/placeholder.png');
        }
    }
}
```

**FormFor Chaining**

- In certain situations like **nested schemas or nested arrays**, it's best to utilise FormFor with chaining due to Vue's slot capabilities.
- You can chain multiple FormFor within a FormFor as each FormFor exposes its own [**Scoped-Slot**](https://vuejs.org/v2/guide/components-slots.html#Scoped-Slots-with-the-slot-scope-Attribute) with `errors` and `invalid` flag. An example is shown below
```html
<FormFor :data="project" display-mode="EDIT" v-slot="form1">
    <!--Invalid : {{ form1.invalid }} -->
    <FieldFor field="Name"></FieldFor>
    <FieldFor field="Description"></FieldFor>
    <h5>FormFor Level 2</h5>
    <FormFor :data="project.Address" display-mode="EDIT" v-slot="form2">
        <!-- Invalid : {{ form2.invalid }} -->
        <FieldFor field="Line1"></FieldFor>
        <FieldFor field="Line2"></FieldFor>
        <h5>FormFor Level 3</h5>
        <FormFor :data="project.Address.Location" display-mode="EDIT" v-slot="form3">
            <!-- Invalid : {{ form3.invalid }} -->
            <FieldFor field="Longitude"></FieldFor>
            <FieldFor field="Latitude"></FieldFor>
        </FormFor>
    </FormFor>
</FormFor>
```

Presence of certain properties within a model will force a particular element to be rendered.

`enum` with Array: This will render Dropdown list.

`textarea`: Based on the integer value passed, it will set the rows of text area.

`phone`: If set to true, it will render the [vue-tel-input](https://github.com/EducationLink/vue-tel-input) component.

Some extra properties which can be set in the models include `filter`, `maxlength`, `min`, `max`, `required`, `calendarConfig`. More options can be passed via individual FieldFor props. You can find all the props in the [Props Configuration](./examples#props-configuration).

## Re-render/Force Reactivity

- In certain cases when you would need to re-render a component to trigger the reactivity, you might want to re-render a FieldFor or a FormFor component. How you can do it ?

- Vue triggers the component to re-render if at-least one of its prop changes and best way to do it is binding a counter to `key` prop and increasing it when needed.

Let's take an example, we need to re-render a Total Cost when Quantity and CostPerQuantity changes.
```html
<FieldFor field="Quantity" @changed="quantityChanged"></FieldFor>
<FieldFor field="CostPerQuantity" @changed="costPerQuantityChanged"></FieldFor>
<FieldFor field="TotalCost" :key="counter"></FieldFor>
```

```js
import Cost from '@/models/Cost'
export default {
    data() {
        return {
            counter: 0,
            cost: new Cost({
                Quantity: 0,
                CostPerQuantity: 0,
                TotalCost: 0
            })    
        };
    },
    methods: {
        quantityChanged(val) {
             this.cost.TotalCost = val * this.cost.CostPerQuantity;  
             this.counter++; 
        },
        costPerQuantityChanged(val) {
             this.cost.TotalCost = val * this.cost.Quantity;   
             this.counter++;
        }   
    }
}
```

In case of FormFor, you can follow the same approach too. It always helps when you have to rollback upon clicking on cancel, update the data and then update the counter linked as key to FormFor and FormFor will take care of roll-back.
```html
<FormFor :data="cost" :display-mode="displayMode" :key="counter">
...
</FormFor>
```

```js
cancel() {
    this.displayMode = 'VIEW';
    this.cost = new Cost(this.prevCost);
    this.counter++;
}
```

**Note** : FieldFor is reactive bottom-up (from Child to Parent) unlimited times but from top-down (Parent to Child) it's always first time reactivity (Exceptional cases like Dropdown).
 

All the examples below are based on FormFor usage, but you can use `type` and `value` properties in case you are planning to use standalone FieldFor.

## TextBox

*Standalone*: `type="Text"`

*Through model*: `{ type: String }`

```html
<FieldFor 
    field="SubTitle"
    id="subTitle"
    tab-index="0"
    label="Sub title" 
    placeholder="Please add a sub title"
    filter="truncateChars"
    :filter-args="[30]"
    :required="true"
    :custom-class="text-left"
    :show-suggestion="true"
    :suggestions="suggestedSubTitles"
    @changed="valueChanged">
</FieldFor>
```

## Number

*Standalone*: `type="Number"`

*Through model*: `{ type: Number }`

```html
<FieldFor 
    field="BudgetCost"
    id="budgetCost"
    tab-index="0"
    label="Budget Cost" 
    placeholder="Please add a budget"
    prepend="£"
    filter="currency"
    :filter-args="['GBP', 2]"
    :required="true"
    :custom-class="text-right"
    :min="0"
    :max="100000"
    @changed="budgetChanged">
</FieldFor>
```

## Toggle

*Standalone*: `type="Boolean"`

*Through model*: `{ type: Boolean }`

```html
<FieldFor 
    :field="IsActive" 
    label="Activated ?"
    @changed="valueChanged">
</FieldFor>
```

## Dropdown

*Standalone* or *Through model* : `type="select-from"` 

**Note**: Keeping `enum` array in the model also would populate dropdown list.

```html
<FieldFor 
    :field="Client" 
    label="Choose Client" 
    :select-from="clients" 
    placeholder="Start typing to search"
    display-field="Name"
    value-field="ClientId"
    :searchable="true" 
    :allow-clear="true"
    :show-avatar="true"
    avatar-prop="ImageUrl"
    @changed="dropDownValueChanged">
</FieldFor>
```

If select-from is array of object, by default the Dropdown tries to display `Name` and `Description` props, however please refer below to customization guide.

**Customization**:

`display-field` : This property of the selected item will be displayed to the user. By default it is set to `Name`.

`value-field` : This property of the selected item sets the value for two way binding in the dropdown. By default it is set to `_id`.

`full-object` : If set to `true`, then whole object is set as value for two way binding in the dropdown.

`searchable` : If set to `true`, then the dropdown becomes searchable.

`option-template` : In case you need a custom template to be put, please make sure to set below option in `vue.config.js`.

```js
runtimeCompiler: true
```

select-from options will be available as `{{ props.option }}`

Here is an example,

```html
taxOptionsTempate: `
    <div class="description">
        <div class="title brand-primary" v-text="props.option.Name"></div>
        <small class="text-dark mb0" v-if="props.option.Description" style="display: block">
            {{ props.option.Description }}
        </small>
        <small v-for="(comp, key) in props.option.TaxComponents" :key="key">
            {{ comp.Name }} : <span class="brand-primary">{{ comp.Rate }} %</span>
        </small>
    </div>
`,
```

## Multi Select

*Standalone* or *Through model* : `type="select-from" and :multiple="true"` 

Rest is similar as above.

## DatePicker

*Standalone*: `type="Date"`

*Through model*: `{ type: Date }`

```html
<FieldFor 
    label="Date Of Birth" 
    field="DateOfBirth" 
    :calendar-config="calendarConfig"
    @change="dateChanged">
</FieldFor>
```

To check for possible calendar-configurations, please visit [vue-flatpickr Component](https://github.com/ankurk91/vue-flatpickr-component)

## Phone

*Standalone*: `type="String" and :phone="true"`

*Through model*: `{type: String, phone: true}`

```html
<FieldFor 
    field="Mobile" 
    :phone="true"
    :show-flags="false">
</FieldFor>
```

## Props Configuration

| Name          | Description |  Scope  | Type |
| --------------|:-----------:| -------:| ------:|
| field         | defines the field of the schema for which element will be rendered | global with FormFor | String
| type          | defines the type of the element which will be rendered | global with standalone | String
| value         | binds the value to the field. use `.sync` for two way communication | global with standalone | any
| label         | name of the label to be shown above the element | global | String
| required      | boolean property defines whether the element is a required field or not | global | Boolean
| placeholder   | shows the value as placeholder | global | String
| customClass   | appends the css class to the element in EDIT mode | global | String
| disabled      | setting this to true will disable the element from user input | global | Boolean
| displayMode   | display mode to be either `VIEW`, `CREATE` or `EDIT` | global | String
| hideLabel     | hide the label from the form-group | global | Boolean
| filter        | applies the passed filter when display-mode is VIEW | global | String
| filterArgs     | applies the passed arguments to called filter when filter prop is passed | global | Array-Like
| regex         | applies the regex to the value for validation | Text field | new RegExp
| showSuggestion | sets the input element in suggestion mode | Text field | Boolean
| suggestions   | list of suggestions from which most matchable suggestion will be populated | Text field | Array
| min           | minimum value | Number field | Number
| max           | maximum value | Number field | Number
| prepend       | input group button will be prepended to show currency, percentage etc | Number field | String
| rows          | value of this param defines the size of text area | textarea | Number
| selectFrom    | value of this property renders the options of dropdown list - must be an array | Dropdown | Array
| displayField  | when dropdown list has objects as options, property passed as displayField shows the value of selected element  | Dropdown | String
| valueField    | when dropdown list has objects as options, property passed as valueField acts as primary key and sets the value | Dropdown | String
| fullObject    | when dropdown list has objects as options, setting this property as true will set whole object as value instead of primary key | Dropdown | Boolean
| searchable    | setting this property as true will add a search option with a textbox for dropdown list | Dropdown | Boolean
| allowClear    | setting this property as true will display an icon-button to clear dropdown's selected value | Dropdown | Boolean
| showAvatar    | setting this property as true will display an avatar based on avatar-props. See below | Dropdown | Boolean
| avatarProp    | this property sets the image's src attribute which is responsible for displaying avatar | Dropdown | String
| optionTemplate| template for displaying dropdown items. Please check Dropdown section above for more details | Dropdown | String
| multiple      | setting this property as true will turn dropdown list into a multi select | Multiselect | Boolean
| phone         | Setting this as true turns your element into telephone supported field | FormFor with property type as string | Boolean
| showFlags     | Setting this as false hides national flags from telephone supported element. (Saves 122KB worth of css) | Phone Field | Boolean
| calendarConfig | Date pickr configuration. Check [options](https://flatpickr.js.org/options/) | Date Field | Object


