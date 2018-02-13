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
               :class="{'best-option': bestOption(option, test),
               'elected-option': option.elected}">
            <div>{{ option.label }}</div>
            <div class="option-headline" v-if="bestOption(option, test) && !option.elected">
              Best </div>
            <div class="option-headline" v-if="option.elected && !bestOption(option, test)">
              Elected </div>
            <div class="option-headline" v-if="option.elected && bestOption(option, test)">
              Elected & Best</div>
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
              <div class="label-overlay" :class="{'elected': option.elected}"
                   @click="toggleElectOption(test.id, option.id)"></div>
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
  }),
  methods: {
    bestOption: (option, test) => {
      let max = (test.options[0].conversionCount || 0) / test.options[0].decisionCount;
      test.options.forEach((o) => {
        if (o.conversionCount / o.decisionCount > max) {
          max = o.conversionCount / o.decisionCount;
        }
      });
      return (max > 0 && max === option.conversionCount / option.decisionCount);
    },
    toggleElectOption(testId, optionId) {
      // Call to the graphql mutation
      this.$apollo.mutate({
        // Query
        mutation: gql`mutation ($testId: String!, $optionId: String!) {
          toggleElectOption(testId: $testId, optionId: $optionId) {
            id,
            name,
            options {
              id,
              elected
            }
          }
        }`,

        update: (store) => {
          const TESTS_ALL = gql`{
            tests{
              id
              name
              totalOptionsDecisions
              options {
                elected
                id
                weight
                decisionCount
                displayCount
                conversionCount
                label
              }
            }
          }`;
          const data = store.readQuery({ query: TESTS_ALL });
          store.writeQuery({ query: TESTS_ALL, data });
        },

        // Parameters
        variables: {
          testId,
          optionId,
        },
      }).then((data) => {
        // Result
        console.log(data);
      }).catch((error) => {
        // Error
        console.error(error);
      });
    },
  },
  apollo: {
    tests: gql`{
      tests{
        id
        name
        totalOptionsDecisions
        options {
          elected
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
<style lang="stylus" rel="stylesheet/stylus" scoped>
  .tests
    font-size 20px
    &>div
      margin-top 30px
      .options
        display flex
        height 100%
        padding-top 25px
        margin auto
        width 70%
        justify-content center
        font-size 15px
        .label
          min-width 80
          position relative
          margin-left 5px
          margin-right 5px
          padding 15px
          .option-headline
            height 0
            position relative
            top -75px
            text-transform uppercase
            font-size 12px
            color grey
            font-weight 500
          &.best-option
            background-image linear-gradient(135deg, #11998e 0%, #38ef7d 100%);
            color white
            border-radius 5px
            .label-overlay
              display block
            &>div:first-child, .label-overlay
              border 1px solid white
          &.elected-option
            background-image linear-gradient(135deg, #4e54c8 0%, #8f94fb 100%);
            color white
            border-radius 5px
            .label-overlay
              display block
            &>div:first-child, .label-overlay
              border 1px solid white
            .label-overlay
              display block
          &.best-option.elected-option
            background-image: linear-gradient(120deg, #FF4E50 0%, #F9D423 100%);
            .label-overlay
              display block
          &>div:first-child
            border 1px solid grey
            margin 5px
            border-radius 25px
            padding 5px
          .label-overlay
            transition 0.1s
            position absolute
            bottom 0
            transform translate(-50%, -50%)
            left 50%
            margin auto
            border 1px solid grey
            border-radius 25px
            font-size 12px
            width 60px
            padding 5px
            margin-top 10px
            display none
            cursor pointer
            &:before
              content 'Elect'
            &.elected:before
              content 'Deny'
            &:hover
              background-color rgba(0,0,0,0.3)
              color white
          &:hover
            .label-overlay
              display block
          .statistics
            margin-bottom 40px
            span
              font-size 12px
</style>
