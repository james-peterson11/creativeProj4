<template>
<div class="admin">
  <h1>The Admin Page!</h1>
    <div class="heading">
      <div class="circle">1</div>
      <h2>Add an Item</h2>
    </div>
    <div class="add">
      <div class="form">
        <input v-model="title" placeholder="Title">
        <p></p>
        <input type="file" name="photo" @change="fileChanged">
        <button @click="upload">Upload</button>
      </div>
      <div class="upload" v-if="addItem">
        <h2>{{addItem.title}}</h2>
        <img :src="addItem.path" />
      </div>
    </div>

    <div class="circle">2</div>
    <h2>Add a User</h2>
    <div class="user">
      <div class="form">
        <input v-model="name" placeholder="User's Name">
        <p></p>
        <input v-model="age" placeholder="How old are you?">
        <p></p>
        <input v-model="favColor" placeholder="What is your faveColor?:)">
        <button @click="insert">Insert</button>
      </div>
      <div class="insert" v-if="addUser">
        <h2>{{addUser.name}}</h2>
      </div>
    </div>


    <div class="heading">
      <div class="circle">3</div>
      <h2>Edit/Delete an Item</h2>
    </div>
    <div class="edit">
      <div class="form">
        <input v-model="findTitle" placeholder="Search">
        <div class="suggestions" v-if="suggestions.length > 0">
          <div class="suggestion" v-for="s in suggestions" :key="s.id" @click="selectItem(s)">{{s.title}}
          </div>
        </div>
      </div>
      <div class="upload" v-if="findItem">
        <input v-model="findItem.title">
        <p></p>
        <img :src="findItem.path" />
      </div>
      <div class="actions" v-if="findItem">
        <button @click="deleteItem(findItem)">Delete</button>
	<button @click="editItem(findItem)">Edit</button>
      </div>
    </div>




    <div class="circle">4</div>
    <h2>Edit/Delete an User</h2>
    <div class="edit">
      <div class="form">
        <input v-model="findPerson" placeholder="Name?">
        <div class="suggestions2" v-if="suggestions2.length > 0">
          <div class="suggestion" v-for="s in suggestions2" :key="s.id" @click="selectUser(s)">{{s.name}}
          </div>
        </div>
      </div>
      <div class="insert" v-if="findUser">
        <input v-model="findUser.name">
        <p></p>
        <img :src="findUser.path" />
      </div>
      <div class="actions" v-if="findUser">
        <button @click="deleteUser(findUser)">Delete</button>
        <button @click="editUser(findUser)">Edit</button>
      </div>
    </div>




    <div id="footer">
      <p>Page Author: James Peterson</p>
      <p><a href="https://github.com/BYU-CS-260-Winter-2020/lab-4-museum-of-ordinary-objects-james-peterson11.git">GitHub Repository</a></p>
  </div>
</div>
</template>


<script>
import axios from 'axios';
export default {
  name: 'Admin',
  data() {
    return {
      title: "",
      file: null,
      addItem: null,

      name: "",
      age: "",
      favColor: "",
      addUser: null,

      items: [],
      findTitle: "",
      findItem: null,

      users: [],
      findPerson: "",
      findUser: null,
    }
  },
  computed: {
    suggestions() {
      let items = this.items.filter(item => item.title.toLowerCase().startsWith(this.findTitle.toLowerCase()));
      return items.sort((a, b) => a.title > b.title);
    },
    suggestions2() {
      let items = this.users.filter(item => item.name.toLowerCase().startsWith(this.findPerson.toLowerCase()));
      return items.sort((a, b) => a.name > b.name);
    }
  },
  created() {
    this.getItems();
    this.getUsers();
  },
  methods: {
    fileChanged(event) {
      this.file = event.target.files[0]
    },
    async upload() {
      try {
        const formData = new FormData();
        formData.append('photo', this.file, this.file.name)
        let r1 = await axios.post('/api/photos', formData);
        let r2 = await axios.post('/api/items', {
          title: this.title,
          path: r1.data.path
        });
        this.addItem = r2.data;
      } catch (error) {
       // console.log(error);
      }
    },


    async insert() {
      try {
        let r1 = await axios.post('/api/users/', {
          name: this.name,
          age: this.age,
          favColor: this.favColor
        });
        this.addUser = r1.data;
      } catch (error) {
        // console.log(error);
      }
    },
    async getUsers() {
      try {
        let response = await axios.get("/api/users/");
        this.users = response.data;
        return true;
      } catch (error) {
       // console.log(error);
      }
    },

    async getItems() {
      try {
        let response = await axios.get("/api/items");
        this.items = response.data;
        return true;
      } catch (error) {
       // console.log(error);
      }
    },
    selectUser(item) {
      this.findPerson = "";
      this.findUser = item;
    },
    async deleteUser(item) {
       try {
         await axios.delete("/api/users/" + item._id);
         this.findUser = null;
         this.getUsers();
         return true;
       } catch (error) {
        // console.log(error);
       }
     },
     async editUser(item) {
        try {
          await axios.put("/api/users/" + item._id, {
            name: this.findUser.name,
          });
          this.findUser = null;
          this.getUsers();
          return true;
        } catch (error) {
         // console.log(error);
        }
      },



    selectItem(item) {
        this.findTitle = "";
        this.findItem = item;
      },
   async deleteItem(item) {
      try {
        await axios.delete("/api/items/" + item._id);
        this.findItem = null;
        this.getItems();
        return true;
      } catch (error) {
       // console.log(error);
      }
    },
   async editItem(item) {
      try {
        await axios.put("/api/items/" + item._id, {
          title: this.findItem.title,
        });
        this.findItem = null;
        this.getItems();
        return true;
      } catch (error) {
       // console.log(error);
      }
    },
  }
}
</script>


<style scoped>
.image h2 {
  font-style: italic;
  font-size: 1em;
}

.heading {
  display: flex;
  margin-bottom: 20px;
  margin-top: 20px;
}

.heading h2 {
  margin-top: 8px;
  margin-left: 10px;
}

.add,
.edit {
  display: flex;
}

.circle {
  border-radius: 50%;
  width: 18px;
  height: 18px;
  padding: 8px;
  background: #333;
  color: #fff;
  text-align: center
}

/* Form */
input,
textarea,
select,
button {
  font-family: 'Montserrat', sans-serif;
  font-size: 1em;
}

.form {
  margin-right: 50px;
}

/* Uploaded images */
.upload h2 {
  margin: 0px;
}

.upload img {
  max-width: 300px;
}

/* Suggestions */
.suggestions {
  width: 200px;
  border: 1px solid #ccc;
}

.suggestion {
  min-height: 20px;
}

.suggestion:hover {
  background-color: #5BDEFF;
  color: #fff;
}

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
