## Aavegotchi Decentralized App using React

In this tutorial, we will be building a DApp (decentralised app) that is connected to the Aavegotchi blockchain. The tutorial will be in React and Typescript, using Web3 and the Aavegotchi Subgraph.

### What is Aavegotchi

[Aavegotchi](https://www.aavegotchi.com/) is a crypto collectible game deployed on Polygon (the leading sidechain of Ethereum) where participants can purchase and grow Aavegotchis, Non-Fungible Token (NFT) avatars used to explore and interact with Aavegotchi’s digital universe. 

Each Aavegotchi avatar is a digital collectible that has unique attributes defined by a rarity score. The score is calculated by specific character traits (energy, aggression, spookiness, brain size, eye shape, and eye color), wearables attached to the avatar, a kinship score, which is based on how often the player interacts with the avatar, and the amount of Aave-powered aTokens staked to the avatar. 

The platform draws on nostalgia for the handheld digital pet game Tamagotchi, enriching its classic gameplay with blockchain-enabled functionality. Aavegotchi incorporates many popular aspects of crypto into one unified platform: decentralized finance (DeFi), blockchain gaming, staking and digital land ownership.

Aave derives its name from the Finnish word for “ghost,” and Aavegotchis are depicted by a cartoon ghost, hence the name for the associated token: GHST.

GHST is the primary utility token of the Aavegotchi ecosystem and is used to buy and sell goods within the Aavegotchi world, including Aavegotchis themselves. GHST can also be staked for additional rewards and can be transferred between users just like other cryptocurrencies.

### What is Subgraph

The [Subgraph](https://thegraph.com/hosted-service/subgraph/aavegotchi/aavegotchi-core-matic) is a GraphQL API built by the Pixelcraft Studios on [The Graph](https://thegraph.com/) that allows you to more efficiently get data from the Aavegotchi blockchain without having to worry about any Web3 setup. It is useful for viewing data from the contract, however, it has its limitations. You cannot call methods that require gas (like petting your Aavegotchi) and some data that you want may not be integrated into the subgraph yet.

### The Build

In this tutorial, we are going to build an Aavegotchi Pokédex that allows the user to search and view all of the summoned Aavegotchis. The end result will look something like this:

![Aavegotchi DApp](/public/images/build.jpg)




