<template>
  <div>
    <div class="create-from">
      <form action="" @submit.prevent="up">
        <h4 style="text-align: center">Create list</h4>
        <div class="create-form-input">
          <label for="">Name:</label>
          <input type="text" placeholder="Enter Name" v-model="name" required>
        </div>
        <div class="create-form-input"  >
          <label for="">Job:</label>
          <input type="text" placeholder="Enter Job" v-model="job" required>
        </div>
        <div class="create-form-input"  >
          <label for="">Image:</label>
          <input type="file" @change="previewImage"  required>
        </div>
        <div class="create-form-input">
          <img :src="imageData" alt="">
        </div>
        <div style="text-align: center">
          <button class="btn">submit</button>
        </div>
      </form>
    </div>

    <div class="detail">
      <ul style="list-style: none;display: flex" >
        <li class="detail-contact" v-for="(list,index) in lists" :key="index">
          <img :src="list.imageData" alt="" width="100">

          <div>
            <input v-if="list.isEdit" type="text" v-model="list.name" @keyup.enter="updating(list)"  required>
            <div @dblclick="ed(list)" v-else> Name - {{list.name}}</div>
          </div>

          <div @dblclick="ed">
            <input v-if="list.isEdit" type="text" v-model="list.job" @keyup.enter="updating(list)"  required>
            <div @dblclick="ed(list)" v-else> Job - {{list.job}}</div>
          </div>

          <div>
            <button class="btn" @click="del(list)">Delete</button>
          </div>

        </li>
      </ul>
    </div>

  </div>

</template>

<script>
import axios from "axios";

export default {
  name: "Create",
  data() {
    return {
      name: "",
      job: "",
      lists: [],
      imageData: "",
    }
  },

  mounted() {
    axios.get("http://localhost:3000/about").then((res)=>{
      this.lists = res.data
    })
  },

  methods: {
    up() {
      this.lists.push({
        name : this.name,
        job : this.job,
        imageData : this.imageData,
      });

      axios.post("http://localhost:3000/about",{
        name : this.name,
        job : this.job,
        image : this.imageData,
        isEdit : false
      }).then((res)=>{
      });

      this.name = ""
      this.job = ""
      this.imageData = ""
    },

    previewImage(e){
      let input = e.target;
      // Ensure that you have a file before attempting to read it
      if (input.files && input.files[0]) {
        // create a new FileReader to read this image and convert to base64 format
        let reader = new FileReader();
        let imageName = e.target.files[0].name
        console.log(imageName)
        // Define a callback function to run, when FileReader finishes its job
        reader.onload = (e) => {
          // Note: arrow function used here, so that "this.imageData" refers to the imageData of Vue component
          // Read image as base64 and set to imageData
          this.imageData = e.target.result;
        }
        // Start the reader job - read file as a data url (base64 format)
        reader.readAsDataURL(input.files[0]);
      }
    },

    ed(x){
      x.isEdit = true;
    },

    updating(x){
      x.isEdit = false
      axios.put("http://localhost:3000/about/"+x.id,{
        name : x.name,
        job : x.job,
        image : x.imageData,
        isEdit : false
      })
    },

    del(x){
      console.log(x.id);
      this.lists.splice(x.id-1,1);
      axios.delete("http://localhost:3000/about/"+x.id)
    }
  },
}
</script>

<style>

</style>