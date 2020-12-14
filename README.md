# vue-twoside-multiple-select

A MultiSelect with two lists for Vue.js, searchable, sorting and action buttons.

enable group list move and remove

![capture1](https://user-images.githubusercontent.com/17465497/102037021-84e18f00-3e07-11eb-972e-7196e019ffd5.JPG)
## Install npm

```bash
npm install vue-twoside-multiple-select --save
```

## Import and use

Import for global usage
```javascript
import Vue from 'vue'
import TwosideSelect from 'vue-twoside-multiple-select'

Vue.use(TwosideSelect)
...
```
Or on a single component
```javascript
import TwosideSelect from 'vue-twoside-multiple-select'
...
},
components: { TwosideSelect }
...
```

## Props

| Name                    | Type             | Default         | Description                                                              |
|-------------------------|------------------|-----------------|--------------------------------------------------------------------------|
| items                    | Array           | | Array of items to select                                               |
| selectedItems           | Array |              | Array of selected items  |
| valueField           | String | value             | Value field |
| textField           | String | text             | Text field |
| childField           | String | children             | Text field |
| showField           | String | disabled             | Text field |
| searchable           | Boolean | false             | If enabled, it will display search fields for lists. |
| searchablePlaceholder | String | Search | Placeholder of inputs search
| sorteable | Boolean | false | Sort array by property name
| orderBy | String | id | Property name to sort
| textItems | String | items | Counter text that is below the left list
| textSelectedItems | Object | {one: 'selected item', greaterThanOne: 'selected items'} | Counter text that is below the right list
| limitSelectedItems | Number | 999 | Limit items that the user can select
| disabled           | Boolean | false             | Disable select |

## Events

| Name                   | Description                                                              |
|------------------------|--------------------------------------------------------------------------|
| itemAdded              | When an item has been added to the list  |
| itemRemoved            | When an item has been removed from the list  |
| selectedListModified   | When the selected list has been modified  |


## How to use


After you have installed the package and imported it, call the component's html and pass its properties.

```html
<twoside-select
  :items="arrayOfItems"
  :selectedItems="arrayOfSelectedtems"
  :limitSelectedItems="10"
  :searchable="true"
  :disabled="false"
  :sorteable="true"
  :orderBy="'name'"
  :showTextSelectedItem="true"
  :limitSelectedItems="999"
  text-field="name"
  value-field="id"
  ></twoside-select>
```
```javascript
arrayOfItems: [
    {
        id: 1,
        name: 'Bitcoin',
        children: [
            {
                id: 22,
                name: 'Bitcoin2'
            },
            {
                id: 23,
                name: 'Bitcoin3',
                disabled: true
            },
            {
                id: 24,
                name: 'Bitcoin4'
            },
            {
                id: 25,
                name: 'Bitcoin5'
            },
            {
                id: 26,
                name: 'Bitcoin6'
            }
        ]
    },
    {
        id: 2,
        name: 'Ethereum',
        disabled: true
    },
    {
        id: 3,
        name: 'Ripple'
    }
```

## Contributing

To contribute to this project you can clone the repository and run the npm run dev command to test.

```javascript
npm install

npm run dev

npm publish
```