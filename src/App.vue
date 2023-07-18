<template>
  <div id="app">
    {{ ipv6Array }}
  </div>
</template>

<script>

export default {
  name: 'App',
  data() {
    return {
      ipv6Array: [],
    }
  },
  created() {
    this.ipv6FromStringToArray('11234:abcd:0000:0000:0102:0000:0000:fffe')
  },
  methods: {
    ipv6FromStringToArray(ipvString) {
      this.checkForValidIpv6(ipvString)
    },
    checkForValidIpv6(ipvString) {
      let groups = ipvString.split(':');
      let fullGroupOfZeros = 0;
      let byteArray = [];

      for (let index = 0; index < groups.length; index++) {
        const element = groups[index];
        let isHexidecimal = this.checkIfHexidecimal(element);
        if(element.length == 0) {
          fullGroupOfZeros++;
          if(fullGroupOfZeros>1) {
            console.log('full group');
            this.ipv6Array = ['not valid']
            return;
          }
          byteArray = [...byteArray, ...[0, 0, 0, 0]];
        } else if(!isHexidecimal) {
          console.log('not Hex');
          this.ipv6Array = ['not valid'];
          return;
        }
        // array or two values converted hexidecimal value
        let hexConversion = this.findByteValue(element)
        byteArray = [...byteArray, ...hexConversion];
      }

      if(byteArray.length < 16) {
        for (let index = 0; index < byteArray.length%16; index++) {
          byteArray.push(0);
        }
      }

      this.ipv6Array = byteArray;
    },
    checkIfHexidecimal(num) {
      let regex = /[0-9A-Fa-f]+$/g
      return regex.test(num);
    },
    findByteValue(element) {
      let bytes = [];
      if(element === '0') {
        return [0, 0];
      }
      for (let c = 0; c < element.length; c += 2)
        bytes.push(parseInt(element.substr(c, 2), 16));
      return bytes;
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
