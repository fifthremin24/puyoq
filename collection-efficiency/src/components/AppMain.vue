<template>
  <v-container fluid>
    <v-row>
      <v-col cols="12" md="6">
        <v-card id="card-ability" class="elevation-10">
          <v-card-title class="headline">特攻設定</v-card-title>
          <v-card-subtitle
            >自分のデッキに設定している特攻数を設定してください (合計{{
              this.maxTotalEventAbilityCard
            }}枚まで)。</v-card-subtitle
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
            <v-row>
              <v-col offset="6" cols="4" class="text-right"> 合計 </v-col>
              <v-col cols="2">
                <span
                  v-bind:class="{
                    'error--text': hasTotalEventAbilityCardWarning,
                  }"
                  >{{ totalEventAbilityCard }}</span
                >
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="6" class="mt-0 pt-0">
                <v-select
                  label="サポ"
                  :items="eventSupporterSelect"
                  v-model="inputs.eventAbilitySupporter.selectedValue"
                ></v-select>
              </v-col>
              <v-col offset="1" cols="5" class="mt-0 pt-0">
                <v-switch
                  label="ギルド外"
                  v-model="inputs.eventAbilitySupporter.externalSupporter"
                ></v-switch>
              </v-col>
            </v-row>
            <v-row>
              <v-col>
                <div v-if="hasTotalEventAbilityCardWarning" class="error--text">
                  <p>特攻の合計は最大9枚になるように設定して下さい。</p>
                </div>
                <div v-else>
                  <p class="mb-0">
                    {{ inputs.goal }} 個収集するのに必要な魔導石はだいたい
                    <span
                      v-bind:class="{
                        'error--text': hasTotalMagicStoneWarning,
                      }"
                      >{{ totalMagicStone }}</span
                    >
                    個です (やる気効率 {{ dropPerStamina }})。
                  </p>
                  <p>
                    <span>※詳細は下で設定して下さい</span>
                  </p>
                </div>
              </v-col>
            </v-row>
          </v-card-text>
        </v-card>
      </v-col>
      <v-col cols="12" md="6">
        <v-card class="elevation-10">
          <v-card-title class="headline">ドロップ数</v-card-title>
          <v-card-subtitle
            >通常周回とチャンスボスのドロップ数を計算します。</v-card-subtitle
          >
          <v-card-text>
            <v-row>
              <v-col>
                <v-data-table
                  :headers="dropCountHeader"
                  :items="dropCountTable"
                  :items-per-page="5"
                  hide-default-footer
                  disable-sort
                ></v-data-table>
              </v-col>
            </v-row>
          </v-card-text>
          <v-card-subtitle
            >※通常周回は特攻分のドロップ数のみ表示しています。実際のドロップ数は特攻分＋ステージドロップ(ランダム)です。</v-card-subtitle
          >
        </v-card>
      </v-col>
    </v-row>
    <v-row>
      <v-col cols="12" md="6">
        <v-card class="elevation-10">
          <v-card-title class="headline">やる気効率計算</v-card-title>
          <v-card-subtitle
            >1やる気あたりのドロップ数を計算します。</v-card-subtitle
          >
          <v-card-text>
            <v-row>
              <v-col>
                <v-select
                  label="周回するステージ"
                  :items="stageLevel"
                  v-model="inputs.normalStageLevel"
                  dense
                ></v-select>
              </v-col>
            </v-row>
            <v-row>
              <v-col class="mt-n2 pt-0">
                <v-text-field
                  label="周回時のドロップ数(特攻以外)"
                  class="mt-0 pt-0"
                  type="number"
                  v-model.number="inputs.normalStageBossDrop"
                  dense
                  outlined
                >
                </v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col class="mt-n2 pt-0">
                <v-text-field
                  label="周回時のドロップ数(特攻分)"
                  class="mt-0 pt-0"
                  type="number"
                  v-model.number="normalStageCardDrop"
                  dense
                  outlined
                  readonly
                  background-color="grey lighten-4"
                >
                </v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col class="mt-n2 pt-0">
                <v-text-field
                  label="周回時の合計ドロップ数"
                  class="mt-0 pt-0"
                  type="number"
                  v-model.number="totalNormalStageDrop"
                  dense
                  outlined
                  readonly
                  background-color="grey lighten-4"
                >
                </v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col class="mt-n2 pt-0">
                <v-text-field
                  label="チャンスボスが出現するまでの周回数"
                  class="mt-0 pt-0"
                  type="number"
                  v-model.number="inputs.normalStageLoop"
                  dense
                  outlined
                >
                </v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col class="mt-n2 pt-0">
                <v-select
                  label="チャンスボス難易度"
                  :items="stageLevel"
                  v-model="inputs.chanceBossLevel"
                  dense
                ></v-select>
              </v-col>
            </v-row>
            <v-row>
              <v-col class="mt-n2 pt-0">
                <v-text-field
                  label="チャンスボスのドロップ数"
                  class="mt-0 pt-0"
                  type="number"
                  v-model.number="totalChanceBossDrop"
                  dense
                  outlined
                  readonly
                  background-color="grey lighten-4"
                >
                </v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col class="mt-n2 pt-0">
                <v-text-field
                  label="1セットのドロップ数"
                  class="mt-0 pt-0"
                  type="number"
                  v-model.number="totalDropOfCycle"
                  dense
                  outlined
                  readonly
                  background-color="grey lighten-4"
                >
                </v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col class="mt-n2 pt-0">
                <v-text-field
                  label="1セットの消費やる気合計"
                  class="mt-0 pt-0"
                  type="number"
                  v-model.number="totalStaminaOfCycle"
                  dense
                  outlined
                  readonly
                  background-color="grey lighten-4"
                >
                </v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col class="mt-n2 pt-0">
                <v-text-field
                  label="1やる気あたりのドロップ数"
                  class="mt-0 pt-0"
                  type="number"
                  v-model.number="dropPerStamina"
                  dense
                  outlined
                  readonly
                  background-color="grey lighten-4"
                >
                </v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col class="mt-n6">
                <p>
                  ※通常ステージを周回してチャンスボスを討伐するまでを1セットとします。
                </p>
              </v-col>
            </v-row>
          </v-card-text>
        </v-card>
      </v-col>
      <v-col cols="12" md="6">
        <v-card id="magic-stone" class="elevation-10">
          <v-card-title class="headline">魔導石計算</v-card-title>
          <v-card-subtitle
            >やる気効率から目標までに必要な魔導石の数を計算します。</v-card-subtitle
          >
          <v-card-text>
            <v-row>
              <v-col>
                <v-text-field
                  label="目標収集数"
                  class="mt-0 pt-0"
                  type="number"
                  v-model.number="inputs.goal"
                  dense
                  outlined
                >
                </v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col class="mt-n2 pt-0">
                <v-text-field
                  label="目標までに必要な総やる気"
                  class="mt-0 pt-0"
                  type="number"
                  v-model.number="totalStamina"
                  dense
                  outlined
                  readonly
                  background-color="grey lighten-4"
                >
                </v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col class="mt-n2 pt-0">
                <v-text-field
                  label="やる気草(50)の所持数"
                  class="mt-0 pt-0"
                  type="number"
                  v-model.number="inputs.staminaItem50"
                  dense
                  outlined
                >
                </v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col class="mt-n2 pt-0">
                <v-text-field
                  label="やる気草(100)の所持数"
                  class="mt-0 pt-0"
                  type="number"
                  v-model.number="inputs.staminaItem100"
                  dense
                  outlined
                >
                </v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col class="mt-n2 pt-0">
                <v-text-field
                  label="やる気草分のやる気"
                  class="mt-0 pt-0"
                  type="number"
                  v-model.number="totalItemStamina"
                  dense
                  outlined
                  readonly
                  background-color="grey lighten-4"
                >
                </v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col class="mt-n2 pt-0">
                <v-text-field
                  label="やる気上限"
                  class="mt-0 pt-0"
                  type="number"
                  v-model.number="inputs.maxStamina"
                  dense
                  outlined
                >
                </v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col class="mt-n2 pt-0">
                <v-text-field
                  label="イベント中の想定ランクアップ回数"
                  class="mt-0 pt-0"
                  type="number"
                  v-model.number="inputs.numOfRankUp"
                  dense
                  outlined
                >
                </v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col class="mt-n2 pt-0">
                <v-text-field
                  label="ランクアップ回復分のやる気"
                  class="mt-0 pt-0"
                  type="number"
                  v-model.number="totalRankUpStamina"
                  dense
                  outlined
                  readonly
                  background-color="grey lighten-4"
                >
                </v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col class="mt-n2 pt-0">
                <v-text-field
                  label="イベント日数"
                  class="mt-0 pt-0"
                  type="number"
                  v-model.number="inputs.totalDaysOfEvent"
                  dense
                  outlined
                >
                </v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col class="mt-n2 pt-0">
                <v-text-field
                  label="イベント期間の自然回復分のやる気"
                  class="mt-0 pt-0"
                  type="number"
                  v-model.number="totalNaturalStamina"
                  dense
                  outlined
                  readonly
                  background-color="grey lighten-4"
                >
                </v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col class="mt-n2 pt-0">
                <v-text-field
                  label="イベント開始時のやる気"
                  class="mt-0 pt-0"
                  type="number"
                  v-model.number="inputs.startStamina"
                  dense
                  outlined
                >
                </v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col class="mt-n2 pt-0">
                <v-text-field
                  label="無料分のやる気(草+RU+自然回復+開始時)"
                  class="mt-0 pt-0"
                  type="number"
                  v-model.number="totalFreeStamina"
                  dense
                  outlined
                  readonly
                  background-color="grey lighten-4"
                >
                </v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col class="mt-n2 pt-0">
                <v-text-field
                  label="魔導石未使用時の収集数"
                  class="mt-0 pt-0"
                  type="number"
                  v-model.number="totalDropOfFreeStamina"
                  dense
                  outlined
                  readonly
                  background-color="grey lighten-4"
                >
                </v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col class="mt-n2 pt-0">
                <v-text-field
                  label="不足分のやる気"
                  class="mt-0 pt-0"
                  type="number"
                  v-model.number="totalStaminaGap"
                  dense
                  outlined
                  readonly
                  background-color="grey lighten-4"
                >
                </v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col class="mt-n2 pt-0">
                <v-text-field
                  label="必要な魔導石"
                  class="mt-0 pt-0"
                  type="number"
                  v-model.number="totalMagicStone"
                  dense
                  outlined
                  readonly
                  background-color="grey lighten-4"
                >
                </v-text-field>
              </v-col>
            </v-row>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>
<script>
export default {
  name: "AppMain",

  data: () => ({
    stageLevel: ["甘口", "中辛", "辛口", "激辛", "超激辛"],

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
        stamina: 100,
        cardDrops: [50, 86, 111, 182, 295, 861],
        chanceBossDrop: 1000,
      },
    },

    dropCountHeader: [
      { text: "ステージ", value: "stage" },
      { text: "甘口", value: "d0" },
      { text: "中辛", value: "d1" },
      { text: "辛口", value: "d2" },
      { text: "激辛", value: "d3" },
      { text: "超激辛", value: "d4" },
    ],

    eventSupporterSelect: [
      { text: "特攻なし", value: -1 },
      { text: "特攻Lv1", value: 0 },
      { text: "特攻Lv2", value: 1 },
      { text: "特攻Lv3", value: 2 },
      { text: "特攻Lv4", value: 3 },
      { text: "特攻Lv5", value: 4 },
      { text: "特攻Lv6", value: 5 },
    ],

    // 自分のデッキに設定できる最大のカード数
    maxTotalEventAbilityCard: 9,

    // スタミナ回復時間(分)
    staminaHealingInterval: 3,

    debug: false,

    inputs: {
      version: 6,

      // セットしている特攻枚数
      eventAbilityCards: [
        { label: "Lv1", index: 0, value: 6 },
        { label: "Lv2", index: 1, value: 1 },
        { label: "Lv3", index: 2, value: 1 },
        { label: "Lv4", index: 3, value: 0 },
        { label: "Lv5", index: 4, value: 0 },
        { label: "Lv6", index: 5, value: 0 },
      ],

      // サポータ設定
      eventAbilitySupporter: {
        selectedValue: 5,
        externalSupporter: false,
      },

      // 周回ステージのドロップ数
      normalStageLevel: "甘口",

      // 周回ステージのドロップ数
      normalStageBossDrop: 35,

      // チャンスボスが出現するまでの周回数
      normalStageLoop: 5,

      // チャンスボスの難易度
      chanceBossLevel: "超激辛",

      // 目標数
      goal: 300000,

      // イベント日数
      totalDaysOfEvent: 10,

      // イベント開始時のやる気
      startStamina: 500,

      // スタミナ上限
      maxStamina: 500,

      // 所持やる気草
      staminaItem50: 13,
      staminaItem100: 10,

      // 想定ランクアップ数
      numOfRankUp: 5,
    },
  }),
  mounted() {
    if (localStorage[this.app] && !this.debug) {
      let saved = JSON.parse(localStorage[this.app]);
      console.log(saved);
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
    totalEventAbilityCard: function () {
      return this.inputs.eventAbilityCards.reduce((acc, x) => acc + x.value, 0);
    },
    hasTotalEventAbilityCardWarning: function () {
      return this.totalEventAbilityCard > this.maxTotalEventAbilityCard;
    },
    dropCountTable: function () {
      let r = [];
      r.push(this.calcNormalStageCardDropDict());
      //r.push(this.calcChanceBossCardDropDict())
      r.push(this.calcChanceBossTotalDropDict());
      return r;
    },
    normalStageCardDrop: function () {
      let level = this.inputs.normalStageLevel;
      let cardNums = this.inputs.eventAbilityCards.map((x) => x.value);
      let supporter = this.inputs.eventAbilitySupporter;
      return this.calcNormalStageCardDrop(level, cardNums, supporter);
    },
    totalNormalStageDrop: function () {
      return this.inputs.normalStageBossDrop + this.normalStageCardDrop;
    },
    totalChanceBossDrop: function () {
      let level = this.inputs.chanceBossLevel;
      let cardNums = this.inputs.eventAbilityCards.map((x) => x.value);
      let supporter = this.inputs.eventAbilitySupporter;
      return this.calcChanceBossTotalDrop(level, cardNums, supporter);
    },
    totalDropOfCycle: function () {
      return (
        this.totalNormalStageDrop * this.inputs.normalStageLoop +
        this.totalChanceBossDrop
      );
    },
    totalStaminaOfCycle: function () {
      return (
        this.normalStageStamina * this.inputs.normalStageLoop +
        this.chanceBossStamina
      );
    },
    normalStageStamina: function () {
      return this.stageDefinition[this.inputs.normalStageLevel].stamina;
    },
    chanceBossStamina: function () {
      return this.stageDefinition[this.inputs.chanceBossLevel].stamina;
    },
    dropPerStamina: function () {
      return this.round(this.totalDropOfCycle / this.totalStaminaOfCycle);
    },
    totalStamina: function () {
      return Math.ceil(this.inputs.goal / this.dropPerStamina);
    },
    totalHoursOfEvent: function () {
      if (this.inputs.totalDaysOfEvent <= 0) return 0;
      return this.inputs.totalDaysOfEvent * 24 - 15; // 15時スタート
    },
    totalNaturalStamina: function () {
      return (this.totalHoursOfEvent * 60) / this.staminaHealingInterval;
    },
    totalItemStamina: function () {
      return 50 * this.inputs.staminaItem50 + 100 * this.inputs.staminaItem100;
    },
    totalRankUpStamina: function () {
      return this.inputs.maxStamina * this.inputs.numOfRankUp;
    },
    totalStaminaGap: function () {
      let r = this.totalStamina - this.totalFreeStamina;
      if (r < 0) {
        r = 0;
      }
      return r;
    },
    totalMagicStone: function () {
      return this.round(this.totalStaminaGap / this.inputs.maxStamina);
    },
    hasTotalMagicStoneWarning: function () {
      return this.totalMagicStone > 0;
    },
    totalFreeStamina: function () {
      let r = 0;
      // dirty hack: Force convert empty string to 0
      r += this.inputs.startStamina * 1;
      r += this.totalNaturalStamina * 1;
      r += this.totalItemStamina * 1;
      r += this.totalRankUpStamina * 1;
      return r;
    },
    totalDropOfFreeStamina: function () {
      return this.round(this.totalFreeStamina * this.dropPerStamina);
    },
  },
  methods: {
    calcNormalStageCardDropDict: function () {
      return this.calcDropDict(
        "通常周回(特攻分のみ)",
        this.calcNormalStageCardDrop
      );
    },
    calcChanceBossCardDropDict: function () {
      return this.calcDropDict(
        "チャンスボス(特攻分のみ)",
        this.calcChanceBossCardDrop
      );
    },
    calcChanceBossTotalDropDict: function () {
      return this.calcDropDict(
        "チャンスボス(合計)",
        this.calcChanceBossTotalDrop
      );
    },
    calcDropDict: function (stage, fn) {
      let cardNums = this.inputs.eventAbilityCards.map((x) => x.value);
      let supporter = this.inputs.eventAbilitySupporter;
      let r = {};
      r["stage"] = stage;
      for (let i = 0; i < this.stageLevel.length; i++) {
        r["d" + i] = fn(this.stageLevel[i], cardNums, supporter);
      }
      return r;
    },
    calcNormalStageCardDrop: function (level, cardNums, supporter) {
      let stage = this.stageDefinition[level];
      let cardDrop = this.sumProduct(stage.cardDrops, cardNums);

      let supporterDrop = 0;
      if (supporter.selectedValue >= 0) {
        // チャンボ甘口で Lv2 の外部サポのみの場合 56 になる
        // 外部サポの切り上げをしてから特攻効果を 3 倍にする流れ
        // ceil(3 / 2) * 3 + 50 = 56
        supporterDrop = stage.cardDrops[supporter.selectedValue];
        if (supporter.externalSupporter)
          supporterDrop = Math.ceil(supporterDrop / 2);
      }

      return cardDrop + supporterDrop;
    },
    calcChanceBossCardDrop: function (level, cardNums, supporter) {
      let normal_drop = this.calcNormalStageCardDrop(
        level,
        cardNums,
        supporter
      );
      return normal_drop * 3;
    },
    calcChanceBossTotalDrop: function (level, cardNums, supporter) {
      let stage = this.stageDefinition[level];
      let cardDrop = this.calcChanceBossCardDrop(level, cardNums, supporter);
      return cardDrop + stage.chanceBossDrop;
    },
    sumProduct: function (arr1, arr2) {
      return arr1.map((x, i) => x * arr2[i]).reduce((acc, x) => acc + x, 0);
    },
    round: function (v) {
      // 小数点二桁まで
      return Math.round(v * 100) / 100;
    },
  },
};
</script>
