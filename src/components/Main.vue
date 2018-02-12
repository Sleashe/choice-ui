<template>
  <div>
    <h1>Choice</h1>
    <div class="tests">
      <span v-if="tests.length === 0">No test have been created yet :(</span>
      <div v-for="test in tests" :key="test._id">
        <span>{{ test.name }}</span>
        <div class="options">
          <div v-for="option in test.options" :key="option.id" class="label"
               :style="{width: option.weight + '%' }"
               :class="{'best-option': bestOption(option, test)}"
               @mouseenter="displayActions(option)">
            <div>{{ option.label }}</div>
            <div class="best-option-headline" v-if="bestOption(option, test)">Best Option</div>
            <div class="statistics">
              <span>Decisions: {{ option.decisionCount }}</span>
              <span :style="{color: ((option.decisionCount / test.totalOptionsDecisions * 100
              - option.weight) < 3 && (option.decisionCount / test.totalOptionsDecisions * 100
              - option.weight) > -3 ) ? 'green' : 'red' }"
                    v-if="option.decisionCount > 0">({{
                (option.decisionCount / test.totalOptionsDecisions * 100
              - option.weight).toFixed(1)}}%)</span><br/>
              <span v-if="option.displayCount"> Displays:
                {{ option.displayCount }}<br/></span>
              <span v-if="!option.displayCount"> Displays: 0<br/></span>
              <span v-if="option.conversionCount">
                Conversions: {{ option.conversionCount }}
                ({{ (option.conversionCount / option.decisionCount * 100).toFixed(2)}}%)</span>
              <span v-if="!option.conversionCount"> Conversions: 0</span>
              <div class="label-overlay"></div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import gql from 'graphql-tag';

export default {
  name: 'Main',
  data: () => ({
    tests: [],
    colors: ['#1abc9c', '#2ecc71', '#3498db', '#9b59b6'],
    optionActionsDisplay: {},
  }),
  methods: {
    bestOption: (option, test) => {
      let max = test.options[0].conversionCount || 0;
      test.options.forEach((o) => {
        if (o.conversionCount > max) {
          max = o.conversionCount;
        }
      });
      return (max === option.conversionCount);
    },
    displayActions(option) {
      console.log(this.optionActionsDisplay);
      this.optionActionsDisplay[option.id] = true;
    },
  },
  apollo: {
    tests: gql`{
      tests{
        name
        totalOptionsDecisions
        options {
          id
          weight
          decisionCount
          displayCount
          conversionCount
          label
        }
      }
    }`,
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="stylus" scoped>
  .tests
    font-size 20px
    &>div
      margin-top 30px
      .options
        padding-top 25px
        margin auto
        width 50%
        justify-content center
        font-size 15px
        display flex
        transition 0.3s
        .label
          height 100%
          transition 0.3s
          padding 15px
          &.best-option
            background-color: rgba(0,255,0,0.17);
            border-radius 5px
            transition 0.3s
            .best-option-headline
              position relative
              top -75px
              text-transform uppercase
              font-size 11px
          &>div:first-child
            cursor pointer
            transition 0.2s
            border 1px solid grey
            margin 5px
            border-radius 25px
            padding 5px
          &:hover
            .label-overlay
              transition 0.15s
              margin auto
              border 1px solid grey
              border-radius 25px
              font-size 12px
              width 60px
              padding 5px
              margin-top 10px
              cursor pointer
              &:before
                content 'Elect'
              &:hover
                background-color rgba(0,0,0,0.3)
                color white
          .statistics
            span
              font-size 12px
</style>
