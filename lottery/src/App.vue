<template>
  <div id="app">
    <img src="./assets/logo.png">
    <h1>Lottery Contract</h1>
    <p>This contract is managed by: {{manager}}</p>
    <p>There are currently {{players.length}} people entered, competing to win {{ toEther(balance) }} ether!</p>
    <hr>

    <!-- <form> -->
      <h4>Want to try your luck?</h4>
      <div>
        <label>Amount of Ether to enter</label>
        <input v-model="value">
      </div>
      <button @click="submit">Enter</button>

    <hr>
      <h4>Ready to pick a winner?</h4>
      <button @click="pickWinner">Pick a winner</button>
    <hr>

    <h1>{{message}}</h1>

  </div>
</template>

<script>
import web3 from "./web3";
import lottery from "./lottery";

export default {
  name: "app",
  data() {
    return {
      manager: "",
      players: [],
      balance: 1,
      value: 0,
      message: ""
    };
  },
  created: function() {
    // Load data from contract
    lottery.methods.manager().call().then(manager => (this.manager = manager));
    lottery.methods.getPlayers().call().then(players => (this.players = players));
    web3.eth.getBalance(lottery.options.address).then(balance => this.balance = balance)
  },
  methods: {
    submit(event) {
      event.preventDefault();

      this.message = "Waiting on transaction success...";
      
      web3.eth.getAccounts()
        .then(accounts => lottery.methods.enter().send({ from: accounts[0], value: this.toWei(this.value) }))
        .then(() => this.message = "You have been entered")
        .catch(() => this.message = "Your transaction was denied")

    },
    pickWinner(event){
      event.preventDefault();
      
      this.message = "Waiting on transaction success...";
      
      web3.eth.getAccounts()
        .then(accounts => lottery.methods.pickWinner().send({ from: accounts[0] }))
        .then(() => this.message = "Winner has been picked")
        
    },
    toEther: amount => amount / 1000000000000000000 || 0,
    toWei: amount => amount * 1000000000000000000 || 0
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

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
</style>
