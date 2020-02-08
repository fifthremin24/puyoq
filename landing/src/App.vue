<template>
  <v-app>
    <v-app-bar app dense>
      <v-toolbar-title>収集着地計算</v-toolbar-title>
      <v-spacer></v-spacer>
      <v-menu left bottom>
        <template v-slot:activator="{ on }">
          <v-btn icon v-on="on">
            <v-icon>mdi-apps</v-icon>
          </v-btn>
        </template>
        <v-list>
          <v-subheader inset>Tool</v-subheader>
          <v-list-item v-for="(x, i) in apps" v-bind:key="i" :href="x.href">
            <v-list-item-icon>
              <v-icon v-text="x.icon"></v-icon>
            </v-list-item-icon>
            <v-list-item-content>
              <v-list-item-title v-text="x.text"></v-list-item-title>
            </v-list-item-content>
          </v-list-item>
        </v-list>
      </v-menu>
      <v-menu left bottom>
        <template v-slot:activator="{ on }">
          <v-btn icon v-on="on">
            <v-icon>mdi-dots-vertical</v-icon>
          </v-btn>
        </template>
        <v-list>
          <div v-for="(x, i) in menus" v-bind:key="i">
            <v-subheader inset v-text="x.subheader"></v-subheader>
            <v-list-item v-for="(c, i) in x.submenus" v-bind:key="i" :href="c.href">
              <v-list-item-icon>
                <v-icon v-text="c.icon"></v-icon>
              </v-list-item-icon>
              <v-list-item-content>
                <v-list-item-title v-text="c.text"></v-list-item-title>
              </v-list-item-content>
            </v-list-item>
          </div>
        </v-list>
      </v-menu>
    </v-app-bar>
    <v-content>
      <AppMain/>
    </v-content>
    <v-footer dark padless>
      <v-card class="flex" flat tile>
        <v-card-text class="py-2 white--text text-center">
          2020 — <strong>れみんちゃんねる</strong>
        </v-card-text>
      </v-card>
    </v-footer>
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
    apps: [
      { text: "収集効率計算", icon: "mdi-bookmark-outline", href: "https://fifthremin24.github.io/puyoq/collection-efficiency/" },
      { text: "収集着地計算", icon: "mdi-bookmark-outline", href: "https://fifthremin24.github.io/puyoq/landing/" },
      { text: "塔のスコア計算", icon: "mdi-bookmark-outline", href: "https://fifthremin24.github.io/tower-score/matrix.html" },
      { text: "マルチ招待用リンク生成", icon: "mdi-bookmark-outline", href: "https://fifthremin24.github.io/multi-invite/index.html" },
    ],
    menus: [
      {
        subheader: "Contribute",
        submenus: [
          { text: "ソースコード", icon: "mdi-github-circle", href: "https://github.com/fifthremin24/puyoq/tree/master/landing/" },
          { text: "課題の一覧", icon: "mdi-format-list-checks", href: "https://github.com/fifthremin24/puyoq/issues" },
          { text: "バグ報告", icon: "mdi-bug", href: "https://github.com/fifthremin24/puyoq/issues/new" },
        ]
      },
      {
        subheader: "Contact",
        submenus: [
          { text: "Twitter", icon: "mdi-twitter", href: "https://twitter.com/fifthremin24" },
          { text: "YouTube", icon: "mdi-youtube", href: "https://www.youtube.com/channel/UCyl4JuSF_-tTcmjPEoWAhkQ" },
          { text: "Blog", icon: "mdi-fountain-pen-tip", href: "https://fifthremin24.hatenablog.com" },
        ]
      },
    ],
  }),
};
</script>
