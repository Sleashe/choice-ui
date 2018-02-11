<template>
  <div class="hello">
    <h1>Choice</h1>
    <div class="tests">
      <span v-if="tests.length === 0">No test have been created yet :(</span>
      <div v-for="test in tests" :key="test._id">
        <span>{{ test.name }}</span>
        <div class="options">
          <div v-for="option in test.options" :key="option._id" class="label"
               :style="{width: option.weight + '%' }">
            <div>{{ option.label }}</div>
            <div class="decisionCount">
              <span>{{ option.decisionCount }}</span>
              <span :style="{color: ((option.decisionCount / test.totalOptionsDecisions * 100
              - option.weight) < 3 && (option.decisionCount / test.totalOptionsDecisions * 100
              - option.weight) > -3 ) ? 'green' : 'red' }"
                    v-if="option.decisionCount > 0">({{
                (option.decisionCount / test.totalOptionsDecisions * 100
              - option.weight).toFixed(1)}}%)</span>
              <span> | Displays: {{ option.displayCount }}</span>
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
  name: 'HelloWorld',
  data: () => ({
    tests: '',
  }),
  props: {
    msg: String,
  },
  apollo: {
    tests: gql`{
      tests{
        name
        totalOptionsDecisions
        options {
          weight
          decisionCount
          displayCount
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
    &>div
      margin-top 10px
      .options
        margin auto
        width 50%
        justify-content center
        font-size 11px
        display flex
        .label
          &>div:first-child
            cursor pointer
            transition 0.2s
            border 1px solid grey
            margin 5px
            border-radius 25px
            padding 5px
            &:hover
              background-color rgba(0,0,0,0.3)
              color white
</style>
