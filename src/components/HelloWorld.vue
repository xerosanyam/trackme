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
          <button title="Increase count" @click="events[key].count++">+</button>
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
    <div class="builtBy">Built by <a href="https://twitter.com/xerosanyam">Sanyam Jain</a></div>
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
    var a = localStorage['events']
    if (a !== undefined) {
      vm.events = JSON.parse(a)
    }
    if (Object.keys(vm.events).length === 0) {
      vm.$set(vm.events, 'Drank water', {'type': 'positive', 'count': 0})
      vm.$set(vm.events, 'Checked Facebook/Whatsapp', {'type': 'negative', 'count': 0})
    }
  },
  methods: {
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
        vm.$set(vm.events, vm.newEvent, {'type': 'positive', 'count': 0})
      } else {
        vm.$set(vm.events, vm.newEvent, {'type': 'negative', 'count': 0})
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
        console.log(JSON.stringify(val))
        localStorage['events'] = JSON.stringify(val)
      },
      deep: true
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
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
table{
  width: 60%;
  margin: auto;
  margin-top:40px;
}
button{
  cursor: pointer;
}
td{
  padding: 10px;
}
button{
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
input{
  font-size: 20px;
  width:18%;
}
.emojis{
  font-size: 24px;
}
.small{
  padding:0.2em 1.1em;
}
.builtBy{
  position: fixed;
  bottom:5px;
  font-size: 10px;
}
</style>
