<template>
    <v-app>
        <v-overlay :value="ShowOverlay">
            <v-card>
                <v-card-title>
                    Setting Form
                </v-card-title>
                <v-card-subtitle>
                    If you set something properties, then you can use drop box option and easily use default setting.
                </v-card-subtitle>
                <v-card-text>
                    <v-text-field v-model="Form_TypeName" label="Contry Type Name" hide-details="auto" :rules="InputRules" hint="Name of Selection Type" :value="InputTypeValue"/>
                    <v-text-field v-model="Form_Ip" label="Server IP Address" hide-details="auto" :rules="InputRules" hint="When you run client that you Want to connect server ip address" :value="InputIpValue"/>
                    <v-text-field v-model="Form_Port" label="Server Port" hide-details="auto" :rules="InputRules" hint="When you run client that you Want to connect server port" :value="InputPortValue"/>
                    <v-text-field v-model="Form_Id" label="Login Id" hide-details="auto" :rules="InputRules" hint="When you connected server that you login to use id" :value="InputIdValue"/>
                    <v-text-field v-model="Form_Pw" label="Login Password" hide-details="auto" :rules="InputRules" hint="When you connected server that you login to use password" :value="InputPwValue"/>
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
    name:'SettingPopup',

    props:[
        'ShowOverlay',

        'InputTypeValue',
        'InputIpValue',
        'InputPortValue',
        'InputIdValue',
        'InputPwValue',
    ],

    data: () => ({
        ResponseForm:{
            IsSuccess: Boolean,
        },
        InputRules:[
            value => !!value || 'Required.',
        ],

        Form_TypeName: "",
        Form_Ip: "",
        Form_Port: "",
        Form_Id: "",
        Form_Pw: "",
    }),

    activated() {
        console.log(this.InputTypeValue)
        this.Form_TypeName = this.InputTypeValue
        this.Form_Ip = this.InputIpValue
        this.Form_Port = this.InputPortValue
        this.Form_Id = this.InputIdValue
        this.Form_Pw = this.InputPwValue
    },

    computed: {
        CheckShowSaveForm: function () {
            return this.Form_TypeName !== "" && this.Form_Ip !== "" && this.Form_Port !== "" && this.Form_Id !== "" && this.Form_Pw != ""
        }

        
    },

    methods: {
        ClearInputs: function () {
            this.Form_TypeName = this.Form_Ip = this.Form_Port = this.Form_Id = this.Form_Pw = ""
        },

        OnResponseTrue: function () {
            this.ResponseForm.IsSuccess = true
            this.ResponseForm.Type = this.Form_TypeName
            this.ResponseForm.Ip = this.Form_Ip
            this.ResponseForm.Port = this.Form_Port
            this.ResponseForm.Id = this.Form_Id
            this.ResponseForm.Pw = this.Form_Pw

            this.$emit('ResponseForm', this.ResponseForm)
            this.ClearInputs()
        },

        OnResponseFalse: function () {
            this.ResponseForm.IsSuccess = false
            this.$emit('ResponseForm', this.ResponseForm)
            this.ClearInputs()
        },
    },
};
</script>