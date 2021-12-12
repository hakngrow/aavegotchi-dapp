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

### Initial Setup

Make sure you have `node >= 10.16` and `npm >= 5.6` installed on your machine. You will also need an Ethereum compatible browser (if you are using Chrome or Firefox you will need to install the [Metamask](https://metamask.io/) browser extension) connected to the Matic Network.  For instructions on how to setup Metamask on the Matic Network, click [here](https://docs.polygon.technology/docs/develop/metamask/config-polygon-on-metamask/).

Now that is done, let us create our React app. To do this, open up your terminal, and run the following line:

```
npx create-react-app aavegotchi-dapp --template typescript
```

This will build a React app inside a newly created directory `aavegotchi-dapp`. Using `--template typescript` configures the React app to use Typescript.

After the installation is done, in your terminal run:
```
cd aavegotchi-dapp
npm start
```

This should automatically open up your browser and you should see the default React app running at `localhost:3000`. If not open up your browser and manually enter `http://localhost:3000/` in the address bar.

![Default React App](/public/images/default.jpg)

In the code editor of choice (I personally use [Visual Studio Code](https://code.visualstudio.com/)), open up the `aavegotchi-dapp` folder.

In `src/App.tsx` replace all the code with the following and save:
```
//App.tsx

import { useEffect } from 'react';
import './App.css';

function App() {

 const fetchGotchis = () => {
   console.log('Blank page');
 }

 useEffect(() => {
   fetchGotchis();
 }, [])

 return (
   <div className="App">
   </div>
 );
}

export default App;
```

Your app refreshes and should now be blank. However, if you right-click and `Inspect` the webpage, you should see `Blank page` in the console.

When the `App` component is rendered, the `useEffect` hook is triggered, which in turn triggers the `fetchGotchis` function. In the `fetchGotchis` function, we will put our logic to fetch the Aavegotchi data from the blockchain.

### Using the Aavegotchi Subgraph

We can use the Aavegotchi Subgraph to pull in our list of Aavegotchi's data. You can open up the Subgraph playground [here](https://thegraph.com/explorer/subgraph/aavegotchi/aavegotchi-core-matic) to get your graphQL query tested before writing any code.

![Aavegotchi Subgraph](/public/images/subgraph.jpg)

On the right, there is a Schema that allows you to visualise the data we can fetch.  Based on the Scehma, we can decide what data we want to be returned from the query. 

For our Aavegotchi DApp, we want the:

- name
- id
- collateral
- numeric traits

![Aavegotchi Subgraph - Query](/public/images/subgraph-query.jpg)

### Using the Subgraph Query in React

To use the subgraph query, we need to install 2 modules, `graphQL` and `graphql-request` as our graphQL client. 

Open up a new terminal, and inside your `aavegotchi-dapp` directory run:
```
npm install graphql-request graphql
```

In `App.tsx`, enter the following new lines of code:
```
//App.tsx

import { useEffect } from 'react';
import { request } from "graphql-request"; // <-- New line
import './App.css';

const uri = 'https://api.thegraph.com/subgraphs/name/aavegotchi/aavegotchi-core-matic';

function App() {

 const fetchGotchis = async () => {
   const query = `
   {
     aavegotchis(first: 100, orderBy: gotchiId) {
       id
       name
       collateral
       withSetsNumericTraits
     }
   }`;
    
   const response = await request(uri, query);
   console.log(response);
  }
...
```

If you are getting a Typescript runtime error, run `npm install @types/react --dev`.

On refreshing, you should now see the Aavegotchi data logged in your console.

The imported `request` function requires 2 arguments, the target URL and the query. We get the URL from the [Aavegotchi Subgraph](https://thegraph.com/hosted-service/subgraph/aavegotchi/aavegotchi-core-matic) under Queries (HTTP), this tells the GraphQL request where to target.

We convert the query we tested earlier into a string. We then asynchronously waited for the response to return and logged it in the console.

Now that we know the GraphQL `request` works, we need to store it in the `App` component state so we can display it in the UI. For this, we use the `useState` React hook. However, because we are using Typescript we need to first set up our interface.

Create a new folder under `src` called `types` and inside create an `index.ts` file. In `src/types/index.ts` put in the following code:
```
// types/index.ts

export interface Gotchi {
 collateral: string;
 id: string;
 name: string;
 withSetsNumericTraits: Array<Number>;
}

export interface QueryResponse {
 aavegotchis: Array<Gotchi>
}
```

Now at the top of `App.tsx` import our types and the `useState` hook from React, and edit the `fetchGotchis` function to store the response in the state:
```
//App.tsx

import { useEffect, useState } from 'react';
import { Gotchi, QueryResponse } from './types';
...

function App() {
  const [ gotchis, setGotchis ] = useState<Array<Gotchi>>([]);

  const fetchGotchis = async () => {
    const query = `
      {
        aavegotchis(first: 100, orderBy: gotchiId) {
          id
          name
          collateral
          withSetsNumericTraits
        }
      }
    `
    const response = await request<QueryResponse>(uri, query);
    setGotchis(response.aavegotchis)
  }
  ...
}
```

Now that we have stored the data, we can map it onto the screen.
```
//App.tsx

return (
  <div className="App">
    {gotchis.map((gotchi, i) => {
      return (
        <p key={i}>{gotchi.name}</p>
      )
    })}
  </div>
);
```

On refresh, we should now see a list of names on the screen.

![Aavegotchi Names](/public/images/names.jpg)

Next we are going to create a new component for the Aavegotchi listing, that allows you to select an Aavegotchi.

### Styling our App

In `App.tsx`, replace the returned `JSX` with the following code:
```
//App.tsx

return (
  <div className="App">
    <div className="container">
      <div className="selected-container">
      </div>
      <div className="gotchi-list">
      </div>
    </div>
  </div>
);
```

In `App.css`, replace the CSS with:
```
.App {
 display: block;
 text-align: center;
 height: 100vh;
 background-color: #FA34F3;
 box-sizing: border-box;
}

.container {
 display: grid;
 grid-template-rows: 50% 50%;
 box-sizing: border-box;
 height: 100%;
 width: 100%;
}

.gotchi-list {
 background-color: white;
 border-left: 5px solid black;
 border-right: 5px solid black;
 height: 100%;
 overflow-x: hidden;
 overflow-y: scroll;
 box-sizing: border-box;
}

@media (min-width: 768px) {
 .container {
   max-width: 1300px;
   margin: 0 auto;
   grid-template-columns: 1fr 1fr;
   grid-template-rows: revert;
 }
 .selected-container {
   box-sizing: border-box;
   padding: 16px;
   height: 100%;
 }
}
```

### Creating UI Components

We want to create a new component for the Aavegotchi listing as well as for the selected Aavegotchi.

So within `src` create a new folder called `components`.  

In `components`, create 2 more folders called `GotchiListing` and `SelectedGotchi` 

Within both, create an `index.tsx` and a `styles.css` file.

Your folder structure should now look like this:

![UI Components Folders](/public/images/components-folders.jpg)

### Render the Aavegotchi Listing

In `GotchiListing/index.tsx`, enter the following content:
```
// GotchiListing/index.tsx

import "./styles.css"

interface Props {
  id: string;
  name: string;
  collateralColor: string;
  selected: boolean;
  selectGotchi: () => void;
}

export const GotchiListing = ({ id, name, collateralColor, selected, selectGotchi }: Props) => {
 return (
   <div className={`gotchi-listing ${selected && 'selected'}`} onClick={() => selectGotchi()}>
     <div className="collateral-container">
       <div className="collateral" style={{ backgroundColor: collateralColor }} />
       </div>
     <p className="id">{id}</p>
     <p className="name">{name}</p>
   </div>
 )
}
```

The `interface` tells the editor that the `GotchiListing` component expects the following properties:

`name` - Name of the Aavegotchi
`id` - Id of the Aavegotchi
`collateralColor` - Primary color of the collateral (more on this later)
`selected` - boolean of whether the item is selected or not
`selectGotchi` - Function that passes the click event to the parent

For styling, enter in `GotchiListing/styles.css`:
```
.gotchi-listing {
  display: flex;
  cursor: pointer;
}
.gotchi-listing.selected,
.gotchi-listing:hover {
  background-color: #fffa65;
}

.collateral-container {
  width: 54px;
  display: grid;
  place-items: center;
}

.collateral {
  width: 32px;
  height: 32px;
  border-radius: 50%;
}

.id {
  padding-right: 12px;
  width: 60px;
  text-align: right;
}

.name {
  text-transform: uppercase;
  white-space: nowrap;
  text-overflow: ellipsis;
  overflow: hidden;
}
```

Now inside `App.tsx`, import in and render the new `GotchiListing` component.

At the top,  with the other imports enter:
```
//App.tsx

import { GotchiListing } from './components/GotchiListing';
```

And inside the `<div>` with a `className` of `gotchi-list`, map out the `<GotchiListing/>` component for each Aavegotchi stored in the state:
```
//App.tsx

<div className="gotchi-list">
  {
    gotchis.map((gotchi, i) => (
      <GotchiListing
        key={gotchi.id}
        id={gotchi.id}
        name={gotchi.name}
        collateralColor="black"
        selectGotchi={() => null}
        selected={false}
      />
    ))
  }
</div>
```

>If you are getting a run time error, in `SelectedGotchi/index.tsx` type and save `export {}` as a temporary fix.

At this point, you will be able to scroll through a list of Aavegotchis.

![Gotchi Listing component](/public/images/components-GotchiListing.jpg)

We are not passing the `gotchi.collateral` to the `collateralColor` parameter. That's because the `collateral` is not a hex code, but a unique ID for the collateral. We will use `Web3` later to call the Aavegotchi contract to receive the corresponding color.

### Render the Selected Aavegotchi

We create another state within `App.tsx` for the selected Aavegotchi's index:
```
//App.tsx

const [ selectedGotchi, setSelectedGotchi ] = useState<number>(0);
```

When we click on a Aavegotchi on the listing, we want to set the index position of the selected Aavegotchi in the state. And then we can use the saved index to check whether a listed Aavegotchi is selected:
```
//App.tsx

<GotchiListing
  ...
  selectGotchi={() => setSelectedGotchi(i)}
  selected={i === selectedGotchi}
/>
```

When you click a Aavegotchi on the listing you should now see the listing is highlighted.

Now let us display the selection in a new `SelectedGotchi` component. In `SelectedGotchi/index.tsx`, enter in the following code:
```
// SelectedGotchi/index.tsx

import './styles.css'

interface Props {
 name: string;
 traits: Array<Number>;
}

export const SelectedGotchi = ({ name, traits }: Props) => {
  return (
    <div className="selected-gotchi-container">
      <div className="name-container">
        <h2>{name}</h2>
      </div>
      <div className="svg-container" />
      <div className="traits-container">
        <div className="trait">
          <p>⚡ Energy</p>
          <p>{traits[0]}</p>
        </div>
        <div className="trait">
          <p>👹 Aggression</p>
          <p>{traits[1]}</p>
        </div>
        <div className="trait">
          <p>👻 Spookiness</p>
          <p>{traits[2]}</p>
        </div>
        <div className="trait">
          <p>🧠 Brain size</p>
          <p>{traits[3]}</p>
        </div>
        <div className="trait">
          <p>👀 Eye shape</p>
          <p>{traits[4]}</p>
        </div>
        <div className="trait">
          <p>👁 Eye color</p>
          <p>{traits[5]}</p>
        </div>
      </div>
    </div>
  )
}
```





