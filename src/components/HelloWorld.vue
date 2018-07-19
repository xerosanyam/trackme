<template>
  <div>
    <div v-if="step===1">
      <h3>What do you want to track?</h3>
      <input type="text" v-model="newEvent" @keyup.enter="goToStep2"/>
      <button type="submit" @click="goToStep2">Add</button>
    </div>
    <div v-else-if="step===2">
      <h3>Is it good for your health ?</h3>
      <button type="submit" @click="addEvent(1)">Yes</button>
      <button type="submit" @click="addEvent(-1)">No</button>
    </div>
    <table>
      <tr>
        <th>&nbsp;</th>
        <th>Event</th>
        <th>Count</th>
        <th></th>
      </tr>
      <tr :key="key" v-for="(event,key)  in events">
        <td>
          <button class="small" title="Delete this event" @click="deleteEvent(key)">x</button>
          <button v-show="event.count>0" class="small" title="Decrease count" @click="decreaseCount(key)">-</button>
        </td>
        <td>{{key}}</td>
        <td>{{event.count}}</td>
        <td>
          <button title="Increase count" @click="increaseCount(key)">+</button>
        </td>
        <td class="emojis">
          <div v-if="event.type==='positive'">
            <span v-if="event.count<1">🙄</span>
            <span v-else-if="event.count<2">🙂</span>
            <span v-else-if="event.count<4">👌</span>
            <span v-else-if="event.count<6">😎</span>
            <span v-else-if="event.count<8">😍</span>
            <span v-else-if="event.count<10">👏</span>
            <span v-else-if="event.count<15">😘</span>
            <span v-else-if="event.count<25">🤗</span>
            <span v-else-if="event.count<40">🌈</span>
            <span v-else>🏆🤟💋</span>
          </div>
          <div v-else>
            <span v-if="event.count<2">😀</span>
            <span v-else-if="event.count<4">😅</span>
            <span v-else-if="event.count<6">😐</span>
            <span v-else-if="event.count<8">🤨</span>
            <span v-else-if="event.count<10">😠</span>
            <span v-else-if="event.count<15">🐖</span>
            <span v-else-if="event.count<25">💩</span>
            <span v-else-if="event.count<40">💀</span>
            <span v-else>🙏</span>
          </div>
        </td>
      </tr>
    </table>
    <div class="builtBy">
      version: 1.2.1
      <br>
      Built by <a href="https://twitter.com/xerosanyam">Sanyam Jain</a>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      newEvent: '',
      events: {},
      step: 1
    }
  },
  mounted () {
    var vm = this
    vm.loadEvents()
    vm.checkForNewDay()
    document.addEventListener('visibilitychange', vm.visibilityChange)
  },
  methods: {
    loadEvents () {
      var vm = this
      var a = localStorage['events']
      if (a !== undefined) {
        vm.events = JSON.parse(a)
      }
      if (Object.keys(vm.events).length === 0) {
        vm.$set(vm.events, 'Drank water', {
          'type': 'positive',
          'count': 0
        })
        vm.$set(vm.events, 'Checked Facebook/Whatsapp', {
          'type': 'negative',
          'count': 0
        })
      }
    },
    visibilityChange () {
      var vm = this
      // on visible
      if (!document.hidden) {
        vm.loadEvents()
        vm.checkForNewDay()
      }
    },
    checkForNewDay () {
      var vm = this
      var temp1 = localStorage['updated']
      // If no updated record
      if (temp1 === undefined) {
        localStorage['updated'] = new Date().toDateString()
      } else {
        var date1 = new Date(temp1)
        var temp2 = new Date().toDateString()
        var date2 = new Date(temp2)
        if (date1 < date2) {
          // If new Day
          vm.calculateScore()
          vm.resetCount()
          console.log('Needs to Reset Count')
          localStorage['updated'] = temp2
          // vm.resetCount()
        }
      }
    },
    calculateScore () {
      var vm = this
      var good = 0
      var bad = 0
      var score = 5
      for (var i in vm.events) {
        if (vm.events[i].type === 'positive') {
          good += vm.events[i].count
        } else {
          bad += vm.events[i].count
        }
      }
      if (good > 10) {
        good = 10
      }
      if (bad > 10) {
        bad = 10
      }
      score += (0.5 * good - 0.5 * bad)
      vm.showScore(score)
      // alert('Score is ' + score)
      // console.log('good is', good)
      // console.log('bad is', bad)
      // console.log('score is', score)
    },
    showScore (score) {
      score = score / 2
      score = Math.ceil(score)
      alert(`Yesterday's Score: ` + score + '/5')
      // if (score >= 9) {
      //   alert('You are a beautiful human. Stay as you are 😘\n\nScore: ' + score + '/10')
      // } else if (score >= 7) {
      //   alert('Almost there!\nHope to see you perform even better today! 🤗\n\nScore: ' + score + '/10')
      // } else if (score >= 5) {
      //   alert('Your performance has been okayish. \n\nScore: ' + score + '/10')
      // } else if (score >= 2) {
      //   alert('It is great that you are tracking your habits.\nBut you certainly can perform better\n\nScore: ' + score + '/10')
      // } else {
      //   alert(`Harming your body isn't good.\nPlease discuss your negative habits with somebody close and try to be more self aware.\n\nScore: ` + score + `/10`)
      // }
    },
    resetCount () {
      var vm = this
      for (var i in vm.events) {
        vm.$set(vm.events[i], 'count', 0)
      }
    },
    goToStep2 () {
      var vm = this
      if (vm.newEvent === '') {
        alert('Add some description of event first')
        return
      }
      if (vm.newEvent in vm.events) {
        alert('Event already exists')
        return
      }
      vm.step = 2
    },
    addEvent (type) {
      var vm = this
      if (vm.newEvent === '') {
        alert('Add some description of event first')
        return
      }
      if (vm.newEvent in vm.events) {
        alert('Event already exists')
        return
      }
      if (type === 1) {
        vm.$set(vm.events, vm.newEvent, {
          'type': 'positive',
          'count': 0
        })
      } else {
        vm.$set(vm.events, vm.newEvent, {
          'type': 'negative',
          'count': 0
        })
      }
      vm.newEvent = ''
      vm.step = 1
    },
    deleteEvent (key) {
      var vm = this
      var a = confirm('Are you sure you want to delete the event ' + key + ' ?')
      if (a) {
        vm.$delete(vm.events, key)
      }
    },
    decreaseCount (key) {
      var vm = this
      var a = confirm('Are you sure you want to decrease the count for ' + key + ' ?')
      if (a) {
        vm.events[key].count--
        if (vm.events[key].count < 0) {
          vm.events[key].count = 0
        }
      }
    },
    increaseCount (index) {
      var vm = this
      vm.events[index].count++
    }
  },
  watch: {
    events: {
      handler (val) {
        var vm = this
        console.log(JSON.stringify(val))
        localStorage['events'] = JSON.stringify(val)
        vm.checkForNewDay()
      },
      deep: true
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1,
h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

table {
  width: 60%;
  margin: auto;
  margin-top: 40px;
}

button {
  cursor: pointer;
}

td {
  padding: 10px;
}

button {
  /* background-color: #2979FF; */
  padding: 0.6em 1.7em;
  vertical-align: middle;
    margin: 0 1em 0 0;
    border-radius: 0.2em;
    box-sizing: border-box;
    text-decoration: none;
    /* color: #FFFFFF; */
    /* box-shadow: inset 0 -0.6em 1em -0.35em rgba(0,0,0,0.17), inset 0 0.6em 2em -0.3em rgba(255,255,255,0.15), inset 0 0 0em 0.05em rgba(255,255,255,0.12); */
    text-align: center;
    border: none;
}

input {
  font-size: 20px;
  width: 18%;
}

.emojis {
  font-size: 24px;
}

.small {
  padding: 0.2em 1.1em;
}

.builtBy {
  position: fixed;
  bottom: 5px;
  font-size: 10px;
  text-align: left;
}
</style>
