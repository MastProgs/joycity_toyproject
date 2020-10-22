<template>
    <v-app>
        <v-select />
    </v-app>
</template>

<script>
export default {
    name:'ViewPage',

    props:[
        'select_national',
        'input_id',
        'input_pw',
        'input_ip',
        'input_port',
        'file_path',
    ],

    data: () => {

    },

    methods: {
        onClick() {

        }
    },
};
</script>