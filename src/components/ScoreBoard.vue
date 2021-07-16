<template>
   <section>
      <p><strong>Your Score:</strong>{{scoreHuman}} | <strong>Computer Score: </strong>{{scoreComputer}}</p>
    </section>
</template>

<style scoped>

</style>


<script>
export default {
  props: ['scoreHuman','scoreComputer']
}
</script>