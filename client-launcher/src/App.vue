<template>
  <v-app>
    <v-navigation-drawer
      app
      class="pt-4"
      color="grey lighten-3"
      mini-variant
      permanent
    >
      <v-btn class="d-block mx-auto mb-4" small dark circle @click="OnAddNewPage()">
        <v-icon>mdi-plus</v-icon>
      </v-btn>
      <v-divider/>

      <div>
        <v-btn 
          v-for="(item, i) in PageConfig" 
          :key="i" 
          :color="`${i === PageIndex ? 'black' : 'grey'}`" 
          class="d-block mx-auto my-2" 
          icon 
          @click="ListButtonClicked(i)"
        >
          {{ item.IconTitle }}
        </v-btn>
      </div>

      <v-spacer/>
      <v-divider/>
      <v-btn icon class="d-block mx-auto my-2" @click="OnSettingButtonClicked()">
        <v-icon>mdi-cog-outline</v-icon>
      </v-btn>
    </v-navigation-drawer>

    <v-app-bar app dense flat dark v-if="IsSettingPage === false">
      <h1>{{ PageConfig[PageIndex].HeadTitle }}</h1>
      <v-spacer/>
      <div v-if="PageIndex !== 0">
        <v-btn class="mr-2" color="green" x-small> <!-- 버튼 클릭 시, 보는 페이지 JSON 으로 설정 저장 필요, Ctrl + s 도 연동 -->
          Save
        </v-btn>
        <v-btn color="red" x-small @click="ListErase(PageIndex)">
          Del
        </v-btn>
      </div>
    </v-app-bar>
    <!-- Settings appbar -->
    <v-app-bar v-else app dense flat dark>
      <h1>Settings</h1>
    </v-app-bar>

    <v-main v-if="IsSettingPage === false">
      <div v-if="PageIndex !== 0" class="ma-5">
        <v-select v-model="PageConfig[PageIndex].CountryType" label="Select Country" :items="CountryList" outlined dense @change="OnCountrySelected"></v-select>

        <!-- CHN -->
        <div v-if="PageConfig[PageIndex].CountryType === 'CHN'" class="mx-5">
          <v-row>
            <v-text-field v-model="PageConfig[PageIndex].Ip" label="IP" class="mx-2" @change="RefreshArgements"></v-text-field>
            <v-text-field v-model="PageConfig[PageIndex].Port" label="Port" class="mx-2" @change="RefreshArgements"></v-text-field>
          </v-row>
          <v-file-input @change="OnFileSelected" label="input FreeStyle_d.exe"></v-file-input>
          <v-text-field v-model="FinalArguments" label="Command Argument" class="mx-2" readonly></v-text-field>
        </div>
        <!-- KOR, POD -->
        <div v-else-if="PageConfig[PageIndex].CountryType !== 'Custom'" class="mx-5">
          <v-row>
            <v-text-field v-model="PageConfig[PageIndex].Id" label="ID" class="mx-2" @change="RefreshArgements"></v-text-field>
            <v-text-field v-model="PageConfig[PageIndex].Pw" label="PassWord" class="mx-2" @change="RefreshArgements"></v-text-field>
          </v-row>
          <v-row>
            <v-text-field v-model="PageConfig[PageIndex].Ip" label="IP" class="mx-2" @change="RefreshArgements"></v-text-field>
            <v-text-field v-model="PageConfig[PageIndex].Port" label="Port" class="mx-2" @change="RefreshArgements"></v-text-field>
          </v-row>
          <v-file-input @change="OnFileSelected" label="input FreeStyle_d.exe"></v-file-input>
          <v-text-field v-model="FinalArguments" label="Command Argument" class="mx-2" readonly></v-text-field>
        </div>
        <!-- Custom -->
        <div v-else class="mx-5">
          <v-file-input @change="OnFileSelected" label="input *.exe"></v-file-input>
          <v-text-field v-model="FinalArguments" label="Command Argument" class="mx-2"></v-text-field>
        </div>
        <v-btn block color="green" @click="DoLaunch">Launch</v-btn>

      </div>
      <div v-else class="ma-5">
        <h1>Hello World</h1>
      </div>

      <AddNewPage :ShowOverlay="CanShowOverAddForm" @ResponseForm="OnResposeAddForm"/>      
    </v-main>
    <!-- Settings main -->
    <v-main v-else>
      <v-card v-for="(setting, i) in DefaultSetting" :key="i" class="ma-4">
        <v-row class="mx-1">
          <v-card-title>{{ setting.CountryType }}</v-card-title>
          <v-spacer/>
          <v-btn color="blue" x-small class="mr-2 mt-4" icon disabled><v-icon small @click="OnModifySetting(i)">mdi-lead-pencil</v-icon></v-btn>
          <v-btn color="red" x-small class="mr-2 mt-4" icon disabled><v-icon small @click="OnDeleteSetting(i)">mdi-delete</v-icon></v-btn>
        </v-row>
        <v-card-text>
          IP : {{ setting.Ip }} <br>
          Port : {{ setting.Port }} <br>
          Id : {{ setting.Id }} <br>
          PassWord : {{ setting.Pw }}
        </v-card-text>
      </v-card>

      <SettingPopup :ShowOverlay="CanShowOverSettingForm"
      :InputTypeValue="Setting_Input_Type"
      :InputIpValue="Setting_Input_Ip"
      :InputPortValue="Setting_Input_Port"
      :InputIdValue="Setting_Input_Id"
      :InputPwValue="Setting_Input_Pw"
      @ResponseForm="OnResponseSetting"
      />
    </v-main>
  </v-app>
</template>

<script>
import AddNewPage from "./components/AddNewPage";
import SettingPopup from "./components/SettingPopup";

function Launch(fpath, arg) {
  require('child_process').execFile(fpath, arg, function (err, data) {
    console.log(err)
    console.log(data.toString())
  })
}

export default {
  name: 'App',

  components: {
    AddNewPage,
    SettingPopup,
  },

  data: () => ({
    DefaultSetting: [
      { CountryType:"KOR", Ip:"192.168.50.166", Port:"10010", Id:"test001", Pw:"1234" },
      { CountryType:"CHN", Ip:"192.168.50.201", Port:"10010", Id:"test1", Pw:"1111" },
      { CountryType:"POD", Ip:"192.168.50.171", Port:"10010", Id:"test1", Pw:"1111" },
    ],

    PageIndex: 0,

    PageConfig:[
      { IconTitle:"Main", HeadTitle:"Client Launcher", CountryType:"", Ip:"", Port:"", Id:"", Pw:"", Filepath:"" },
      { IconTitle:"KOR", HeadTitle:"KOR Ver.", CountryType:"KOR", Ip:"192.168.50.166", Port:"10010", Id:"test001", Pw:"1234", Filepath:"D://" },
      { IconTitle:"CHN", HeadTitle:"CHN Ver.", CountryType:"CHN", Ip:"192.168.50.201", Port:"10010", Id:"test1", Pw:"1111", Filepath:"D://" },
      { IconTitle:"POD", HeadTitle:"POD Ver.", CountryType:"POD", Ip:"192.168.50.171", Port:"10010", Id:"test1", Pw:"1111", Filepath:"D://" },
      { IconTitle:"Custom", HeadTitle:"Custom Ver.", CountryType:"Custom", Ip:"", Port:"", Id:"", Pw:"", Filepath:"D://" },
    ],

    CountryList:[
      "KOR",
      "CHN",
      "POD",
      "Custom",
    ],

    CanShowOverAddForm: false,

    FinalArguments: "",

    // Setting various
    IsSettingPage: false,
    CanShowOverSettingForm: false,

    Setting_Input_Type: "",
    Setting_Input_Ip: "",
    Setting_Input_Port: "",
    Setting_Input_Id: "",
    Setting_Input_Pw: "",
  }),

  computed: () => ({
  }),

  methods: {
    ListButtonClicked: function (params) {
      this.IsSettingPage = false
      this.PageIndex = params
      this.RefreshArgements()
    },

    OnAddNewPage: function () {
      this.IsSettingPage = false
      this.CanShowOverAddForm = true
    },

    OnResposeAddForm: function (params) {
      this.CanShowOverAddForm = false
      if (params.IsSuccess == false) { return }
      
      this.ListPushBack(params.IconName, params.TitleName)
    },

    ListPushBack: function (iconName, titleName) {
      this.PageConfig.push({ IconTitle: iconName, HeadTitle: titleName, CountryType:"", Ip:"", Port:"", Id:"", Pw:"", Filepath:""})
      this.ListButtonClicked(this.PageConfig.length - 1)
    },
    ListErase: function (index) {
      this.IsSettingPage = false
      this.PageIndex = 0
      this.PageConfig.splice(index, 1)
    },

    // Setting Page
    AddSettingList: function () {
      this.DefaultSetting.push({ CountryType: this.Setting_Input_Type, Ip: this.Setting_Input_Ip, Port: this.Setting_Input_Port, Id: this.Setting_Input_Id, Pw: this.Setting_Input_Pw})
    },
    OnSettingButtonClicked: function () {
      this.IsSettingPage = true
    },
    OnDeleteSetting: function (index) {
      let cnt = 0, type = 0
      this.CountryList.forEach(element => {
        if (element == this.DefaultSetting[index].CountryType) {
          type = cnt          
        }
        ++cnt
      })

      this.CountryList.splice(type, 1)
      this.DefaultSetting.splice(index, 1)
    },
    OnModifySetting: function (index) {   
      this.Setting_Input_Type = this.DefaultSetting[index].CountryType
      this.Setting_Input_Ip = this.DefaultSetting[index].Ip
      this.Setting_Input_Port = this.DefaultSetting[index].Port
      this.Setting_Input_Id = this.DefaultSetting[index].Id
      this.Setting_Input_Pw = this.DefaultSetting[index].Pw

      this.CanShowOverSettingForm = true
    },
    OnResponseSetting: function (response) {
      this.CanShowOverSettingForm = false
      if (response.IsSuccess == false) { return }

      this.CountryList.forEach(element => {
        if (element === response.Type) {
          this.DefaultSetting.forEach(li => {
            if (li.CountryType === response.Type) {
              li.Ip = response.Ip
              li.Port = response.Port
              li.Id = response.Id
              li.Pw = response.Pw
              return
            }
          })
          return
        }
      })
      
    },

    // Main Page
    RefreshArgements: function () {
      switch (this.PageConfig[this.PageIndex].CountryType) {
        case "KOR":        
          this.FinalArguments = this.PageConfig[this.PageIndex].Id.split('test').join('') + "&" + this.PageConfig[this.PageIndex].Id + "&" + this.PageConfig[this.PageIndex].Pw + "&" + this.PageConfig[this.PageIndex].Ip + "&" + this.PageConfig[this.PageIndex].Port + "&1"  
          break;
        case "CHN":          
          this.FinalArguments = this.PageConfig[this.PageIndex].Ip + ":" + this.PageConfig[this.PageIndex].Port
          break;
        case "POD":          
          this.FinalArguments = "[TOKEN]0[/TOKEN][SERVER]" + this.PageConfig[this.PageIndex].Ip + ":" + this.PageConfig[this.PageIndex].Port + "[/SERVER][LANGUAGE]0[/LANGUAGE][USERINDEX]" + this.PageConfig[this.PageIndex].Id.split('test').join('') + "[/USERINDEX][USERID]" + this.PageConfig[this.PageIndex].Id + "[/USERID]"
          break;      
        default:
          this.FinalArguments = ""
          break;
      }
    },

    OnCountrySelected: function (country) {
      this.PageConfig[this.PageIndex].CountryType = country

      this.DefaultSetting.forEach(element => {
        if (country == element.CountryType) {
          this.PageConfig[this.PageIndex].Ip = element.Ip
          this.PageConfig[this.PageIndex].Port = element.Port
          this.PageConfig[this.PageIndex].Id = element.Id
          this.PageConfig[this.PageIndex].Pw = element.Pw
        }
      });
      
      this.RefreshArgements()
    },

    OnFileSelected: function (file) {
      this.PageConfig[this.PageIndex].Filepath = file.path
    },


    // Launch
    DoLaunch: function () {
      Launch(this.PageConfig[this.PageIndex].Filepath, this.FinalArguments)
    }
  },
};


</script>
