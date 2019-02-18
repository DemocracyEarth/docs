---
id: main
title: Democracy Earth
sidebar_label: Democracy Earth
---

This serves as a snapshot of the development efforts (`master` branch) as of February 2019 as we ramp up mainnet token deploy. 

# Sovereign

Sovereign is a governance tool for network communities. From the backend point of view, there are three kinds of users that can exist within the app, which maps to the three kinds of user creation and login methods that exist: Metamask, Blockstack, and email user. 

Metamask users must have the Metamask extension installed on the browser, which allows apps to interact with the Ethereum blockchain via the web3 API. On user creation, Sovereign reads the public address of the current Metamask account and uses Metamask to prove that the user indeed holds the private key of that account by performing a digital signature. Upon successful verification, Sovereign reads the current balances of ETH and associated ERC20 tokens and assigns them to the `profile.wallet` data structure for that user's database collection. One Metamask account may have many ERC20 tokens balances present, Sovereign will read only tokens that are part of the token.js library and assign them to the user’s `profile.wallet.reserves` array.

Blockstack users must have the Blockstack Browser installed on their machine, which allows accessing the user's Blockstack ID. On user creation and login, Sovereign hands over the process to Blockstack Browser where the user can grant permission. Sovereign then reads in basic info such as public address (currently a bitcoin address), Blockstack username, and user email (if present).

Email users follow the regular user creation process that normal web2 apps offer. There are currently two methods to create email users: with and without password. The former through the regular login menu (top right), the latter in the more recent hero section of the landing page (prompts user via email to set password later). Emails users have no public address associated with them.

Metamask users are able to vote, delegate, create new posts and comment on existing posts. Blockstack users can create new post and comment on existing ones, but can’t vote or delegate because a Stack token transaction is not possible yet. Email users can create new posts and comment on existing ones, and will not be able to vote or delegate as they hold no public address. All three kinds of users are able to coexist within the app. 



# VOTE token

The VOTE token is a standard ERC20 token deployed using Zeppelin tech (OpenZeppelin-eth, ZeppelinOS & EVM Packages). Specifically, it is an instance of the pre-deployed on-chain smart contract of the OpenZeppelin-eth library, namely `StandAloneERC20.sol`. 

VOTE token is built on top of Zeppelin tech for several reasons. The Zeppelin dev team has always pushed for a security-first mentality. On one hand, the OpenZeppelin library has withstood heavy use for more than 2 year and has proved to be a secure tool set of smart contracts. On the other hand, the ZeppelinOS platform offers smart contract upgradability via its underlying proxy system, a critical feature we believe is needed for iteration-driven development and natural bug fixing. The combination of the OpenZeppelin library with the ZeppelinOS platform results in the OpenZeppelin-eth EVM Package. An EVM Package is an on-chain piece of code (smart contracts) that are reusable, they allow us to insert ourselves in an ecosystem where we can reuse smart contracts as building blocks. The `StandAloneERC20.sol` is such an EVM package available in Rinkeby and mainnet. 

[pic]

Security, upgradability and an open source ecosystem of reusable smart contracts are the main reasons the VOTE token is built on top of Zeppelin tech. We are betting on open source code, remaining true to our open source nature. We are trusting we will continue strengthening our relationship with the Zeppelin team, looking to not only benefit from their platform but also contribute to it with possible EVM Packages that we can submit. 
Voting in Sovereign (or VOTE token in action)

Having the VOTE token built on top of Zeppelin tech allows us to further our development roadmap in what be thought of as a number of phases. 

For the sake of simplicity, I will refer to the place where we are now as phase one. This current phase originated in June 2018 after the NY retreat, and we are now almost at the very end of phase one. We expect to ship on mainnet in this Q1 of 2019, and among other things this writing should serve us as a way to organize and plan future phases. 

Phase one finds Sovereign as a multi-type user app where voting means Ethereum on-chain transactions (effectively ERC20 token transfers) for every vote and delegation. All posts in the app (with the exception of posts created by email users) have a public address associated with it: every time a user creates a new post, Sovereign will assign the public address of said user as the address of the post. Hence when voting on a post, we tap directly into the web3 library and perform a `sendTransaction` operation (with argument `from` as the address of the logged in user initiating the vote and argument `to` as the address of the post).

Future phases, then, can be thought of from the perspective of EVM Packages. This approach should allow us to design smart contracts that reference our VOTE token to perform desire actions. That means we don’t need to upgrade or modify the VOTE token deploy itself, we can append functionality to a separate smart contract that can be upgraded as needed. As a visualization example: 

```
pragma solidity ^0.4.24;
import "zos-lib/contracts/Initializable.sol";
import "openzeppelin-eth/contracts/token/ERC20/StandaloneERC20.sol";

contract Vote is Initializable {
   StandaloneERC20 public token;
  
   function initialize(StandaloneERC20 _token) public initializer {
       token = _token;
   }
}
```

The idea is that such smart contract (Vote.sol in this example) can serve as a blank canvas. As of today (February 6), we have only tested locally via the terminal that indeed Vote.sol can successfully reference variable `token` after initialization and get basic data such as `totalSupply`.

This approach can potentially open up a whole field of opportunities. The functionality we need in Sovereign regarding the use of the VOTE token (for example staking or some portion of the identity protocol) could be coded in smart contract that references it and therefore a smart contract we can interact with Sovereign via web3. 

As we prepare to deploy on mainnet, we must acknowledge the uncertainty that lies ahead regarding where we should allocate our development time and efforts. We believe the proposed approach offers a minimum of flexibility needed for us to be able to adapt to the fast-paced crypto environment. Although much remains to be tested and tried, we believe we have enough reason to move forward with this kind of approach
