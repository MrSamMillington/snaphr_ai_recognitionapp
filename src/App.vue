<template>
  <v-app>
    <v-toolbar
      app
      color="blue"
    >
      <v-toolbar-title v-text="title" class="white--text"></v-toolbar-title>
    </v-toolbar>
    <v-content>
      <!-- Our code here  -->
      <v-layout
        column
        wrap
        class="my-5"
        align-center
      >
        <v-flex xs12 sm12 m12 class="my-3">
          <div class="text-xs-center">
            <h2 class="display-3">Upload</h2>
            <span class="subheading">
              This should be a valid image file
            </span>
            <br><br>
            <app-upload v-model="fileName" @formData="getFormData" ></app-upload>
            <v-btn @click.native="uploadFiles" class="secondary 1">submit<v-icon right light>cloud_upload</v-icon></v-btn>
          </div>
        </v-flex>
      </v-layout>
    </v-content>
    <v-footer :fixed="fixed" app color="teal">
      <span>&copy; 2018</span>
    </v-footer>
  </v-app>
</template>

<script>
import Upload from './components/FileUpload.vue'
const Clarifai = require('clarifai')

const app = new Clarifai.App({
  apiKey: '91292dd18aae49c182b55181474bf900'
})

export default {

  name: 'App',
  data () {
    return {
      title: 'ai recognition app',
      formData: new FormData(),
      fileName: '',
      fixed: true
    }
  },
  components: {
    AppUpload: Upload
  },
  methods: {
    getFormData(e){
      this.formData = e[0]; //just get the first
    },
    uploadFiles(){

      if(this.fileName !== ''){ //quick check the file has been selected
        //put the upload magic here
        const image = this.formData.get('file')
        console.log(image);
        console.log(this.formData.get('path'));
        this.getBase64(image).then(
          data => {
            console.log(data.replace('data:image/jpeg;base64,', ''));
            app.models.predict(Clarifai.GENERAL_MODEL, {base64: data.replace('data:image/jpeg;base64,', '')}).then(
              function(response) {
                // do something with response
                console.log(response)
              },
              function(err) {
                // there was an error
                console.log(err)
              });
          }
        )
      }
    },
    getBase64(file) {
      return new Promise((resolve, reject) => {
        const reader = new FileReader();
        reader.readAsDataURL(file);
        reader.onload = () => resolve(reader.result);
        reader.onerror = error => reject(error);
      });
    }
  }
}
</script>
