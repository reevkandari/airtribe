<template>
  <q-layout view="lHh Lpr lFf">
    <q-toolbar class="bg-indigo text-white">
      <div class="full-width row">
        <div class="col-2" />
        <q-btn  dense no-ripple class="col-2" flat @click="resetEverything" label="Reset" />
        <div class="col-2" />
        <q-toolbar-title class="col-5" right>
          Rishabh Kandari Airtribe Assignment
        </q-toolbar-title>

      </div>

    </q-toolbar>

    <q-page-container>
      <router-view class="q-pt-xl" />
    </q-page-container>
  </q-layout>
</template>

<script>


import { defineComponent, ref } from 'vue'

export default defineComponent({
  name: 'MainLayout',
  methods:{
    resetEverything(){
      //for multiple tests and to start fresh, this function resets the localstorage to first load
      this.$q.localStorage.clear();
      location.reload();
    }
  }
})
</script>
