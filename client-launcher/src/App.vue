<template>
  <v-app>
    <v-navigation-drawer
      app
      class="pt-4"
      color="grey lighten-3"
      mini-variant
      permanent
    >
      <v-btn class="d-block mx-auto mb-4" small dark circle @click="OnAddNewPage('hello', 'temp title')">
        <v-icon>mdi-plus</v-icon>
      </v-btn>
      <v-divider/>

      <div>
        <v-btn 
          v-for="(item, i) in PageConfig" 
          :key="i" 
          :color="`${i === PageIndex ? 'black' : 'grey'}`" 
          class="d-block mx-auto" 
          icon 
          @click="ListButtonClicked(i)"
        >
          {{ item.IconTitle }}
        </v-btn>
      </div>

    </v-navigation-drawer>

    <v-app-bar app dense flat dark>
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

    <v-main>
      <div v-if="PageIndex !== 0" class="ma-5">
        <v-select v-model="PageConfig[PageIndex].CountryType" label="Select Country" :items="CountryList" outlined dense @change="OnCountrySelected"></v-select>

        <!-- KOR, CHN, POD -->
        <div v-if="PageConfig[PageIndex].CountryType !== 'Custom'" class="mx-5">
          <v-row>
            <v-text-field v-model="PageConfig[PageIndex].Id" label="ID" class="mx-2" @change="RefreshArgements"></v-text-field>
            <v-text-field v-model="PageConfig[PageIndex].Pw" label="PassWord" class="mx-2" @change="RefreshArgements"></v-text-field>
          </v-row>
          <v-row>
            <v-text-field v-model="PageConfig[PageIndex].Ip" label="IP" class="mx-2" @change="RefreshArgements"></v-text-field>
            <v-text-field v-model="PageConfig[PageIndex].Port" label="Port" class="mx-2" @change="RefreshArgements"></v-text-field>
          </v-row>
          <v-file-input @change="OnFileSelected"></v-file-input>
          <v-text-field v-model="FinalArguments" label="Command Argument" class="mx-2" readonly></v-text-field>
        </div>
        <!-- Custom -->
        <div v-else class="mx-5">
          <v-text-field v-model="FinalArguments" label="Command Argument" class="mx-2"></v-text-field>
        </div>

      </div>
      <div v-else class="ma-5">
        <h1>Hello World</h1>
      </div>

      <AddNewPage :ShowOverlay="CanShowOverAddForm" @ResponseForm="OnResposeAddForm"/>      
    </v-main>
  </v-app>
</template>

<script>
import AddNewPage from "./components/AddNewPage";

export default {
  name: 'App',

  components: {
    AddNewPage,
  },

  data: () => ({
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
  }),

  computed: () => ({
  }),

  methods: {
    ListButtonClicked: function (params) {
      this.PageIndex = params
      this.RefreshArgements()
    },

    OnAddNewPage: function () {
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
      this.PageIndex = 0
      this.PageConfig.splice(index, 1)
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
          break;
      }
    },

    OnCountrySelected: function (country) {
      this.PageConfig[this.PageIndex].CountryType = country
      switch (country) {
        case "KOR":
          this.PageConfig[this.PageIndex].Ip = "192.168.50.166"
          this.PageConfig[this.PageIndex].Port = "10010"
          this.PageConfig[this.PageIndex].Id = "test001"
          this.PageConfig[this.PageIndex].Pw = "1234"          
          this.RefreshArgements()
          break;
        case "CHN":
          this.PageConfig[this.PageIndex].Ip = "192.168.50.201"
          this.PageConfig[this.PageIndex].Port = "10010"
          this.PageConfig[this.PageIndex].Id = "test1"
          this.PageConfig[this.PageIndex].Pw = "1234"
          this.RefreshArgements()
          break;
        case "POD":
          this.PageConfig[this.PageIndex].Ip = "192.168.50.171"
          this.PageConfig[this.PageIndex].Port = "10010"
          this.PageConfig[this.PageIndex].Id = "test1"
          this.PageConfig[this.PageIndex].Pw = "1234"
          this.RefreshArgements()
          break;
        case "Custom":
          break;
      
        default:
          break;
      }
    },

    OnFileSelected: function (file) {
      this.PageConfig[this.PageIndex].Filepath = file.path
    },
  },
};


</script>
