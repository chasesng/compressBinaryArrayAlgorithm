
<template style="width:95vw">

  <br />

  <div class="fade-on-scroll cntr br10 ibn p6 ib" :style="{ opacity: 1 - (scrollPosition / fadeThreshold) }"
    style="width:95vw;margin-bottom:10vh;margin-top:5%;overflow: hidden;background: linear-gradient(0deg, rgba(0,0,0,1) 18%, rgba(181,181,181,1) 89%);">

    <p class="wt ft cntr"
      style="font-size:40px;height:fit-content;margin-top:15%;margin-bottom:2%;color:white;opacity:.5">img
      Compression</p>

    <p class="wt ft cntr" style="opacity:.6;font-style:italic;font-size:22px">This algorithm does not provide better
      results than converting to flif/jpeg, it is merely experimental & acts as a proof of concept.</p>


    <p class="wt p6" style="opacity:.5;margin-top:5vh"> - Chase</p>
    <a href="https://github.com/chasesng">My github</a>
    <p class="wt p8 mt10">This is part of my 'app a day for a week' personal project. Contact me for features you want
      added to this site if any.</p>
  </div>

  <div>
    <p class="wtbg ibn p8 cntr ib" style="margin-bottom:1%;width:40vw;padding:1%">Images can be converted to a binary Array with its objects representing the 16bit RGB values (Allowing for more than 65,000 colors to be displayed) of the pixels and more. By creating an algorithm that
      be run to return the same string with less text, we can effectively shorten it, and therefore downsize
      the image.
    </p>
  </div>











  <div>
    <p class="wt ibn p5">Upload Image</p>
    <input type="file" ref="fileInput" class="wt" @change="imageToBinaryArray" style="margin-left:5.5%;">
    <div v-if="binaryArrayString">
      <p class="wt ibn p5" style="margin-top:2%;">Image in Binary Array (Truncated)</p>
      <div class="wtbg cntr" style="flex-wrap: wrap;width:50vw;height:5vh;overflow:scroll;font-size:1em;;color:red">{{
        String(binaryArrayString).substring(0, 300) }}</div>
      <p class='wt ibn p8' style="margin-top:2%;">Actual Data (Non-Truncated)</p>
      <div class="wt cntr" style="flex-wrap: wrap;width:40vw;overflow-x:scroll;font-size:.8em;">{{
        String(binaryArrayString).split(',').length }} Objects in Array | Size: {{ getBytes(binaryArrayString) }}</div>
      <button class="brButton" style="font-size:1em;"
        @click="compressBinaryArray(binaryArrayString)">Generate
        Compression Algorithm</button>
      <p class="wt ibn p5" style="margin-top:2%;">Algorithm Compressed Array (Full Length)</p>
      <div v-if="algorithmContainer" class="wtbg cntr"
        style="flex-wrap:wrap;width:50vw;height:5vh;overflow:scroll;font-size:1em;color:red">{{ String(algorithmContainer) }}
      </div>
      <p class='wt ibn p8' style="margin-top:2%;">Algorithm Compressed Data</p>
      <div v-if="algorithmContainer" class="wt cntr" style="width:40vw;overflow:scroll;font-size:.8em;">{{
        String(algorithmContainer).split(',').length }} Objects in Array | {{
    Math.round(String(algorithmContainer).split(',').length / binaryArrayString.length * 100) }}% the length of the
        original | Size: {{ getBytes(algorithmContainer) }}</div>

      <p class='wt ibn p8' style="opacity:.5">What does the algorithm do?<br />The image is shortened using a simple
        run-length encoding algorithm to compress the binary data in the input through expression.<br />
        Iterating through the original binary Array, it counts the number of consecutive elements with the same value and
        stores it in a compressed array.
      </p>
      <div v-if="algorithmContainer">
        <p class="wt ibn p5" style="margin-top:2%;">Algorithm Unpacked Array (Full Length)</p>
      <div class="wtbg cntr" style="flex-wrap: wrap;width:50vw;height:5vh;overflow:scroll;font-size:1em;;color:red">{{
        unPackCompressedArray(algorithmContainer) }}</div>
      <p class='wt ibn p8' style="margin-top:2%;">Unpacked Data</p>
      <div class="wt cntr" style="flex-wrap: wrap;width:40vw;overflow-x:scroll;font-size:.8em;">{{
        String(unPackCompressedArray(algorithmContainer)).split(',').length }} Objects in Array | Size: {{ getBytes(unPackCompressedArray(algorithmContainer)) }}</div>
    </div>
    
    </div>
    <!-- base64String is so long is freezes the site when trying to find repeating patterns of the longest substring within -->
    <!-- try binary array -->
    <!-- unPackCompressedArray -->
  </div>
  <div class="ib" style="margin-top:2%">
    <p class="wt ibn p6" style="opacity:.8">Compress & Unpack Code</p>
      <div class="f">
        <img src="../assets/compressAlgo/compressBinaryArray.png">
        <img src="../assets/compressAlgo/unPackCompressedArray.png">
      </div>
      <div class="wtbg cntr ib" style="opacity:.8;margin-top:5%;width:40vw;height:30vh">
      <p class="ibn p5" style="margin-top:1%">What is the purpose of this algorithm?</p>
      <p class="ibn p8" style="margin-top:1%">By expressing images are a Binary Array, they can be stored in a string format.<br/>However, these binary arrays are often extremely long. As such, having it be compressed to be unpacked on load would greatly speed up user experience.</p>
        <p class="ibn p8" style="margin-top:1%">The compression algorithm is most beneficial in cases where a few colors dominate the image.</p>

      </div>
    </div>
</template>



<script>
import { ref } from 'vue';


export default {


  data() {


    return {
      imageSrc: null,
      convertedSrc: null,
      algorithmContainer: ref(''),
      base64String: '',
      binaryArrayString: ref(''),
      scrollPosition: -300,
      fadeThreshold: 700,
      places: ref([]),

      currentDate: new Date().toLocaleDateString('en-US', {
        weekday: 'long',
        month: 'long',
        day: 'numeric',
        year: 'numeric'
      })
    }
  }, methods: {

    redirectToAdd() {
      this.$router.push({ path: '/Add_Recommendation' })
    },
    handleScroll() {
      this.scrollPosition = window.scrollY;
      const div = document.querySelector('.fade-on-scroll');
      if (div) {
        if (this.scrollPosition > this.fadeThreshold) {
          div.classList.add('fade-out');
        } else {
          div.classList.remove('fade-out');
        }

      }
    },
    compressBase64(base64String) {
      // Decode the base64 string into a byte array
      const byteCharacters = atob(base64String);
      const byteNumbers = new Array(byteCharacters.length);
      for (let i = 0; i < byteCharacters.length; i++) {
        byteNumbers[i] = byteCharacters.charCodeAt(i);
      }
      const byteArray = new Uint8Array(byteNumbers);
      const compressedArray = [];
      let currentByte = byteArray[0];
      let count = 1;
      for (let i = 1; i < byteArray.length; i++) {
        if (byteArray[i] === currentByte) {
          count++;
        } else {
          compressedArray.push(currentByte);
          compressedArray.push(count);
          currentByte = byteArray[i];
          count = 1;
        }
      }
      compressedArray.push(currentByte);
      compressedArray.push(count);

      this.algorithmContainer = new Uint8Array(compressedArray)
      return new Uint8Array(compressedArray);
    },


    compressBinaryArray(binaryArray) {
      const compressedArray = [];
      let count = 0;
      let byte = 0;
      for (let i = 0; i < binaryArray.length; i++) {
        if (binaryArray[i] === byte && count < 255) {
          count++;
        } else {
          compressedArray.push(byte, count);
          count = 1;
          byte = binaryArray[i];
        }
      }
      compressedArray.push(byte, count);

      this.algorithmContainer = compressedArray
      return compressedArray;
    },

    convertToBase64() {
      const fileInput = this.$refs.fileInput;
      const file = fileInput.files[0];
      const reader = new FileReader();
      reader.readAsDataURL(file);
      reader.onload = () => {
        this.base64String = reader.result.split(',')[1];
        console.log(this.base64String)
      };
      reader.onerror = (error) => {
        console.log('Error: ', error);
      };
    },
    imageToBinaryArray() {
      return new Promise((resolve, reject) => {
        const fileInput = this.$refs.fileInput;
        const file = fileInput.files[0];
        const reader = new FileReader();
        reader.readAsDataURL(file);
        reader.onload = () => {
          const img = new Image();

          img.onload = () => {
            const canvas = document.createElement('canvas');
            canvas.width = img.width;
            canvas.height = img.height;
            const context = canvas.getContext('2d');
            context.drawImage(img, 0, 0);
            const imageData = context.getImageData(0, 0, canvas.width, canvas.height).data;
            const binaryArray = new Uint16Array(imageData.length);
            for (let i = 0; i < imageData.length; i++) {
              binaryArray[i] = imageData[i];
            }
            this.binaryArrayString = binaryArray
            console.log(this.binaryArrayString)
            resolve(binaryArray);
          };
          img.onerror = reject;
          img.src = reader.result;
        };
        reader.onerror = reject;
        reader.readAsDataURL(file);
      });
    },

    getBytes(arr) { //return size of array in mb, 1000^2
      var a = new Blob([JSON.stringify(arr)]).size;
      return a / 1000000 + "mb"
    },

    unPackCompressedArray(compressedArray) {
      const binaryArray = [];
      for (let i = 0; i < compressedArray.length; i += 2) {
        const byte = compressedArray[i];
        const count = compressedArray[i + 1];
        for (let j = 0; j < count; j++) {
          binaryArray.push(byte);
        }
      }

      return new Uint16Array(binaryArray);
    }






  },
  mounted() {
    window.addEventListener('scroll', this.handleScroll);

  },
  beforeUnmount() {
    window.removeEventListener('scroll', this.handleScroll)
  },
  watch: {
  
  }

}
</script>


<style>
#app {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.eaPlace:active {
  background-color: gray;
  transition: background-color .2s
}

.redirectToAddButton:active {
  background-color: gray;
  transition: background-color .2s
}


.selected {
  background-color: gray;
}


.fade-on-scroll {
  opacity: 1;
  transition: opacity 0.3s ease-in-out;
}

.fade-on-scroll.fade-out {
  opacity: 0;
}

#displayTitle {
  text-align: center;
  color: grey;
  margin-left: 45px;
}


.box {
  border: 1px solid #ccc;
}


.crsImg {
  width: 550px;
  height: 350px;
  margin: auto;
  margin-left: 130px;
}

.txt {
  float: right;
  width: 50%;
  padding-left: 20px;
}


.rdrButton:hover {
  background-color: gray;
}

#viewPlanbtn {
  height: 50px;
  width: 100%;
  font-size: 20px;
  text-align: center;
  /* border: 1px solid grey; */
  cursor: pointer;

  border-radius: 0px;
}


#banner {
  height: 100%;
  font-size: 26px;
  text-align: center;
  vertical-align: middle;
  line-height: 50px;
  border-right: 1px solid gray;
  flex: 1;
  display: inline-block;
  background-color: whitesmoke;

}

.previewPlans {
  -webkit-overflow-scrolling: touch;
}

.containBanners {
  height: 210px;
  display: flex;
  border: 1px solid gray;
  margin: auto;
  overflow: hidden;
  box-shadow: 10px 10px 5px 0px rgba(0, 0, 0, 0.75);
  -webkit-box-shadow: 10px 10px 5px 0px rgba(0, 0, 0, 0.75);
  -moz-box-shadow: 10px 10px 5px 0px rgba(0, 0, 0, 0.75);
}

.hovertoolTip {
  color: darkgray;
  font-size: 15px;
}

.rationaleImage {
  width: 330px;
  height: 550px;
  border-radius: 20px;
  border: 1px solid grey;
  overflow: hidden;
  box-shadow: 10px 10px 5px 0px rgba(0, 0, 0, 0.75);
  -webkit-box-shadow: 10px 10px 5px 0px rgba(0, 0, 0, 0.75);
  -moz-box-shadow: 10px 10px 5px 0px rgba(0, 0, 0, 0.75);
}

.rationaleText {
  float: left;
  margin: auto;
  width: 500px;
  margin-left: 80px;
  padding-top: 5px;
}

.icon_container {
  position: relative;
  display: inline-block;
  width: 100px;
  height: 100px;
  line-height: 50px;
  text-align: center;
}


.icon {
  position: relative;
  font-size: 30px;
  color: #fff;
  vertical-align: middle;
  margin-top: 40px;
}

.box:hover {
  background: lightgray
}

.customizationImg {
  width: auto;
  height: 200px;
  border-radius: 20px;
  border: 1px solid grey;
  overflow: hidden;
}

.holdCustomizationText {
  float: left;
  margin: auto;
  width: 50%;
  margin-left: 5%;
  padding-top: 5px;
}

.loader {
  --height-of-loader: 4px;
  --loader-color: #0071e2;
  width: 60%;
  height: var(--height-of-loader);
  border-radius: 30px;
  background-color: rgba(0, 0, 0, 0.2);
  position: relative;
}

.loader::before {
  content: "";
  position: absolute;
  background: var(--loader-color);
  top: 0;
  left: 0;
  width: 0%;
  height: 100%;
  border-radius: 30px;
  animation: moving 1s ease-in-out infinite;
  ;
}

@keyframes moving {
  50% {
    width: 100%;
  }

  100% {
    width: 0;
    right: 0;
    left: unset;
  }
}</style>
