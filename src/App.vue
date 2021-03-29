
<template>
  <div id="root">

    <div class="scan-progress">
     
      <nav v-show="menu">
        <div class="scan">
          <button @click="prev_scan">
            <i class="fas fa-chevron-left"></i>
          </button>
          <input type="text" v-model="scan">
          <button @click="next_scan">
            <i class="fas fa-chevron-right"></i>
          </button>
        </div>
        <swiper v-if="discoveredComplete == true" slides-per-view="auto">
          <swiper-slide v-for="page in discoveredPage" :key="page.id">
            <button @click="goTo(page.id)" :class="{current: currentPage(page.id)}">{{page.id}}</button>
          </swiper-slide>
        </swiper>
       <div v-else class="loading">
          <p>Toutes les pages sont entrain d'etre trouvé pour le scan {{scan}}</p>
          <img src="200.gif"/>
       </div>
        
      </nav>
    

      <button @click="openMenu" :class="{open: menu}">
        <i class="fas fa-bars"></i>
      </button>
    </div>

    <div class="canvas" v-for="image in getImage" :key="image">
        <scan-img :src="image"/>
    </div>

    <div class="controls">
      <button @click="prev" @keypress.enter="prev">
        <i class="fas fa-arrow-left"></i>
      </button>

      <button @click="next">
        <i class="fas fa-arrow-right"></i>
      </button>

      <!-- <div class="download" @click="forceDownload(this.getImage, 'test')">
        <i class="fas fa-download"></i>
      </div> -->
    </div>
  </div>
</template>

<script>
import ScanImg from './Components/ScanImg.vue'
import { Swiper, SwiperSlide } from 'swiper/vue';
import 'swiper/swiper.scss';
export default {
  data() {
    return {
      rootPath: "https://scansmangas.xyz/scans/shingeki-no-kyojin/",
      scan: 111,
      page: 1,
      discoveredComplete: false,
      discoveredPage: [],
      menu: false
    }
  },
  computed: {
    getImage() {
      return [
        this.path(this.rootPath, this.scan, this.page)
      ]
    }
  },
  watch: {
    scan() {
      this.page = 1
      this.discoveredComplete = false
      this.discoveredPage = []
      this.discovering()
    }
  },
  methods: {
    next() {
     if(this.discoveredComplete === true) {
       if(this.page < this.discoveredPage.length)
        this.page++
     } else { 
       this.page++
     }
    },
    prev() {
      if(this.page > 1)
        this.page--
    },
    goTo(id) {
      this.page = id
    },
    prev_scan() {
      this.scan--
    },
    next_scan() {
      this.scan++
    },
    path(root, scan, page) {
      return root + scan + '/' + page + '.jpg'
    },
    openMenu() {
      this.menu = !this.menu
    },
    validImage(page) {
      return new Promise((resolve, reject) => {
        let image = new Image()
        image.src = this.path(this.rootPath, this.scan, page)
        image.onload = () => {
          this.discoveredPage.push({
            id: page,
            link: this.path(this.rootPath, this.scan, page)
          })
          resolve(false)
        }
        image.onerror = () => {
          resolve(true)
        }
      })
    },
    async discovering() {
      let page = this.page
      let res = false
      while(res != true) {
          res = await this.validImage(page)
          page += 1
      }
      this.discoveredComplete = true
    },
    currentPage(id) {
      return id === this.page
    },
    forceDownload(url, fileName){
      var xhr = new XMLHttpRequest();
      xhr.open("GET", url, true);
      xhr.setRequestHeader('Access-Control-Allow-Headers', '*');
      xhr.setRequestHeader('Access-Control-Allow-Origin', '*');
      xhr.responseType = "blob";
      xhr.onload = function(){
          var urlCreator = window.URL || window.webkitURL;
          var imageUrl = urlCreator.createObjectURL(this.response);
          var tag = document.createElement('a');
          tag.href = imageUrl;
          tag.download = fileName;
          document.body.appendChild(tag);
          tag.click();
          document.body.removeChild(tag);
      }
      xhr.send();
    }
  },
  mounted() {
    window.addEventListener('keydown', (keyCode) => {
      if(keyCode.code == "ArrowRight") {
        this.next()
      }
      if(keyCode.code == "ArrowLeft") {
        this.prev()
      }
      if(keyCode.code == "ArrowUp") {
        this.next_scan()
      }
      if(keyCode.code == "ArrowDown") {
        this.prev_scan()
      }
      if(keyCode.code == "Escape") {
        this.openMenu()
      }
    })
     this.discovering()
  },
  components: {
    ScanImg,
    Swiper,
    SwiperSlide
  }
}
</script>

<style lang="scss">

    * {
      padding: 0;
      margin: 0;
    }

    button, input {
      &:focus {
        outline: none;
        border: none;
      }
    }

     #root {
       overflow: hidden;
       width: 100%;
       height: 100vh;
       background-color: black;
       display: flex;
       align-items: center;
       justify-content: center;

       .scan-progress {
          position: absolute;
          width: -webkit-fill-available;
          left: 0;
          top: 0;
          overflow-x: hidden;
         > button {
           &.open {
             position: relative;
             top: 0;
           }
            top: 0;
            left: 30px;
            position: fixed;
            cursor: pointer;
            pointer-events: all;
            background: #434343;
            padding: 10px 20px;
            border: none;
            border-bottom-right-radius: 10px;
            border-bottom-left-radius: 10px;
            i {
              color: white;
              font-size: 30px;
            }
         }

         nav {
           display: flex;
           flex-direction: column;
           align-items: center;
           user-select: none;
           padding: 20px;
           background: #434343;
           .swiper-container {
             max-width: 80%;
             .swiper-wrapper {
               .swiper-slide {
                 width: fit-content !important;
                 button {
                   font-family: Arial, Helvetica, sans-serif;
                   font-size: 14px;
                   background-color: black;
                   color: white;
                   cursor: grab;
                   padding: 5px;
                   margin-left: 8px;
                   border: none;
                   &.current {
                     color: black;
                     background-color: white;
                   }
                 }
               }
             }
           }
           .loading {
             align-items: center;
             display: flex;
             flex-direction: column;
             p {
               margin-bottom: 10px;
               color: rgb(224, 224, 224);
               font-size: 20px;
               font-family: Arial, Helvetica, sans-serif;
             }
             img {
               width: 50px;
             }
           }

           .scan  {
             margin-bottom: 20px;
             display: flex;
             align-items: center;
             input[type=text] {
               width: 80px;
               text-align: center;
               background: none;
               border: none;
               font-family: Arial, Helvetica, sans-serif;
               font-size: 45px;
               margin: 0 20px;
             }
             > button {
               cursor: pointer;
               background: none;
               border: none;
               i {
                 font-size: 35px;
               }
               transition: color 0.5s;
               &:hover {
                 color: white;
               }
             }
           }
         }
       }

       .canvas {
         img {
           width: 100%;
           height: 100vh;
           object-fit: contain;
         }
       }

       .controls {
         top: 0;
         left: 0;
         position: absolute;
         padding: 0 20px;
         width: -webkit-fill-available;
         height: 100%;
         pointer-events: none;
         display: flex;
         justify-content: space-between;
         > button {
           background: none;
           outline: none;
           border: none;
           i {
            cursor: pointer;
            pointer-events: all;
            transition: 0.4s color;
            color: grey;
            font-size: 80px;
            &:hover {
              color: white;
            }
           }
         }
         .download {
            cursor: pointer;
            pointer-events: all;
            position: absolute;
            right: 20px;
            transition: 0.4s color;
            bottom: 20px;
            font-size: 40px;
            color: grey;
            &:hover {
              color: white;
            }
         }
       }
     }
</style>