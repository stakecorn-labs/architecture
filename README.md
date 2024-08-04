# Technical Architecture
📜 Technical Architecture of Hodly


# Overview

This document serves as a comprehensive guide to understanding the underlying design and structure of Hodly.
It is intended to provide a clear and detailed description of the various components, systems, and technologies that make up our solution.
This document will be an essential resource for developers, operators, and anyone involved in the project, offering insight into the Hodly's architecture and how all the pieces fit together. Please feel free to reach out to the team if you have any questions or concerns.

## What is Hodly?

Hodly offers a gas-efficient yield-collecting solution for the [Eigenlayer](https://eigenlayer.xyz/) ecosystem, and it will initially be deployed on [Thorchain](https://thorchain.org/) which has native ETH liquidity. Users can re-stake ETH with Eigenlayer and delegate their security to an app-chain (Active Validator Set or AVS). The AVS can stream yield back to the user as native ETH using Hodly, where they can view their accumulated yield, claim it, or even have it auto-streamed to their address on a gas-efficient interval. 

## How does it work?

Eigenlayer parses the re-staked amount and user address and forwards this to the AVS. The AVS can compute the user's share of the yield in points, tokens or fees, then send it via IBC to THORChain on some interval. THORChain is able to swap to native ETH and hold in yield collector module with the owner set to the User's L1 address. The User can then view their yield and claim it any time. Additionally, the user may opt-in for auto-streaming, which causes THORChain to stream out the entire balance on a gas-efficient interval. 

## Terms:

- `AVS` Active Validator Set for the app-chain
- `IBC` Inter-Blockchain Communication protocol
- `LST` Liquid Staking Token
- `LSP` Liquid Staking Protocol

# Diagrams

The following diagrams for each process provide a visual representation of Hodly's data flow. 

## Eigenlayer: Staking, Restaking

## Hodly: Yield-collection








