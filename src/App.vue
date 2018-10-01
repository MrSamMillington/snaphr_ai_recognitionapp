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
            <br><br>
            <app-bar-chart :data="chartData" ></app-bar-chart>
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
import BarChart from './components/charts/VisionBar.vue'
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
      fixed: true,
      chartData: [],
    }
  },
  components: {
    AppUpload: Upload,
    AppBarChart: BarChart
  },
  methods: {
    getFormData(e){
      this.formData = e[0]; //just get the first
    },
    uploadFiles(){

      if(this.fileName !== ''){ //quick check the file has been selected
        //put the upload magic here
        console.log('here')
        const image = this.formData.get('file')
        this.getBase64(image).then(
          data => {
            app.models.predict(Clarifai.GENERAL_MODEL, {base64: data.replace('data:image/jpeg;base64,', '')}).then(
              (response) => {
                // do something with response
                //console.log(response)

                console.log('out')


                const array = response.outputs[0].data.concepts;

                console.log(array)

                this.chartData = array;

                console.log(this.chartData);

              //  this.setChartData(array);
              //  console.log(this.chartData)

              },
              (err) => {
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
    },
    setChartData(array){
      console.log('here')
      console.log(array)
      this.chartData = array;
    }
  }
}
</script>
