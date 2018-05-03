<template>
    <div class="wrapper">
        <v-head></v-head>
        <div>
            <router-view></router-view>
        </div>
    </div>
</template>

<script>
    import vHead from '../common/Header.vue';
    export default {
        components:{
            vHead
        }
    }
</script>
