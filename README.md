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

## Eigenlayer: Restaking

Diagram of Eigenlyer Restaking Flow

## AVS: Yield-streaming

Diagram of AVS Yield Computation

## Hodly: Yield-collecting

Diagram of Hodly Yield Collection

## User: Yield-claiming

Diagram of User Yield Claiming

# Implementation

## Eigenlayer Contracts

When the user deposits their ETH and delegates it to an AVS operator in a token pool, their address and amount is stored in the [Eigenlayer contract](https://www.blog.eigenlayer.xyz/ycie/): 

```
contract TokenManager {
    mapping(address => address) tokenPoolRegistry;
    mapping(address => mapping(address => uint256)) stakerPoolShares;
    
    function stakeToPool(address pool, uint256 amount);
    function withdrawFromPool(address pool);
}

contract TokenPool {
    uint256 public totalShares;
    function stake(uint256 amount) TokenManagerOnly;
    function withdraw(uint256 shares) TokenManagerOnly;
}

contract DelegationManager {
    // ...
    mapping(address => mapping(address => uint256)) operatorPoolShares;
    // ...
}
```

## AVS Reference Implementation

The AVS deposits the yield. The AVS is then able to compute the share of the yields to pay to each user and send it.  

```
contract YieldManager {
    uint256 public accumulatedYield; // Temporary balance of yield (points, tokens, fees)

    function depositYield(uint256 amount) public; // Deposit yield for accumulation

    function sendYield(address user, uint256 amount) onlyOperator; //AVS sends yield per user

    function batchSendYield(address[] memory users, uint256[] memory amounts) onlyOperator; //AVS batch sends yield
}
```

## Axelar EVM to IBC General Message Parsing

The AVS must have a deployed [Axelar Gateway contract](https://docs.axelar.dev/dev/cosmos-gmp) or similiar. 

The AVS calls the Gateway contract with the yield tokens, which then forwards it via Axelar to THORChain. 

```
bytes memory argValue = abi.encode(recipients); // A standard EVM payload
bytes memory payload  = abi.encode(
    "multi_send", // CosmWasm method name
    StringArray.fromArray1(["recipients"]), // argument name
    StringArray.fromArray1(["string[]"]), // argument type
    argValue // argument value
);
bytes memory payloadToCW = abi.encodePacked(
    bytes4(uint32(1)), // version number
    payload
);
function callContractWithToken(
    string memory destinationChain,
    string memory contractAddress,
    bytes memory payloadToCW,
    string memory symbol,
    uint256 amount
) external;
```

### Optional: AVS Runs the Relayer. 

The AVS can run an EVM<>IBC Relayer to skip Axelar and directly interact with THORChain. This allows to skip opting in to Axelar's security model. 

## Hodly WASM Contracts

Hodly receives the IBC payload and executes a [Swap](https://github.com/Team-Kujira/kujira-rs/blob/master/packages/kujira-fin/src/execute.rs)

```
ExecuteMsg
Swap {
        offer_asset: Option<Coin>,
        belief_price: Option<Decimal256>,
        max_spread: Option<Decimal256>,
        to: Option<Addr>,
        #[serde(skip_serializing_if = "Option::is_none")]
        callback: Option<CallbackData>,
    }
```

## THORChain Yield Accounts

## User Onchain Transactions

# Economics

## One-time Liquidity Auction

## Liquidity Mining








