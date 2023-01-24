<template>
  <BannerFundo />
  <div class="main-container">
      <h1>Burgueria do vue: </h1>
      <BurguerForm />
  </div>
</template>

<script>
import BannerFundo from '@/components/BannerFundo.vue';
import BurguerForm from '@/components/BurguerForm.vue';

export default {
  name: 'HomePage',
  components: { BannerFundo, BurguerForm }
}


</script>
