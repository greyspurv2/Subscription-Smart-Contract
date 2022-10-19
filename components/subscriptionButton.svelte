<script>
    import {Web3} from 'svelte-web3';

    //fix route for subscriptionButton.svelte with correct path

    import HelloContract from "./contracts/Hello.json";
  

    //gut this part out and setup logic that fits with the subscription solididty scripts

    async function getGreeting() {
      const web3 = new Web3(window.ethereum)
      const networkId = await web3.eth.net.getId();
      const deployedNetwork = HelloContract.networks[networkId];
      const contract = new web3.eth.Contract(
        HelloContract.abi,
        deployedNetwork && deployedNetwork.address
      );
      const response = await contract.methods.greet().call();
      return response;
    }
  
    $: promise = getGreeting();
    let greetie = "Human.";
  
    async function setGreetie(greetie) {
      const web3 = new Web3(window.ethereum);
      const accounts = await web3.eth.getAccounts();
      const networkId = await web3.eth.net.getId();
      const deployedNetwork = HelloContract.networks[networkId];
      const contract = new web3.eth.Contract(
        HelloContract.abi,
        deployedNetwork && deployedNetwork.address
      );
      await contract.methods.setGreetie(greetie).send({from: accounts[0]});
      // refresh promise that fetches greeting from blockchain
      promise = getGreeting();
    }
  </script>
  
  {#await promise}
    <div class="">
      <h1>Subscribe</h1>
    </div>
  {:then greeting}
    <h1>{greeting}</h1>
    <input bind:value={greetie} />
    <button on:click={setGreetie(greetie)}>Submit</button>
  {/await}