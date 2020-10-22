<template>
    <form>
        <fieldset v-for="(stop, index) in stops" :key="index">
            <stop v-bind="whatever"/>
        </fieldset>
        <button @click="onClick">Add one</button>
    </form>
</template>

<script>
export default {
    data() {
        return {
            stops: []
        };
    },
    methods: {
        onClick() {
            this.stops.push(/* whatever */)
        }
    },
    components: {
        Stop
    }
};
</script>