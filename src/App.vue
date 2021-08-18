<template>
<div>
<navbar></navbar>
<converter></converter>
</div>
</template>

<script>
import Converter from './components/Converter.vue'
import Navbar from './components/Navbar.vue'

export default {

  components: {
    'converter': Converter,
    'navbar': Navbar
  }

}
</script>

<style>

</style>
