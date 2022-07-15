<template>
  <v-container fluid>
    <v-row>
      <v-col cols="12" md="6">
        <v-card class="elevation-10">
          <v-card-title class="headline">特攻設定</v-card-title>
          <v-card-subtitle
            >特攻の組み合わせを全部計算するのでデッキに設定していない特攻もカウントして設定してください。サポ分のカウントは不要です。</v-card-subtitle
          >
          <v-card-text>
            <v-row v-for="x in inputs.eventAbilityCards" v-bind:key="x.label">
              <v-col cols="10">
                <v-slider
                  v-model="x.value"
                  :max="9"
                  :min="0"
                  step="1"
                  ticks="always"
                  tick-size="2"
                  hide-details
                  v-bind:label="x.label"
                >
                </v-slider>
              </v-col>
              <v-col cols="2">
                <v-text-field
                  v-model="x.value"
                  class="mt-0 pt-0"
                  type="number"
                  hide-details
                  single-line
                ></v-text-field>
              </v-col>
            </v-row>
          </v-card-text>
        </v-card>
      </v-col>
      <v-col cols="12" md="6">
        <v-card class="elevation-10">
          <v-card-title class="headline">サポ設定</v-card-title>
          <v-card-subtitle
            >利用するサポの特攻レベルを設定してください。特攻なしのパターンは自動的に計算します。</v-card-subtitle
          >
          <v-card-text>
            <v-row>
              <v-subheader>ギルド内</v-subheader>
            </v-row>
            <v-row class="mr-0 mt-n5" align="center" justify="space-around">
              <v-checkbox
                v-for="x in inputs.eventAbilityCards.slice(0, 3)"
                v-bind:key="x.label"
                v-model="x.internal_supporter"
                :label="x.label"
              >
              </v-checkbox>
            </v-row>
            <v-row class="mr-0 mt-n5" align="center" justify="space-around">
              <v-checkbox
                v-for="x in inputs.eventAbilityCards.slice(3, 6)"
                v-bind:key="x.label"
                v-model="x.internal_supporter"
                :label="x.label"
              >
              </v-checkbox>
            </v-row>
            <v-row>
              <v-subheader>ギルド外</v-subheader>
            </v-row>
            <v-row class="mr-0 mt-n5" align="center" justify="space-around">
              <v-checkbox
                v-for="x in inputs.eventAbilityCards.slice(0, 3)"
                v-bind:key="x.label"
                v-model="x.external_supporter"
                :label="x.label"
              >
              </v-checkbox>
            </v-row>
            <v-row class="mr-0 mt-n5" align="center" justify="space-around">
              <v-checkbox
                v-for="x in inputs.eventAbilityCards.slice(3, 6)"
                v-bind:key="x.label"
                v-model="x.external_supporter"
                :label="x.label"
              >
              </v-checkbox>
            </v-row>
          </v-card-text>
        </v-card>
      </v-col>
      <v-col>
        <v-card class="elevation-10">
          <v-card-title class="headline">着地計算</v-card-title>
          <v-card-subtitle
            >収集数の現在値と目標値を設定するとチャンスボスを討伐するときの特攻数を計算します。</v-card-subtitle
          >
          <v-card-text>
            <v-row>
              <v-col class="mt-0 pt-0" cols="12" md="6">
                <v-text-field
                  class="mt-0 pt-0"
                  v-model.number="inputs.currentCount"
                  type="number"
                  label="現在値"
                  outlined
                  :rules="[rules.required, rules.positiveNumber]"
                >
                </v-text-field>
              </v-col>
              <v-col class="mt-0 pt-0" cols="12" md="6">
                <v-text-field
                  class="mt-0 pt-0"
                  v-model.number="inputs.targetCount"
                  type="number"
                  label="目標値"
                  outlined
                  :rules="[rules.required, rules.positiveNumber]"
                >
                </v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col class="mt-n5 pt-0" cols="12">
                <v-switch
                  class="mt-0 pt-0"
                  v-model="inputs.leaderCardSwitch"
                  label="リーダーカードは特攻以外を使用"
                ></v-switch>
              </v-col>
            </v-row>
            <v-row>
              <v-col class="mt-0 pt-0">
                <div v-if="landingSucceeded" class="primary--text">
                  <p class="mb-0">
                    チャンスボス
                    {{ landingPlanTable.length }} 回で着地できます。
                  </p>
                  <p class="mb-1">{{ landingPlanExpression }}</p>
                </div>
                <div v-else class="error--text">{{ landingErrorMessage }}</div>
                <v-data-table
                  :headers="dropCombinationHeader"
                  :items="landingPlanTable"
                  :items-per-page="5"
                  hide-default-footer
                >
                </v-data-table>
              </v-col>
            </v-row>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
    <v-row>
      <v-col cols="12">
        <v-card class="elevation-10">
          <v-card-title class="headline">全ドロップの組み合わせ</v-card-title>
          <v-card-subtitle>
            特攻の組み合わせはドロップ数ごとに 1 パターンのみ表示します。
          </v-card-subtitle>
          <v-data-table
            :headers="dropCombinationHeader"
            :items="dropCombinationTableReverse"
            :items-per-page="5"
          >
          </v-data-table>
        </v-card>
      </v-col>
    </v-row>
    <!--
    <v-row>
      <v-col cols="12">
        <v-card class="elevation-10">
          <v-card-title class="headline">特攻組み合わせ</v-card-title>
          <v-data-table :headers="eventCardCombinationHeader" :items="eventCardCombinationTable" :items-per-page="10">
          </v-data-table>
        </v-card>
      </v-col>
    </v-row>
    -->
  </v-container>
</template>

<script>
export default {
  name: "AppMain",

  app: "landing",

  data: () => ({
    rules: {
      required: (v) => !!v || "Required",
      positiveNumber: (v) => v >= 0 || "Positive number",
    },
    eventAbilityKeys: ["lv1", "lv2", "lv3", "lv4", "lv5"],

    eventDifficulty: ["甘口", "中辛", "辛口", "激辛", "超激辛"],

    stageDefinition: {
      甘口: {
        stamina: 10, // 消費やる気
        cardDrops: [3, 4, 5, 8, 12, 35], // 特攻ドロップ (Lv1 .. Lv5)
        chanceBossDrop: 50, // チャンスボスドロップ
      },
      中辛: {
        stamina: 15,
        cardDrops: [5, 8, 10, 16, 24, 70],
        chanceBossDrop: 75,
      },
      辛口: {
        stamina: 20,
        cardDrops: [8, 12, 15, 24, 36, 105],
        chanceBossDrop: 140,
      },
      激辛: {
        stamina: 30,
        cardDrops: [15, 24, 30, 48, 72, 210],
        chanceBossDrop: 250,
      },
      超激辛: {
        stamina: 50,
        cardDrops: [30, 48, 60, 96, 144, 420],
        chanceBossDrop: 500,
      },
    },

    dropCombinationHeader: [
      { text: "チャンスボス", value: "chance_boss" },
      { text: "特攻Lv1", value: "lv1" },
      { text: "特攻Lv2", value: "lv2" },
      { text: "特攻Lv3", value: "lv3" },
      { text: "特攻Lv4", value: "lv4" },
      { text: "特攻Lv5", value: "lv5" },
      { text: "特攻Lv6", value: "lv6" },
      { text: "サポ", value: "supporter" },
      { text: "ドロップ数", value: "drop" },
    ],
    eventCardCombinationHeader: [
      { text: "特攻Lv1", value: "lv1" },
      { text: "特攻Lv2", value: "lv2" },
      { text: "特攻Lv3", value: "lv3" },
      { text: "特攻Lv4", value: "lv4" },
      { text: "特攻Lv5", value: "lv5" },
      { text: "特攻Lv6", value: "lv6" },
      { text: "特攻合計", value: "sum" },
    ],

    landingSucceeded: false,
    landingErrorMessage: "",

    debug: false,

    inputs: {
      version: 3,
      eventAbilityCards: [
        {
          label: "Lv1",
          index: 0,
          value: 9,
          internal_supporter: true,
          external_supporter: true,
        },
        {
          label: "Lv2",
          index: 1,
          value: 1,
          internal_supporter: true,
          external_supporter: true,
        },
        {
          label: "Lv3",
          index: 2,
          value: 1,
          internal_supporter: true,
          external_supporter: true,
        },
        {
          label: "Lv4",
          index: 3,
          value: 0,
          internal_supporter: true,
          external_supporter: true,
        },
        {
          label: "Lv5",
          index: 4,
          value: 0,
          internal_supporter: true,
          external_supporter: true,
        },
        {
          label: "Lv6",
          index: 5,
          value: 0,
          internal_supporter: true,
          external_supporter: true,
        },
      ],
      currentCount: 240808,
      targetCount: 242424,
      leaderCardSwitch: false,
    },
  }),
  mounted() {
    if (localStorage[this.app] && !this.debug) {
      let saved = JSON.parse(localStorage[this.app]);
      if (saved && saved.version == this.inputs.version) {
        this.inputs = saved;
      }
    }
  },
  watch: {
    inputs: {
      handler(v) {
        localStorage[this.app] = JSON.stringify(v);
      },
      deep: true,
    },
  },
  computed: {
    eventAbilityCardValues: function () {
      return this.inputs.eventAbilityCards.map((x) => x.value);
    },
    landingPlanTable: function () {
      return this.createLandingPlanTable() || [];
    },
    landingPlanExpression: function () {
      let args = [
        this.inputs.currentCount,
        ...this.landingPlanTable.map((x) => x.drop),
      ];
      return args.join(" + ") + " = " + this.inputs.targetCount;
    },
    dropCombinationTable: function () {
      return this.createDropCombinationTable() || [];
    },
    dropCombinationTableReverse: function () {
      let copy = [...this.dropCombinationTable];
      return copy.reverse();
    },
    eventCardCombination: function () {
      let start = 0;
      let finish = this.inputs.leaderCardSwitch ? 8 : 9;
      let cards = this.eventAbilityCardValues;
      return this.calcEventCardCombination(start, finish, cards);
    },
    eventCardCombinationTable: function () {
      return this.createEventCardCombinationTable() || [];
    },
  },
  methods: {
    createLandingPlanTable: function () {
      this.landingSucceeded = false;
      this.landingErrorMessage = "";

      if (
        !Number.isInteger(this.inputs.currentCount) ||
        this.inputs.currentCount < 0
      ) {
        this.landingErrorMessage = "現在値には正数を指定して下さい。";
        console.log("Invalid currentCount: " + this.inputs.currentCount);
        return;
      }
      if (
        !Number.isInteger(this.inputs.targetCount) ||
        this.inputs.targetCount < 0
      ) {
        this.landingErrorMessage = "目標値には正数を指定して下さい。";
        console.log("Invalid targetCount: " + this.inputs.targetCount);
        return;
      }

      let diff = this.inputs.targetCount - this.inputs.currentCount;
      if (!Number.isInteger(diff)) {
        this.landingErrorMessage = "現在地・目標値には正数を指定して下さい。";
        console.log("Diff is not integer: " + diff);
        return;
      }
      if (diff == 0) {
        this.landingErrorMessage = "着地済みです。";
        console.log("Too small diff: " + diff);
        return;
      }
      if (diff < 0) {
        this.landingErrorMessage = "目標値を超えています。";
        console.log("Too small diff: " + diff);
        return;
      }
      if (diff < 50) {
        // チャンスボスは最低でも 50 がドロップする
        this.landingErrorMessage = "目標値が近すぎて着地できません。";
        console.log("Too small diff: " + diff);
        return;
      }
      if (diff > 8000) {
        this.landingErrorMessage = "目標値が遠すぎて着地計算できません。";
        console.log("Too large diff: " + diff);
        return;
      }
      let combi = this.dropCombinationTable;

      let r = [];
      if (
        // 1 回または同じ値2回で着地できる場合
        this.createLandingPlanTableSimple(r, diff, combi) ||
        // 組み合わせで着地する場合
        this.createLandingPlanTableRecur(r, diff, combi, 1) ||
        this.createLandingPlanTableRecur(r, diff, combi, 2)
      ) {
        this.landingSucceeded = true;
        return r;
      }

      this.landingErrorMessage = "現在の値では着地できません。";
      return;
    },
    createLandingPlanTableSimple: function (r, diff, combi) {
      let max = 3;
      for (let n = 1; n <= max; n++) {
        let found = combi.find((x) => x.drop == diff / n);
        if (found) {
          for (let i = 0; i < n; i++) {
            r.push(found);
          }
          return true;
        }
      }

      return false;
    },
    createLandingPlanTableRecur: function (r, diff, combi, maxDepth) {
      let fn = function (stack, rest, depth) {
        // console.log(depth + ": " + rest)
        if (rest == 0) {
          r.push(...stack);
          return true;
        }
        if (rest < 50) {
          return false;
        }
        if (maxDepth <= depth) return false;

        let maxIndex = combi.findIndex((x) => x.drop >= rest);
        if (maxIndex < 0) maxIndex = combi.length - 1;
        for (let i = maxIndex; i >= 0; i--) {
          let c = combi[i];
          if (fn([c, ...stack], rest - c.drop, depth + 1)) return true;
        }
        return false;
      };

      return fn([], diff, 0);
    },
    createDropCombinationTable: function () {
      let r = [];
      let no = 0;
      let seen = {};
      for (let i = 0; i < this.eventCardCombination.length; i++) {
        let combi = this.eventCardCombination[i];
        for (let j = 0; j < this.eventDifficulty.length; j++) {
          let difficulty = this.eventDifficulty[j];
          this.createDropCombinationMaybe(
            r,
            seen,
            no++,
            difficulty,
            combi,
            null,
            "none"
          );
          for (let k = 0; k < this.inputs.eventAbilityCards.length; k++) {
            let supporter = this.inputs.eventAbilityCards[k];
            if (supporter.internal_supporter)
              this.createDropCombinationMaybe(
                r,
                seen,
                no,
                difficulty,
                combi,
                supporter,
                "internal"
              );
            if (supporter.external_supporter)
              this.createDropCombinationMaybe(
                r,
                seen,
                no,
                difficulty,
                combi,
                supporter,
                "external"
              );
          }
        }
      }

      // Sort by drop
      r.sort((a, b) => a.drop - b.drop);

      return r;
    },
    createDropCombinationMaybe: function (
      r,
      seen,
      no,
      difficulty,
      combi,
      supporter,
      supporter_mode
    ) {
      let total_drop = this.calcTotalDrop(
        difficulty,
        combi,
        supporter,
        supporter_mode
      );
      if (seen[total_drop]) return;
      seen[total_drop] = true;

      r.push({
        no: no,
        lv1: combi[0],
        lv2: combi[1],
        lv3: combi[2],
        lv4: combi[3],
        lv5: combi[4],
        lv6: combi[5],
        supporter: this.createSupporterName(supporter, supporter_mode),
        chance_boss: difficulty,
        drop: total_drop,
      });
    },
    createSupporterName: function (supporter, supporter_mode) {
      if (supporter_mode == "none") return "特攻なし";
      else if (supporter_mode == "external")
        return supporter.label + " (ギルド外)";
      else return supporter.label + " (ギルド内)";
    },
    calcTotalDrop: function (difficulty, combi, supporter, supporter_mode) {
      let stage = this.stageDefinition[difficulty];
      let cardDrop = this.sumProduct(stage.cardDrops, combi);

      let supporter_drop = 0;
      if (supporter_mode != "none") {
        // チャンボ甘口で Lv2 の外部サポのみの場合 56 になる
        // 外部サポの切り上げをしてから特攻効果を 3 倍にする流れ
        // ceil(3 / 2) * 3 + 50 = 56
        supporter_drop = stage.cardDrops[supporter.index];
        if (supporter_mode == "external")
          supporter_drop = Math.ceil(supporter_drop / 2);
      }

      return (cardDrop + supporter_drop) * 3 + stage.chanceBossDrop;
    },
    sumProduct: function (arr1, arr2) {
      return arr1.map((x, i) => x * arr2[i]).reduce((acc, x) => acc + x, 0);
    },
    createEventCardCombinationTable: function () {
      let r = [];
      let combi = this.eventCardCombination;
      for (let i = 0; i < combi.length; i++) {
        r.push({
          no: i,
          lv1: combi[i][0],
          lv2: combi[i][1],
          lv3: combi[i][2],
          lv4: combi[i][3],
          lv5: combi[i][4],
          sum: combi[i].reduce((acc, e) => acc + e),
        });
      }
      return r;
    },
    calcEventCardCombination: function (start, finish, cards) {
      let rec = function (
        r,
        max,
        curr,
        c1,
        c2,
        c3,
        c4,
        c5,
        c6,
        l1,
        l2,
        l3,
        l4,
        l5,
        l6
      ) {
        let n = c1 + c2 + c3 + c4 + c5 + c6;
        if (n == max) {
          r.push([c1, c2, c3, c4, c5, c6]);
          return;
        }
        if (l1 > 0 && curr <= 1)
          rec(
            r,
            max,
            1,
            c1 + 1,
            c2,
            c3,
            c4,
            c5,
            c6,
            l1 - 1,
            l2,
            l3,
            l4,
            l5,
            l6
          );
        if (l2 > 0 && curr <= 2)
          rec(
            r,
            max,
            2,
            c1,
            c2 + 1,
            c3,
            c4,
            c5,
            c6,
            l1,
            l2 - 1,
            l3,
            l4,
            l5,
            l6
          );
        if (l3 > 0 && curr <= 3)
          rec(
            r,
            max,
            3,
            c1,
            c2,
            c3 + 1,
            c4,
            c5,
            c6,
            l1,
            l2,
            l3 - 1,
            l4,
            l5,
            l6
          );
        if (l4 > 0 && curr <= 4)
          rec(
            r,
            max,
            4,
            c1,
            c2,
            c3,
            c4 + 1,
            c5,
            c6,
            l1,
            l2,
            l3,
            l4 - 1,
            l5,
            l6
          );
        if (l5 > 0 && curr <= 5)
          rec(
            r,
            max,
            5,
            c1,
            c2,
            c3,
            c4,
            c5 + 1,
            c6,
            l1,
            l2,
            l3,
            l4,
            l5 - 1,
            l6
          );
        if (l6 > 0 && curr <= 6)
          rec(
            r,
            max,
            6,
            c1,
            c2,
            c3,
            c4,
            c5,
            c6 + 1,
            l1,
            l2,
            l3,
            l4,
            l5,
            l6 - 1
          );
      };
      let r = [];
      for (let max = start; max <= finish; max++)
        rec(r, max, 1, 0, 0, 0, 0, 0, 0, ...cards);
      return r;
    },
  },
};
</script>
