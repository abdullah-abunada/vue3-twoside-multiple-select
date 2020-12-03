<template>
  <div class="row">
    <div class="col-md-5">
      <div class="left-select">
        <input
            :disabled="disabled"
            class="searchable"
            type="text"
            :placeholder="searchablePlaceholder"
            :class="'searchable ' + searchableClass"
            name="leftSearch"
            v-model="leftSearch"
            @keyup="search($event.target.value, 'left')"
            v-if="searchable">

        <ul :class="(disabled) ? 'ul-disabled' : null">
          <li v-for="(item, index) in list.left" :key="index" >
            <ul v-if="item.children" class="ul-category">
              <li class="li-category-grp" @click="addGroupItem(item, index)" :class="(!disabled) ? (item[showField]) ? 'li-disabled' : 'list-hover' : null">
                {{ item[textField] }}
              </li>
              <li v-for="(item2, index2) in item.children" :key="index2" class="li-category-list" @click="addChildItem(item, index, item2, index2)" :class="(!disabled) ? (item2[showField]) ? 'li-disabled' : 'list-hover' : null">
                {{ item2[textField] }}
              </li>
            </ul>
            <ul class="ul-none-category" v-else>
              <li @click="addItem(item, index)" :class="(!disabled) ? (item[showField]) ? 'li-disabled' : 'list-hover' : null">
                {{ item[textField] }}
              </li>
            </ul>
          </li>
        </ul>
        <p v-if="showTextSelectedItem" class="text-selected-items"><strong>{{ leftCount() }}</strong> {{ textItems }}</p>
      </div>
    </div>

    <div class="switch">
      <img src="../public/switch.png" alt="Switch">
    </div>

    <div class="col-md-5">
      <div class="right-select">
        <input
            :disabled="disabled"
            type="text"
            :placeholder="searchablePlaceholder"
            :class="'searchable ' + searchableClass"
            name="rightSearch"
            v-model="rightSearch"
            @keyup="search($event.target.value, 'right')"
            v-if="searchable">

        <ul :class="(disabled) ? 'ul-disabled' : null">
          <li v-for="(item, index) in list.right" :key="index">
            <ul v-if="item.children" class="ul-category">
              <li class="li-category-grp" @click="removeGroupItem(item, index)" :class="(!disabled) ? (item[showField]) ? 'li-disabled' : 'list-hover' : null">
                {{ item[textField] }}
              </li>
              <li v-for="(item2, index2) in item.children" :key="index2" class="li-category-list" @click="removeChildItem(item, index, item2, index2)" :class="(!disabled) ? (item2[showField]) ? 'li-disabled' : 'list-hover' : null">
                {{ item2[textField] }}
              </li>
            </ul>
            <ul class="ul-none-category" v-else>
              <li @click="removeItem(item, index)" :class="(!disabled) ? (item[showField]) ? 'li-disabled' : 'list-hover' : null">
                {{ item[textField] }}
              </li>
            </ul>
          </li>
        </ul>

        <p  v-if="showTextSelectedItem" class="text-selected-items"><strong>{{ rightCount() }}</strong> {{ textSelectedItems[list.right.length == 1 ? 'one' : 'greaterThanOne'] }}</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'MultipleSelect',
  props: {
    items: {
      type: Array,
      required: true
    },
    selectedItems: {
      type: Array,
      default: function() { return[] }
    },
    valueField: {
      default: 'value',
      type: String,
    },
    textField: {
      default: 'text',
      type: String
    },
    showField: {
      default: 'disabled',
      type: String
    },
    childField: {
      default: 'children',
      type: String
    },
    searchable: {
      type: Boolean,
      default: false,
    },
    searchablePlaceholder: {
      type: String,
      default: 'Search'
    },
    searchableClass: {
      type: String,
      default: 'form-control mb-2'
    },
    textItems: {
      type: String,
      default: 'items'
    },
    showTextSelectedItem: {
      type: Boolean,
      default: true
    },
    textSelectedItems: {
      type: Object,
      default: function() { return {
        one: 'selected item',
        greaterThanOne: 'selected items'
      } }
    },
    sorteable: {
      type: Boolean,
      default: false,
    },
    orderBy: {
      type: String,
      default: 'text'
    },
    limitSelectedItems: {
      type: Number,
      default: 999
    },
    disabled: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      list: {
        left: this.items,
        right: this.selectedItems,
      },
      rightSearch: '',
      leftSearch: '',
    }
  },
  created(){
    var self = this;
    if(this.selectedItems){
      this.selectedItems.forEach(function(selectedItem, index){
        if(selectedItem[self.childField]){
          let selectedItemChild = selectedItem[self.childField];
          for(var i=0;i<selectedItemChild.length;i++){
            let pos = self.findChildWithAttr(self.list.left, self.valueField, selectedItem[self.valueField], selectedItemChild[i][self.valueField]);
            if(pos >= 0){
              self.list.left.forEach(function(leftItem,index){
                if(leftItem[self.valueField] == selectedItem[self.valueField]){
                  leftItem[self.childField].splice(pos, 1);
                  if(leftItem[self.childField].length == 0) {
                    self.list.left.splice(index,1)
                  }
                }
              })
            }
          }

        }else{
          let pos = self.findWithAttr(self.list.left, self.valueField, selectedItem[self.valueField]);
          if(pos >= 0){
            self.list.left.splice(pos, 1);
          }
        }

      })
    }
    if(this.sorteable){
      this.list.left = this.sort(this.list.left, this.orderBy, 0);
      this.list.right = this.sort(this.list.right, this.orderBy, 0);
    }
  },
  methods: {
    leftCount() {
      var self = this;
      var result = this.list.left.length
      this.list.left.forEach(function(item){
        if(item[self.childField]){
          result+=item[self.childField].length
        }
      })
      return result
    },
    rightCount() {
      var self = this;
      var result = this.list.right.length
      this.list.right.forEach(function(item){
        if(item[self.childField]){
          result+=item[self.childField].length
        }
      })
      return result
    },
    addGroupItem(item, index) {
      var self = this;
      if(this.disabled) {
        return false
      }

      if(item.disabled) {
        return false
      }

      let totalItems = this.list.right.length;
      if(totalItems >= this.limitSelectedItems){
        return false;
      }

      var existChk = false;
      this.list.right.forEach(function(rightItem){
        if(item[self.valueField] == rightItem[self.valueField]){
          item[self.childField].forEach(function(childItem){
            rightItem[self.childField].push(childItem)
          })

          // sort
          rightItem[self.childField] = self.sort(rightItem[self.childField], self.orderBy, 0);
          existChk = true
        }
      })

      if(!existChk) {
        this.list.right.push(item);
      }
      this.list.left.splice(index, 1)
      // sort
      this.list.right = this.sort(this.list.right, this.orderBy, 0);
      this.$emit('itemAdded', this.list.right)
      this.$emit('selectedListModified', this.list.right)
    },
    addChildItem(parentItem, parentIndex, item, index) {
      var self = this;
      if(this.disabled) {
        return false
      }

      if(item.disabled) {
        return false
      }

      let totalItems = this.list.right.length;
      if(totalItems >= this.limitSelectedItems){
        return false;
      }
      var existChk = false;
      this.list.right.some(function(rightItem){
        if(parentItem[self.valueField] == rightItem[self.valueField]){
          rightItem[self.childField].push(item)
          // sort
          rightItem[self.childField] = self.sort(rightItem[self.childField], self.orderBy, 0);
          existChk = true
        }
      })

      if(!existChk) {
        // 따로 만들어서 push 해야함 -- 다시 확인해야함
        var parentArray = this.deepClone(parentItem)

        this.list.right.push(parentArray);
        this.list.right.some(function(rightItem){
          if(parentItem[self.valueField] == rightItem[self.valueField]){
            var itemArray = new Array()
            itemArray[0]=item
            rightItem[self.childField] = itemArray
          }
        })
      }

      parentItem[this.childField].splice(index, 1)

      if(parentItem[this.childField].length == 0){
        this.list.left.splice(parentIndex,1)
      }

      // sort
      this.list.right = this.sort(this.list.right, this.orderBy, 0);
      this.$emit('itemAdded', this.list.right)
      this.$emit('selectedListModified', this.list.right)
    },
    addItem(item, index) {
      if(this.disabled) {
        return false
      }

      if(item.disabled) {
        return false
      }

      let totalItems = this.list.right.length;
      if(totalItems >= this.limitSelectedItems){
        return false;
      }
      this.list.right.push(item);
      this.list.left.splice(index, 1);
      // sort
      this.list.right = this.sort(this.list.right, this.orderBy, 0);
      this.$emit('itemAdded', this.list.right)
      this.$emit('selectedListModified', this.list.right)
    },
    removeGroupItem(item, index) {
      var self = this;
      if(this.disabled) {
        return false
      }

      if(item.disabled) {
        return false
      }

      var existChk = false;
      this.list.left.forEach(function(leftItem){
        if(item[self.valueField] == leftItem[self.valueField]){
          item[self.childField].forEach(function(childItem){
            leftItem[self.childField].push(childItem)
          })

          // sort
          leftItem[self.childField] = self.sort(leftItem[self.childField], self.orderBy, 0);
          existChk = true
        }
      })

      if(!existChk) {
        this.list.left.push(item);
      }
      this.list.right.splice(index, 1)
      this.rightSearch = null;
      this.search('','right')
      // sort
      this.list.left = this.sort(this.list.left, this.orderBy, 0);
      this.$emit('itemRemoved', this.list.left)
      this.$emit('selectedListModified', this.list.right)
    },
    removeChildItem(parentItem, parentIndex, item, index) {
      var self = this;
      if(this.disabled) {
        return false
      }

      if(item.disabled) {
        return false
      }
      var existChk = false;
      this.list.left.some(function(leftItem){
        if(parentItem[self.valueField] == leftItem[self.valueField]){
          leftItem[self.childField].push(item)
          // sort
          leftItem[self.childField] = self.sort(leftItem[self.childField], self.orderBy, 0);
          existChk = true
        }
      })

      if(!existChk) {
        // 따로 만들어서 push 해야함 -- 다시 확인해야함
        var parentArray = this.deepClone(parentItem)

        this.list.left.push(parentArray);
        this.list.left.some(function(leftItem){
          if(parentItem[self.valueField] == leftItem[self.valueField]){
            var itemArray = new Array()
            itemArray[0]=item
            leftItem[self.childField] = itemArray
          }
        })
      }

      parentItem[this.childField].splice(index, 1)

      if(parentItem[this.childField].length == 0){
        this.list.right.splice(parentIndex,1)
      }

      this.rightSearch = null;
      this.search('','right')
      // sort
      this.list.left = this.sort(this.list.left, this.orderBy, 0);
      this.$emit('itemRemoved', this.list.left)
      this.$emit('selectedListModified', this.list.right)
    },
    removeItem(item, index){
      var self = this;
      if(this.disabled) {
        return false
      }

      if(item.disabled) {
        return false
      }
      this.list.left.push(item);
      this.list.right.splice(index, 1);
      this.list.right = this.getSelectedItems();
      this.rightSearch = null;
      this.search('','right')
      // sort
      this.list.left = this.sort(this.list.left, this.orderBy, 0);
      this.$emit('itemRemoved', this.list.left)
      this.$emit('selectedListModified', this.list.right)
    },
    search(text, position){
      var self = this;
      this.list[position] = (position == 'left') ? this.getUnselectedItems() : this.getSelectedItems();
      var originalArray = this.deepClone(this.list[position])

      if(text){
        var childChk=false
        this.list[position] = this.list[position].filter(function(result){
          if(result[self.childField]){
            // child exact matching...fail..검색 후 자꾸 좌우로 옮기고 나서, 리스트가 이상해진다.
            // result[self.childField] = result[self.childField].filter(function(result2){
            //   return result2[self.textField].toLowerCase().includes(text.toLowerCase());
            // })
            result[self.childField].forEach(function(child){
              if(child[self.textField].toLowerCase().includes(text.toLowerCase())){
                childChk= true;
              }
            })
            if(childChk) return true
          }
          return result[self.textField].toLowerCase().includes(text.toLowerCase());
        })
        if(position == 'left'){
          this.items = originalArray
        }else{
          this.selectedItems = originalArray
        }
      }
    },
    getUnselectedItems() {
      var self = this;
      let data = this.items;
      let selectedItems = this.selectedItems;
      data.forEach(function(leftItem, index){
        selectedItems.forEach(function(rightItem){
          if(leftItem[self.valueField] == rightItem[self.valueField]){
            if(leftItem[self.childField] && rightItem[self.childField]){
              leftItem[self.childField].forEach(function(leftChildItem, index2){
                rightItem[self.childField].forEach(function(rightChildItem){
                  if(leftChildItem[self.valueField] == rightChildItem[self.valueField]){
                    leftItem[self.childField].splice(index2,1)
                  }
                })
              })
            }else{
              data.splice(index, 1);
            }
          }
        })
      })
      return this.items;
    },
    getSelectedItems() {
      var self = this;
      let items = this.items;
      let selectedItems = this.selectedItems;
      selectedItems.forEach(function(selectedItem, index){
        items.forEach(function(item){
          if(item[self.valueField] == selectedItem[self.valueField]){
            if(item[self.childField] && selectedItem[self.childField]){
              selectedItem[self.childField].forEach(function(rightChildItem, index2){
                item[self.childField].forEach(function(leftChildItem){
                  if(rightChildItem[self.valueField] == leftChildItem[self.valueField]){
                    selectedItem[self.childField].splice(index2,1)
                  }
                })
              })
            }else{
              selectedItems.splice(index, 1);
            }

          }
        })
      })
      return selectedItems;
    },
    findWithAttr(array, attr, value) {
      for (var i = 0; i < array.length; i++) {
        if (array[i][attr] === value) {
          return i
        }
      }
      return -1
    },
    findChildWithAttr(array, attr, value, childValue) {
      var self = this;
      for (var i = 0; i < array.length; i++) {
        if(array[i][attr] == value){
          if(array[i][self.childField]){
            let arrayChild = array[i][self.childField];

            for(var j=0; j < arrayChild.length; j++){
              if (arrayChild[j][attr] === childValue) {
                return j
              }
            }

          }
        }
      }
      return -1
    },
    sort(items, property, direction) {
      function compare(a, b) {
        if(!a[property] && !b[property]) {
          return 0;
        } else if(a[property] && !b[property]) {
          return -1;
        } else if(!a[property] && b[property]) {
          return 1;
        } else {
          const value1 = a[property].toString().toUpperCase(); // ignore upper and lowercase
          const value2 = b[property].toString().toUpperCase(); // ignore upper and lowercase
          if (value1 < value2) {
            return direction === 0 ? -1 : 1;
          } else if (value1 > value2) {
            return direction === 0 ? 1 : -1;
          } else {
            return 0;
          }
        }
      }

      return items.sort(compare);
    },
    deepClone(obj) {
      if(obj === null || typeof obj !== 'object') {
        return obj;
      }

      const result = Array.isArray(obj) ? [] : {};

      for(let key of Object.keys(obj)) {
        result[key] = this.deepClone(obj[key])
      }

      return result;
    }
  }
}
</script>

<style scoped>
ul{
  list-style-type: none;
  padding: 0;
  height: 270px;
  overflow: auto;
  border: 1px solid rgba(153, 153, 153, 0.2);
  width: 100%;
  margin: 5px 0;
}
ul li{
  padding: 8px;
}
.ul-category {
  height: auto;
  border: none;
}
.ul-none-category {
  height: auto;
  border: none;
  margin: -8px;
}
.li-category-grp {
  background: #578EBE;
  border-radius: 4px;
  border: none;
}
.li-category-list {
  padding-left: 20px !important;
  border: none;
}
.ul-disabled {
  background-color: #f5f5f5;
}
.li-disabled {
  background-color: #f5f5f5;
}
.list-hover:hover{
  background: #09f;
  cursor: pointer;
  color: #fff;
}
.searchable{
  border: 1px solid #ddd;
  padding: 10px;
  width: 100%;
}
.text-selected-items{
  margin: 0;
  padding: 0;
}
.switch{
  display: flex;
  flex-direction: column;
  justify-content: center;
}
/* Responsive */
@media (max-width: 768px)
{
  .switch{
    text-align: center;
    margin: 0 auto;
    padding: 15px 0;
  }
}
</style>