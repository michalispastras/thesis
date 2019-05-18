<template>
    <div class="container">
        <div class="row justify-content-center mt-5">
            <div class="col">
                <a href="/big-data">
                    <button class="btn btn-primary">BIG DATA</button>
                </a>
            </div>
            <div class="col">
                <a href="/data-search">
                    <button class="btn btn-primary">DSS</button>
                </a>
            </div>
            <div class="col">
                <button class="btn btn-primary">TOOL 1</button>
            </div>
            <div class="col">
                <button class="btn btn-primary">TOOL 2</button>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                routeName: "{{route('big-data')}}"
            }
        },
        mounted() {
            console.log('Component mounted.')
        }
    }
</script>

<style>
    .btn {
        height: 60px;
        width: 200px;
    }
</style>
