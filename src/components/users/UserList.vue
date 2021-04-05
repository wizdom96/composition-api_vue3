<template>
  <base-container>
    <h2>Active Users</h2>
    <base-search @search="updateSearch" :search-term="enteredSearchTerm"></base-search>
    <div>
      <button @click="sort('asc')" :class="{selected: sorting === 'asc'}">Sort Ascending</button>
      <button @click="sort('desc')" :class="{selected: sorting === 'desc'}">Sort Descending</button>
    </div>
    <ul>
      <user-item
        v-for="user in displayedUsers"
        :key="user.id"
        :user-name="user.fullName"
        :id="user.id"
        @list-projects="$emit('list-projects', $event)"
      ></user-item>
    </ul>
  </base-container>
</template>

<script>
import UserItem from './UserItem.vue';
import { ref, computed, watch} from 'vue';
export default {
  components: {
    UserItem,
  },
  props: ['users'],
  setup(props){

      const enteredSearchTerm = ref(''); 
      const activeSearchTerm = ref(''); 
      const sorting = ref(''); 
      
      const availableUsers = computed(function(){
         let users = [];
      if (activeSearchTerm.value) {
         users = props.users.filter((usr) =>
           usr.fullName.toLowerCase().includes(activeSearchTerm.value.toLowerCase())
           );
         } else if (users) {
          users = props.users;
         }
          return users;
      });
      
      const updateSearch = function(val){
        return enteredSearchTerm.value = val;
      }; 
      const sort = function(mode){
        return sorting.value = mode;
      };

      const displayedUsers = computed(function(){
         if (!sorting.value) {
        return availableUsers.value;
      }
      return availableUsers.value.slice().sort((u1, u2) => {
        if (sorting.value === 'asc' && u1.fullName > u2.fullName) {
          return 1;
        } else if (sorting.value === 'asc') {
          return -1;
        } else if (sorting.value === 'desc' && u1.fullName > u2.fullName) {
          return -1;
        } else {
          return 1;
        }
      })
      });
      watch(enteredSearchTerm,function(newValue){
          setTimeout(() => {
        if (newValue === enteredSearchTerm.value) {
          activeSearchTerm.value = newValue;
        }
      }, 300);
      })

      return{
        enteredSearchTerm,
        activeSearchTerm,
        updateSearch,
        displayedUsers,
        sort,
        sorting

      }
  }
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>