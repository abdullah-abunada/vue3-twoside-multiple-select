<template>
  <div class="container">
    <h1>Vue3 Two-side Multiple Select</h1>
    <multiple-select
        v-if="toggle"
        :items="cryptocurrencies"
        :selectedItems="selectedCurrencies"
        :searchable="true"
        :disabled="false"
        :sorteable="true"
        :showTextSelectedItem="true"
        :limitSelectedItems="999"
        :orderBy="'name'"
        text-field="name"
        value-field="id2"
        v-on:selectedListModified="testEvent"
    ></multiple-select>
  </div>
</template>

<script>
import MultipleSelect from '../MultipleSelect'
export default {
  components: {
    MultipleSelect,
  },
  data () {
    return {
      toggle: true,
      cryptocurrencies: [
        {
          id2: 1,
          name: 'Bitcoin',
          disabled: false,
          children: [
            {
              id2: 22,
              name: 'Bitcoin2'
            },
            {
              id2: 23,
              name: 'Bitcoin3'
            },
            {
              id2: 24,
              name: 'Bitcoin4'
            },
            {
              id2: 25,
              name: 'Bitcoin5'
            },
            {
              id2: 26,
              name: 'Bitcoin6'
            },
          ]
        },
        {
          id2: 2,
          name: 'Ethereum'
        },
        {
          id2: 3,
          name: 'Ripple'
        },
        {
          id2: 4,
          name: 'Bitcoin Cash2233',
          // disabled: true,
          children: [
            {
              id2: 42,
              name: 'Ethereum2'
            },
            {
              id2: 43,
              name: 'Ethereum3'
            },
            {
              id2: 44,
              name: 'Ethereum4'
            },
            {
              id2: 45,
              name: 'Ethereum5'
            },
            {
              id2: 46,
              name: 'Ethereum6'
            }
          ]
        },
        {
          id2: 5,
          name: 'Litecoin',
        },
        {
          id2: 6,
          name: 'Dash'
        },
        {
          id2: 7,
          name: 'Monero'
        },
        {
          id2: 8,
          name: 'Ethereum Classic'
        },
        {
          id2: 9,
          name: 'Nano'
        }
      ],
      selectedCurrencies: [
        {
          id2: 1,
          name: 'Bitcoin',
          disabled: false,
          children: [
            {
              id2: 22,
              name: 'Bitcoin2'
            },
            {
              id2: 25,
              name: 'Bitcoin5'
            },
            {
              id2: 26,
              name: 'Bitcoin6'
            },
          ]
        },
        {
          id2: 3,
          name: 'Ripple'
        },
        {
          id2: 2,
          name: 'Ethereum'
        },
        {
          id2: 4,
          name: 'Bitcoin Cash2233',
          // disabled: true,
          children: [
            {
              id2: 42,
              name: 'Ethereum2'
            },
            {
              id2: 43,
              name: 'Ethereum3'
            },
            {
              id2: 44,
              name: 'Ethereum4'
            },
            {
              id2: 45,
              name: 'Ethereum5'
            },
            {
              id2: 46,
              name: 'Ethereum6'
            }
          ]
        }

      ],
    }
  },
  methods: {
    resetItems(){
      this.toggle = this.toggle ? false : true;
      this.selectedCurrencies = [];
      this.cryptocurrencies = [
        {
          id: 1,
          name: 'Bitcoin'
        },
        {
          id: 2,
          name: 'USD Coin'
        },
        {
          id: 3,
          name: 'Ratomorto Coin'
        },

      ];
    },
    testEvent(list) {
      console.log(list)
    }
  }
}
</script>

<style scoped>
</style>