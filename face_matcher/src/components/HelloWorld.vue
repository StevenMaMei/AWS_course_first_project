<template>
  <div>
    <select class="form-control" v-model="selected" required>
      <option disabled value="">Select one please</option>
      <option v-for="option in this.options" v-bind:value="option.id"  v-bind:key="option.id">{{ option.name }}</option>
    </select>
    <input type="file" @change="onFileSelected">
    <button @click="onUpload">Upload</button>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  data(){
    return {
      selectedFile: null,
      selected: null,
      options: [{id: "Collections", name: "Añadir en la BD"},{id: "Compare", name: "comparar"}]
    }
  },
  methods: {
    onFileSelected(event){
      this.selectedFile = event.target.files[0]
    },
    onUpload(){
      var AWS = require('aws-sdk');
      var bucketName = "cloud2020grupo1";
      var bucketRegion = "us-west-2"
      var route = encodeURIComponent(this.selected)+"/"
      var creds = new AWS.Credentials({
        accessKeyId: process.env.VUE_APP_AWS_ACCESS_KEY_ID, secretAccessKey: process.env.VUE_APP_AWS_SECRET_ACCESS_KEY
      });
      AWS.config.update({
        region: bucketRegion,
        credentials: creds
      });
      var upload = new AWS.S3.ManagedUpload({
        params: {
          Bucket: bucketName,
          Key: route+this.selectedFile.name,
          Body: this.selectedFile,
          ACL: "public-read"
        }
      });
      var promise = upload.promise();
      promise.then(
        function() {
          alert("Successfully uploaded photo.");
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
<style scoped>

</style>
