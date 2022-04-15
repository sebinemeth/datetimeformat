<template>
  <div class="home">
    <DateTimeFormat msg="Welcome to Your Vue.js + TypeScript App"/>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import DateTimeFormat from '@/components/DateTimeFormat.vue'; // @ is an alias to /src

@Component({
  components: {
    DateTimeFormat,
  },
})
export default class Home extends Vue {}
</script>
