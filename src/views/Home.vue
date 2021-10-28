<template>
  <div class="home">
  <!-- @mouseover="isPaused=true;" -->
    <!-- @mouseleave="isPaused=false;" -->
  <b-card no-body class="overflow-hidden mx-auto" style="max-width: 70%;" 
    >
    <b-row no-gutters>
      <b-col md="6">
        <b-card-img :src="currentObject.primaryImageSmall?currentObject.primaryImageSmall:require('..//assets/dummy-image-square.jpg')" alt="Image" class="rounded-0"></b-card-img>
      </b-col>
      <b-col md="6">
        <b-card-body>
          <b-card-title>
            {{currentObject.title}}
          </b-card-title>
        </b-card-body>
      <b-button href="javascript:;" @click="show=!show;isPaused=show;" variant="dark">{{show?'Hide Details':'Show Details'}}</b-button>
      <b-card v-if="show" style="height: 80vh;overflow-y: scroll;">

          <b-card-text>
            {{currentObject.department}}<br>
            {{currentObject.accessionYear}}<br>
            {{currentObject.objectDate}}<br>
            {{currentObject.creditLine}}<br>
          </b-card-text>
        <b-carousel
          v-if="currentObject.additionalImages.length > 0"
          id="carousel-1"
          :interval="4000"
          controls
          indicators
          background="#ababab"
          style="text-shadow: 1px 1px 2px #333;"
        >
          <!-- Slides with image only -->
          <b-carousel-slide v-for="(img,key) in currentObject.additionalImages" :key=key :img-src="img" style=""></b-carousel-slide>
        </b-carousel>
         <pre style="text-align: left;">{{ currentObject}}</pre>
      </b-card>
      </b-col>
    </b-row>
  </b-card>
  </div>
</template>

<script>
// @ is an alias to /src

export default {
  name: "Home",

  data() {
    return {
      mainUrl: "https://collectionapi.metmuseum.org/public/collection/v1/",
      objectWithImages: [],
      currentObject: {},
      isPaused: false,
      time: 0,
      show:false,
    };
  },
  mounted() {
    this.apicall();
  },
  methods: {
    apicall() {
      let newUrl = this.mainUrl + "search";
      let queryParams = {
        q: "https://images",
        hasImages: true,
      };
      this.axios
        .get(newUrl, { params: queryParams })
        .then((response) => {
          // console.log(response.data);
          this.objectWithImages = response.data.objectIDs;
        });
    },

    objectapicall(id) {
      let newUrl = this.mainUrl + "objects/";
      this.axios.get(newUrl + id).then((response) => {
        // console.log(response.data);
        this.currentObject = response.data;
      });
    },
    interval() {
        if(!this.isPaused) {
          this.time+=1;
        }
      }
  },
  watch: {
    objectWithImages: function (newval) {
      // console.log(oldval, newval, newval.length);
      setInterval(this.interval, 1000);
      this.objectapicall(newval[0]);
    },
    time: function (newval) {
      if(newval%10==0){
      // console.log(oldval, newval, newval/10);
        this.objectapicall(this.objectWithImages[newval/10]);
      }
      if(newval/10==this.objectWithImages.length){
        this.time=0;
      }
    },
    isPaused: function (newval) {
      if(newval){
        console.log('interval Paused');
      }
      else{
        console.log('interval continue');
      }
    },
  },
};
</script>
