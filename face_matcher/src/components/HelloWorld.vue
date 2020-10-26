<template>
  <div>
    <div class="container-md">
      <div class="row">
        <div class="col-sm">
          <select class="custom-select" v-model="selected" required>
            <option disabled value="">Select one please</option>
            <option
              v-for="option in this.options"
              v-bind:value="option.id"
              v-bind:key="option.id"
              >{{ option.name }}</option
            >
          </select>
        </div>
        <div class="col-sm">
          <!-- Styled -->
          <b-form-file
            @change="onFileSelected"
            placeholder="Choose a image or drop it here..."
            drop-placeholder="Drop image here..."
          ></b-form-file>

          <div id="preview">
            <br />
            <img class="rounded" v-if="url" :src="url" />
          </div>
        </div>
        <div class="col-">
          <button type="button" class="btn btn-info" @click="onUpload">
            Upload
          </button>
        </div>
      </div>
      <br/>
      <div v-if="similarity !=0" class="row" style="
    place-content: center;
    color: white;
">
        <h2>Similarity with our DataBase: {{this.similarity}}%</h2>
      </div>
       <div v-if="similarity =0" class="row" style="
    place-content: center;
    color: white;
">
        <h2>There are not similarity with our DataBase</h2>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "HelloWorld",
  data() {
    return {
      similarity:'',
      selectedFile: null,
      url: null,
      selected: null,
      options: [
        { id: "Collections", name: "Añadir en la BD" },
        { id: "Compare", name: "Comparar" }
      ]
    };
  },
  methods: {
    onFileSelected(event) {
      this.selectedFile = event.target.files[0];
      this.url = URL.createObjectURL(this.selectedFile);
    },
    async setSimilarity(name){
      const response = await axios.get(`https://0yy3h1j9sl.execute-api.us-west-2.amazonaws.com/produccion?fileName=` + name)
      var myObj = JSON.parse(response.data);
      console.log(myObj.similarity);
      this.similarity = myObj.similarity;
    },
    onUpload() {
      var AWS = require("aws-sdk");
      var bucketName = "cloud2020grupo1";
      var bucketRegion = "us-west-2";
      var route = encodeURIComponent(this.selected) + "/";
      var creds = new AWS.Credentials({
        accessKeyId: process.env.VUE_APP_AWS_ACCESS_KEY_ID,
        secretAccessKey: process.env.VUE_APP_AWS_SECRET_ACCESS_KEY
      });
      AWS.config.update({
        region: bucketRegion,
        credentials: creds
      });
      var upload = new AWS.S3.ManagedUpload({
        params: {
          Bucket: bucketName,
          Key: route + this.selectedFile.name,
          Body: this.selectedFile,
          ACL: "public-read"
        }
      });
      var name = this.selectedFile.name;
      
      console.log(name);
      var promise = upload.promise();
      let that = this;
      promise.then(
        async function() {
          alert("Successfully uploaded photo.");
          await that.setSimilarity(name);
        },
        function(err) {
          return alert("There was an error uploading your photo: " + err);
        }
        
      );

    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
