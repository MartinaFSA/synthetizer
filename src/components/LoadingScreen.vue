<template>
  <section v-show="isLoading" id="loadingScreen">
    <div>
        <h1 data-text="Synthetizer">Synthetizer</h1>
    </div>
    <div>
        <h2 data-text="Beats">Beats</h2>
    </div>
  </section>
</template>

<script>
    export default {
        data() {
            return {
                isLoading: true
            }
        },

        created() {
            setTimeout(() => this.isLoading = false, 5500)
        }
    }
</script>

<style scoped>
    @import url('https://fonts.googleapis.com/css2?family=Birthstone&display=swap');
    @import url('../assets/loadingScreen.css');
</style>