<template>
  <div class="lesson-02">
    <Lesson v-bind:text="text" v-bind:code="code"
            :validate="validate"
            :modules="modules"
            :exercise="exercise"
            :concepts="concepts"
            lessonTitle="Create a new node that's linked to an old one">
    </Lesson>
  </div>
</template>

<script>
import Lesson from '../../components/Lesson'
import text from './02.md'
import concepts from './02-concepts.md'
import exercise from './02-exercise.md'
const CID = require('cids')

let code = `/* globals ipfs */

const run = async () => {
  let cid = await ipfs.dag.put({test: 1})
  // your code goes here
}

return run
`
const validate = async (result, ipfs) => {
  if (!result) {
    return {fail: 'You forgot to return a result :)'}
  }
  if (!CID.isCID(result)) {
    return {fail: 'Did not return a valid CID instance.'}
  }
  let hash = 'zdpuAoPanArLvuFtuvmLYuSvp8zE8wuKSMZUkMN8Y1PaHLvKP'
  if (result.toBaseEncodedString() === hash) {
    return {success: 'Everything works!'}
  } else {
    let obj = await ipfs.dag.get(result)
    let expected = JSON.stringify({bar: new CID(hash)})
    let got = JSON.stringify(obj.value)
    let fail = `Was expecting "${expected}" but got "${got}"`
    return {fail}
  }
}

let modules = {cids: require('cids')}

export default {
  components: {
    Lesson
  },
  data: () => {
    return {
      code, text, validate, modules, exercise, concepts
    }
  }
}
</script>
