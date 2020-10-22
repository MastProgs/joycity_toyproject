<template>
  <v-app>
    <v-navigation-drawer
      app
      class="pt-4"
      color="grey lighten-3"
      mini-variant
      permanent
    >
      <v-btn class="d-block mx-auto mb-4" small dark circle @click="ListPushBack('hello', 'temp title')">
        <v-icon>mdi-plus</v-icon>
      </v-btn>
      <v-divider/>

      <div>
        <v-btn v-for="(item, i) in PageConfig" :key="i" class="d-block mx-auto" icon @click="ListButtonClicked(i)">
          {{ item.IconTitle }}
        </v-btn>
      </div>

    </v-navigation-drawer>

    <v-app-bar app dense flat dark>
      <h1>{{ PageConfig[PageIndex].HeadTitle }}</h1>
      <v-spacer/>
      <div v-if="PageIndex != 0">
        <v-btn class="mr-2" color="green" x-small> <!-- 버튼 클릭 시, 보는 페이지 JSON 으로 설정 저장 필요, Ctrl + s 도 연동 -->
          Save
        </v-btn>
        <v-btn color="red" x-small @click="ListErase(PageIndex)">
          Del
        </v-btn>
      </div>
    </v-app-bar>

    <v-main>
    </v-main>
  </v-app>
</template>

<script>
export default {
  name: 'App',

  components: {
  },

  data: () => ({
    PageIndex: 0,

    PageConfig:[
      { IconTitle:"Main", HeadTitle:"Client Launcher" },
      { IconTitle:"KOR", HeadTitle:"KOR Ver." },
    ],
  }),

  computed: () => ({
  }),

  methods: {
    ListButtonClicked: function (params) {
      this.PageIndex = params
    },

    ListPushBack: function (iconName, titleName) {
      this.PageConfig.push({ IconTitle: iconName, HeadTitle: titleName})
      this.ListButtonClicked(this.PageConfig.length - 1)
    },
    ListErase: function (index) {
      this.PageIndex = 0
      this.PageConfig.splice(index, 1)
    },
  },
};


</script>
