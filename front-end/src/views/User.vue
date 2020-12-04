<template>
<div class="home">
  <section class="user-gallery">
    <div class="users" v-for="item in items" :key="item.id">
      <h1>{{item.name}}</h1>
      <p>{{item.age}}</p>
      <p>{{item.favColor}}</p>
    </div>
  </section>
  <div id="footer">
      <p>Page Author: James Peterson</p>
      <p><a href="https://github.com/BYU-CS-260-Winter-2020/lab-4-museum-of-ordinary-objects-james-peterson11.git">GitHub Repository</a></p>
  </div>
</div>
</template>

<script>
// @ is an alias to /src
import axios from 'axios';
export default {
  name: 'User',
  data() {
    return {
     items: [],
    }
  },
  created() {
    this.getItems();
  },
  methods: {
    async getItems() {
      try {
        let response = await axios.get("/api/users/");
        this.items = response.data;
        return true;
      } catch (error) {
       // console.log(error);
      }
    },
  }
}
</script>

<style scoped>
#footer {
  display: flex;
  flex-shrink: 0;
  height: 50px;
  width: 100%;
  color: #020E14;
  flex-direction: row;
  justify-content: space-around;
  background-color: #D3BEB5;
}
</style>
