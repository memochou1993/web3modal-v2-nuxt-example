<template>
  <div>
    <client-only>
      <w3m-core-button />
    </client-only>
  </div>
</template>

<script>
import { configureChains, createClient, getAccount } from '@wagmi/core'
import { goerli, mainnet } from '@wagmi/core/chains'
import { EthereumClient, modalConnectors, walletConnectProvider } from '@web3modal/ethereum'
import { Web3Modal } from '@web3modal/html'

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
  }
}
</script>
