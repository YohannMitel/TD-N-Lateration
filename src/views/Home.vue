<template>
  <div id="container" class="container-fluid">
    <div class="d-flex flex-column h-100">
      <!-- Title of the page -->
      <div class="d-flex flex-column">
        <div class="text-center my-4">
          <h1>TD - N Lateration</h1>
        </div>

        <div class="d-flex flex-row flex-wrap justify-content-between ">


          <div class="m-4 d-flex flex-row gap-2">
            <button @click="resetCam()" class="btn btn-primary">Reset cam</button>
            <button @click="computePosition()" class="btn btn-primary">Compute pos</button>
            {{ computedPosition ? `Position computed !` : '' }}
          </div>
          <!-- Checkboxes for controlling display 
          <div class="d-flex  flex-row  my-4">


            <div class="form-check me-4 px-2">
              <input class="form-check-input" type="checkbox" id="opacityCheck" v-model="opacityEnabled">
              <label class="form-check-label" for="opacityCheck">
                Enable opacity
              </label>
            </div>

            <div class="form-check me-4 px-2">
              <input class="form-check-input" type="checkbox" id="binCheck" v-model="displayBin">
              <label class="form-check-label" for="binCheck">
                Display bin
              </label>
            </div>

            <div class="form-check px-2">
              <input class="form-check-input" type="checkbox" id="controllersCheck" v-model="displayControllers">
              <label class="form-check-label" for="controllersCheck">
                Display controllers
              </label>
            </div>
          </div>-->
        </div>
      </div>


      <!-- Three.js container, now takes the remaining height -->
      <div class="flex-grow-1 mb-4 px-3  position-relative">

        <ThreeContainer ref="threeContainerComp" :opacity="opacityEnabled" :bin="displayBin"
          :controllers="displayControllers" :openState="openState" />
      </div>



    </div>
  </div>
</template>

<script setup>
import { onMounted, watch, ref, onUnmounted,computed } from 'vue';
import ThreeContainer from '@/components/ThreeContainer.vue';


// Variables for controlling visibility and opacity
const opacityEnabled = ref(true);
const displayBin = ref(true);
const displayControllers = ref(true);
const openState = ref(false);
const computedPosition = ref(null);

const threeContainerComp = ref(null)

const resetCam = () => {
  if(threeContainerComp.value) threeContainerComp.value.resetCamera()
} 
  
const computePosition = () => {
  if(threeContainerComp.value) computedPosition.value = threeContainerComp.value.computePosition()
  console.log(threeContainerComp.value.computePosition())
} 

</script>

<style scoped>
.container-fluid {
  height: 100vh;
  background-color: #f4f4f4;
}

.d-flex {
  display: flex;
}

.h-100 {
  height: 100%;
}

.flex-grow-1 {
  flex-grow: 1;
}

h1 {
  font-size: 3rem;
}

.form-check {
  display: inline-block;
  margin-right: 20px;
}
</style>