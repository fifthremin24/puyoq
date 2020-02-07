<template>
  <v-app>
    <v-app-bar app dense hide-on-scroll>
      <v-toolbar-title>ぷよクエ収集 着地計算ツール</v-toolbar-title>
      <v-spacer></v-spacer>
      <v-menu left bottom>
        <template v-slot:activator="{ on }">
          <v-btn icon v-on="on">
            <v-app-bar-nav-icon></v-app-bar-nav-icon>
          </v-btn>
        </template>
        <v-list>
          <v-list-item v-for="(x, i) in menus" v-bind:key="i" :href="x.href">
            <v-list-item-icon>
              <v-icon v-text="x.icon"></v-icon>
            </v-list-item-icon>
            <v-list-item-content>
              <v-list-item-title v-text="x.text"></v-list-item-title>
            </v-list-item-content>
          </v-list-item>
        </v-list>
      </v-menu>
    </v-app-bar>
    <v-content>
      <AppMain/>
    </v-content>
  </v-app>
</template>

<script>
import AppMain from './components/AppMain';

export default {
  name: 'App',

  components: {
    AppMain,
  },

  data: () => ({
    menus: [
      { text: "ソースコード", icon: "mdi-github-circle", href: "https://github.com/fifthremin24/puyoq/tree/master/landing" },
      { text: "課題の一覧", icon: "mdi-format-list-checks", href: "https://github.com/fifthremin24/puyoq/issues" },
      { text: "バグ報告", icon: "mdi-bug", href: "https://github.com/fifthremin24/puyoq/issues/new" },
      { text: "Twitter", icon: "mdi-twitter", href: "https://twitter.com/fifthremin24" },
      { text: "YouTube", icon: "mdi-youtube", href: "https://www.youtube.com/channel/UCyl4JuSF_-tTcmjPEoWAhkQ" },
      { text: "Blog", icon: "mdi-fountain-pen-tip", href: "https://fifthremin24.hatenablog.com" },
    ],
  }),
};
</script>
