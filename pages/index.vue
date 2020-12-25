<template>
  <div class="container">
    <div v-if="!isAmbulanceCalled" class="form">
      <h2 class="form-title">
        Вам стало плохо?
      </h2>
      <p class="form-text">
        В случае, если вы почувствовали себя нехорошо, немедленно вызовите скорую!
      </p>
      <button class="form-btn" @click="callAmbulance">
        Вызвать скорую
      </button>
    </div>
    <div v-else class="form">
      <div class="ambulance-light">
        <div class="red" />
        <div class="blue" />
      </div>
      <h2 class="form-title">
        Скорая уже едет
      </h2>
      <p v-for="(value, key) in ambulanceData" :key="key" class="form-text">
        {{ value }}
      </p>
      <button class="form-btn" @click="reloadData">
        Обновить данные
      </button>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'nuxt-property-decorator'

@Component
export default class extends Vue {
  isAmbulanceCalled: boolean = false
  ambulanceData: any = {}

  callAmbulance () {
    this.$axios.$post('/emergency').then((response: any) => {
      this.isAmbulanceCalled = response
      this.reloadData()
    })
  }

  reloadData () {
    this.$axios.$get('/emergency/info').then((response: any) => {
      this.ambulanceData = response.piData

      if (this.ambulanceData.free) {
        this.isAmbulanceCalled = false
      }
    })
  }
}
</script>

<style lang="scss">
.container {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #f0f0f0;
}

.form {
  width: 100%;
  max-width: 600px;
  background-color: white;
  padding: 16px;
  border-radius: 8px;
  box-shadow: 0 0 3px 0 #969696;
  position: relative;

  &-title {
    margin: 0;
  }

  &-btn {
    width: 100%; height: 60px;
    color: #721c24;
    background-color: #f8d7da;
    border: 2px solid #f5c6cb;
    outline: none;
    font-size: 24px;
    //font-weight: bold;
    border-radius: 8px;
    box-shadow: inset 0 0 3px 0 #f5c6cb;
    cursor: pointer;

    &:hover {
      opacity: .9;
    }

    &:active {
      transform: scale(.99);
    }
  }
}

@keyframes opacity {
  0% {
    opacity: 1;
  }
  50% {
    opacity: .3;
  }
}

.ambulance-light {
  position: absolute;
  height: 100px; width: 100%;
  top: -120px; left: 0;
  display: flex;

  .red, .blue {
    flex-basis: 50%;
  }

  .red {
    background-color: red;
    animation: opacity linear .5s infinite;
    border-radius: 16px 0 0 16px;
    box-shadow: 0 0 10px 0 red;
  }

  .blue {
    background-color: blue;
    animation: opacity linear .5s .25s infinite;
    border-radius: 0 16px 16px 0;
    box-shadow: 0 0 10px 0 blue;
  }

  &::before {
    content: '';
    position: absolute;
    height: 40px; width: calc(100% - 40px);
    top: 10px; left: 20px;
    background-color: white;
    opacity: .3;
    border-radius: 8px;
    z-index: 1;
  }

  &::after {
    content: '';
    position: absolute;
    height: 100%; width: 100%;
    top: 0; left: 0;
    border-radius: 16px;
    box-shadow: inset 0 0 8px 4px white;
  }
}
</style>
