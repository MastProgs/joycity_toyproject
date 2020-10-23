<template>
    <v-app>
        <v-overlay :value="ShowOverlay">
            <v-card>
                <v-card-title>
                    Create New Configuration
                </v-card-title>
                <v-card-subtitle>
                    Please Define names for use.<br>Icon name is side of left bar label. Title name is top of bar label.
                </v-card-subtitle>
                <v-card-text>
                    <v-text-field v-model="Form_IconName" label="Icon Name" hide-details="auto" :rules="InputIconRules" hint="Name of Left icon"/>
                    <v-text-field v-model="Form_TitleName" label="Title Name" hide-details="auto" :rules="InputTitleRules" hint="Name of Top Title"/>
                </v-card-text>
                <v-card-actions>
                    <v-spacer/>
                    <v-btn @click="OnResponseTrue()" color="green" class="ma-2" v-show="CheckShowSaveForm">
                        Save
                    </v-btn>
                    <v-btn @click="OnResponseFalse()" color="red" class="ma-2">
                        Cancel
                    </v-btn>
                </v-card-actions>
            </v-card>

        </v-overlay>
    </v-app>
</template>

<script>
export default {
    name: "AddNewPage",

    props: [
        'ShowOverlay',
    ],

    data: () => ({
        ResponseForm:{
            IsSuccess: Boolean,
        },

        InputIconRules:[
            value => !!value || 'Required.',
            value => (value && value.length <= 5) || 'Max 5 characters',
        ],
        InputTitleRules:[
            value => !!value || 'Required.',
        ],

        Form_IconName: "",
        Form_TitleName: "",
        CanShowSaveForm: false,
    }),

    computed: {
        CheckShowSaveForm: function () {
            return this.Form_IconName !== "" && this.Form_TitleName !== ""
        }
    },

    methods: {
        ClearInputs: function () {
            this.Form_IconName = this.Form_TitleName = ""
        },

        OnResponseTrue: function () {
            this.ResponseForm.IsSuccess = true
            this.ResponseForm.IconName = this.Form_IconName
            this.ResponseForm.TitleName = this.Form_TitleName

            this.$emit('ResponseForm', this.ResponseForm)
            this.ClearInputs()
        },

        OnResponseFalse: function () {
            this.ResponseForm.IsSuccess = false
            this.$emit('ResponseForm', this.ResponseForm)
            this.ClearInputs()
        },
    },
}
</script>