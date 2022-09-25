# dApp-Boilerplate

## What is this?

This repository is a boilerplate for building dApps on the Effect Network. It is a Vue.js app that uses the Effect Network JavaScript SDK to interact with the Effect Network.

There are two folders in this repository:

1. [Effect-Network-dApp](./)
2. [Effect-Force-Template](./Effect-Force-Template/)

The first folder, the one you are in right now; is a basic example web application built with Vue that uses the Effect Network JavaScript SDK to interact with the Effect Network. They are called Effect Network dApps; they allow users to create and post tasks on the Effect Force.
The second folder is an example campaign [template](https://developer.effect.network/effect_network/template.html) built with Vue for Effect Force. The contents of `index.html` are created with the (effect-js)(https://github.com/effectai/effect-js) library or created with [Effect-Force](https://app.effect.network/campaigns/new).

## Medium Article

We published a medium article that goes a bit more in-depth on how to launch a dApp on the Effect Network. You can read it here: [How to Build a dApp on the Effect Network](https://medium.com/effect-ai/launch-your-dapp-on-effect-network-eece1ba221f6)

### Prerequisites

- Node.js (v16.0.0 or higher)
- Vue knowledge
- EFX (testnet) Tokens
- NVM (optional)
- EOSIO Account (optional)
- BSC Account (optional)

## Getting Started

The easiest way to get started is by cloning this repository.

```bash
git clone http://github.com/effectai/dApp-Boilerplate
```

`cd` into the Effect-Network-dApp folder and run the following commands to get started:

```bash
npm install # install dependencies
npm run dev # run the app in development mode
```

You can navigate to [http://localhost:3000](http://localhost:3000) to see the app running.
Afterward, `cd` into the Effect-Force-Template folder, open the `index.html` file and copy-paste the contents into the [Effect Force campaign template editor](http://testnet.effect.network/campaigns/new).
Note that this link will take you to the Testnet environment of the Effect Network. You can also use the [Effect Force campaign template editor](http://app.effect.network/campaigns/new) to create a campaign on the Mainnet.

### Testnet EFX Faucet
To get testnet tokens, you must join our Discord Server and request them in the #faucet channel. You can join our Discord Server [here](https://discord.gg/effectnetwork).
Run the following command in the channel to get your testnet EFX tokens:

```
!get_tokens <your-account-name>
```
Your account name can be found on your profile page on the [Effect Force](https://testnet.effect.network/profile) website.

## Resources

- [Effect Network Documentation](https://developer.effect.network/)
- [Vue.js Documentation](https://vuejs.org/v3/guide/)
- [Effect Network JavaScript SDK Reference](https://effectai.github.io/effect-js/)
- [Launch you dApp on Effect Network](https://medium.com/effect-ai/launch-your-dapp-on-effect-network-eece1ba221f6)
- [Effect Force (Mainnet)](https://app.effect.network/)
- [Effect Force (Testnet)](https://testnet.effect.network/)
- [Join our DAO](http://dao.effect.network)
- [Join our Discord Server](https://discord.gg/effectnetwork)
