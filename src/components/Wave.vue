<template>
  <div class="mainContainer">

      <div class="dataContainer">
        <div class="header">
        👋 Hey there!
        </div>

        <div class="bio">
        I am Moses, a year ago I was working as a gardener. Today I work as a frontend engineer while learning how to do smart contracts. <br> Technology it's amazing! 
        If you want, connect your Ethereum wallet and wave at me!
        </div>

        <button class="waveButton" @click="wave">
          Wave at Me
        </button>

        <button class="waveButton" @click="connectWallet">
          Connect Wallet
        </button>

        <div v-if="allWaves.length">
          <div class="waves" v-for="(wave, index) in allWaves" :key="index">
            <span>Address: {{ wave.address }}</span>
            <span>Time: {{ wave.timestamp.toString() }}</span>
            <span>Message: {{ wave.message }}</span>
          </div>
        </div>
      </div>
    </div>
</template>

<script>
import { ethers } from 'ethers'
import abi from '@/utils/WavePortal.json'

export default {
  name: 'Wave',
  data() {
    return {
      contractAddress: '0x0e3E88d42ed1daf8b50Dd8F0bE5C3149Aae50aA5',
      contractABI: abi.abi,
      allWaves: []
    }
  },
  async mounted() {
    this.checkIfWalletIsConnected();
  },
  methods: {
    async checkIfWalletIsConnected() {
      try {
        const { ethereum } = window;
  
        if (!ethereum) {
        console.log("Make sure you have metamask!");
        return;
        } else {
          console.log("We have the ethereum object", ethereum);
        }
        const accounts = await ethereum.request({ method: 'eth_accounts' });

        if (accounts.length !== 0) {
          const account = accounts[0];
          console.log("Found an authorized account:", account);
          this.getAllWaves();
        } else {
          console.log("No authorized account found")
        }
        
      } catch (error) {
        console.log(error);
      }
    },
    async connectWallet() {
      try {
        const { ethereum } = window;

        if (!ethereum) {
          alert("Get MetaMask!");
          return;
        }

        const accounts = await ethereum.request({ method: "eth_requestAccounts" });

        console.log("Connected", accounts[0]);

      } catch (error) {
        console.log(error)
      }
    },
    async wave() {
      try {
        const { ethereum } = window;

        if (ethereum) {
          const provider = new ethers.providers.Web3Provider(ethereum);
          const signer = provider.getSigner();

          const wavePortalContract = new ethers.Contract(this.contractAddress, this.contractABI, signer);

          let count = await wavePortalContract.getTotalWaves();
          console.log("Retrieved total wave count...", count.toNumber());

          const waveTxn = await wavePortalContract.wave("This is a test message :)");
          console.log("Mining...", waveTxn.hash);

          await waveTxn.wait();
          console.log("Mined -- ", waveTxn.hash);

          count = await wavePortalContract.getTotalWaves();
          console.log("Retrieved total wave count...", count.toNumber());
        } else {
          console.log("Ethereum object doesn't exist!");
        }
      } catch (error) {
        console.log(error)
      }
    },
    async getAllWaves() {
      try {
        const { ethereum } = window;
        if ( ethereum ) {
          const provider = new ethers.providers.Web3Provider(ethereum);
          const signer = provider.getSigner();
          const wavePortalContract = new ethers.Contract(this.contractAddress, this.contractABI, signer);

          const waves = await wavePortalContract.getAllWaves();

          let wavesCleaned = [];
          waves.map(wave => {
            wavesCleaned.push({
              address: wave.waver,
              timestamp: new Date(wave.timestamp * 1000),
              message: wave.message
            });
          });

          this.allWaves = wavesCleaned;

        } else {
          console.log("Ethereum object doesn't exist!");
        }
      } catch (error) {
          console.error(error);
      }
    }
  }
}
</script>

<style scoped>
.mainContainer {
  display: flex;
  justify-content: center;
  width: 100%;
  margin-top: 64px;
}

.dataContainer {
  display: flex;
  flex-direction: column;
  justify-content: center;
  max-width: 600px;
}

.header {
  text-align: center;
  font-size: 32px;
  font-weight: 600;
}

.bio {
  text-align: center;
  color: gray;
  margin-top: 16px;
}

.waveButton {
  cursor: pointer;
  margin-top: 16px;
  padding: 8px;
  border: 0;
  border-radius: 5px;
  
}

.waves {
  background-color: "OldLace";
  margin-top: 1rem;
  padding: 8px;
}

</style>
