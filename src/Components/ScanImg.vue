<template>
    <div v-if="loading">
        <img src="200.gif"/>
    </div>

    <div v-if="error == true" class="error">
        <p>Le chargement de la page est impossible ou vous avez atteint la fin du scan !</p>
        <img src="error.webp"/>
    </div>
    <span v-else ref="scanImg"></span>
</template>


<script>
export default {
    name: "scan-img",
    props: {
        src: String
    },
    data() {
        return {
            loading: true,
            error: false
        }
    },
    methods: {

    },
    mounted() {
        let img = new Image()
        img.src = this.src
        let _this = this
        img.onload = function () {
            _this.loading = false
            _this.$refs.scanImg.appendChild(img)
        }
        img.onerror = function () {
            _this.loading = false
            _this.error = true
        }
    }
}
</script>

<style lang="scss" scoped>
    .error {
        display: flex;
        flex-direction: column;
        align-items: center;
        p {
            color: red;
            font-size: 24px;
            font-family: Arial, Helvetica, sans-serif;
        }
        > img {
            height: auto !important;
            width: 100px !important;
        }
    }
</style>