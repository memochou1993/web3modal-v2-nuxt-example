<template>
  <div>
    <client-only>
      <w3m-core-button />
      <button @click="sign">
        Sign
      </button>
    </client-only>
  </div>
</template>

<script>
import Vue from 'vue'
import { configureChains, createClient, getAccount, signMessage } from '@wagmi/core'
import { goerli, mainnet } from '@wagmi/core/chains'
import { EthereumClient, modalConnectors, walletConnectProvider } from '@web3modal/ethereum'
import { Web3Modal } from '@web3modal/html'

Vue.config.ignoredElements = [
  'w3m-core-button'
]

export default {
  name: 'IndexPage',
  mounted () {
    // define constants
    const projectId = '5b71f5ba1a93e7ee227d6f2f023b946d'
    const chains = [mainnet, goerli]

    // configure wagmi client
    const { provider } = configureChains(chains, [walletConnectProvider({ projectId })])
    const wagmiClient = createClient({
      autoConnect: true,
      connectors: [
        ...modalConnectors({ appName: 'web3Modal', chains })
      ],
      provider
    })

    // create ethereum and modal clients
    const ethereumClient = new EthereumClient(wagmiClient, chains)
    const web3Modal = new Web3Modal(
      {
        projectId
      },
      ethereumClient
    )

    web3Modal.subscribeModal(({ open }) => {
      const account = getAccount()
      console.log(account)
    })
  },
  methods: {
    async sign () {
      const signature = await signMessage({
        message: 'Hello, World!'
      })
      console.log(signature)
    }
  }
}
</script>
