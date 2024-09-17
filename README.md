# DeFi Kingdom Clone on Avalanche - README

## Project Overview

Welcome to the **DeFi Kingdom Clone on Avalanche** project! This project brings together the thrilling world of blockchain-based gaming 🕹️ with decentralized finance (DeFi) concepts, allowing players to collect, build, and battle with their digital assets in a custom-built DeFi Kingdom. The core functionalities include battling, exploring, and trading, with players earning rewards in the form of tokens 🪙. The game is deployed on a custom **EVM subnet** using **Avalanche** for faster transactions and lower fees.

## Table of Contents

- [Project Overview](#project-overview)
- [Features](#features)
- [Getting Started](#getting-started)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
  - [Deploying Your Custom Subnet](#deploying-your-custom-subnet)
  - [Deploying Smart Contracts](#deploying-smart-contracts)
    - [ERC20 Contract](#erc20-contract)
    - [Vault Contract](#vault-contract)
- [Acknowledgement](#Acknowledgement)

## Features

- **EVM Subnet Setup**: Leverage the Avalanche network to create a custom EVM subnet, enabling lower fees and fast transactions.
- **In-game Currency**: Deploy your own native token to be used as in-game currency for purchasing and trading assets.
- **Metamask Integration**: Connect your custom EVM subnet to Metamask to allow players to manage their assets.
- **Smart Contracts for Game Activities**: Use Solidity to deploy foundational smart contracts that define game activities such as battling, exploring, and trading.
- **Vault Mechanism**: Implement a Vault contract for secure token deposits and withdrawals.

## Getting Started

### Prerequisites

To build and run this project, you'll need the following:

- Node.js (v14 or later)
- Truffle or Hardhat for deploying contracts
- Avalanche Subnet
- Solidity (for smart contract development)
- Metamask (for connecting to your custom subnet)
- Remix

### Installation

1. Clone this repository:
   ```bash
   git clone https://github.com/yourusername/defi-kingdom-clone-avalanche.git
   ```
2. Navigate to the project directory:
   ```bash
   cd defi-kingdom-clone-avalanche
   ```
3. Install the required dependencies:
   ```bash
   npm install
   ```

### Deploying Your Custom Subnet

1. Follow the official Avalanche documentation to set up a custom EVM subnet:
   - [Avalanche Subnet Guide](https://docs.avax.network/learn/subnets)
   
2. Define your own native currency to be used in the game.

3. Connect your custom subnet to Metamask:
   - Add the custom subnet network to Metamask using the RPC URL and Chain ID.

### Deploying Smart Contracts

#### ERC20 Contract

The ERC20 contract defines your native in-game currency. Here is a basic ERC20 contract example:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

contract ERC20 {
    uint public totalSupply;
    mapping(address => uint) public balanceOf;
    mapping(address => mapping(address => uint)) public allowance;
    string public name = "GameToken";
    string public symbol = "GTK";
    uint8 public decimals = 18;

    event Transfer(address indexed from, address indexed to, uint value);
    event Approval(address indexed owner, address indexed spender, uint value);

    function transfer(address recipient, uint amount) external returns (bool) {
        balanceOf[msg.sender] -= amount;
        balanceOf[recipient] += amount;
        emit Transfer(msg.sender, recipient, amount);
        return true;
    }

    function approve(address spender, uint amount) external returns (bool) {
        allowance[msg.sender][spender] = amount;
        emit Approval(msg.sender, spender, amount);
        return true;
    }

    function transferFrom(
        address sender,
        address recipient,
        uint amount
    ) external returns (bool) {
        allowance[sender][msg.sender] -= amount;
        balanceOf[sender] -= amount;
        balanceOf[recipient] += amount;
        emit Transfer(sender, recipient, amount);
        return true;
    }

    function mint(uint amount) external {
        balanceOf[msg.sender] += amount;
        totalSupply += amount;
        emit Transfer(address(0), msg.sender, amount);
    }

    function burn(uint amount) external {
        balanceOf[msg.sender] -= amount;
        totalSupply -= amount;
        emit Transfer(msg.sender, address(0), amount);
    }
}
```

#### Vault Contract

The Vault contract manages player deposits and withdrawals of the game tokens.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

interface IERC20 {
    function totalSupply() external view returns (uint);
    function balanceOf(address account) external view returns (uint);
    function transfer(address recipient, uint amount) external returns (bool);
    function allowance(address owner, address spender) external view returns (uint);
    function approve(address spender, uint amount) external returns (bool);
    function transferFrom(address sender, address recipient, uint amount) external returns (bool);

    event Transfer(address indexed from, address indexed to, uint value);
    event Approval(address indexed owner, address indexed spender, uint value);
}

contract Vault {
    IERC20 public immutable token;

    uint public totalSupply;
    mapping(address => uint) public balanceOf;

    constructor(address _token) {
        token = IERC20(_token);
    }

    function _mint(address _to, uint _shares) private {
        totalSupply += _shares;
        balanceOf[_to] += _shares;
    }

    function _burn(address _from, uint _shares) private {
        totalSupply -= _shares;
        balanceOf[_from] -= _shares;
    }

    function deposit(uint _amount) external {
        uint shares;
        if (totalSupply == 0) {
            shares = _amount;
        } else {
            shares = (_amount * totalSupply) / token.balanceOf(address(this));
        }

        _mint(msg.sender, shares);
        token.transferFrom(msg.sender, address(this), _amount);
    }

    function withdraw(uint _shares) external {
        uint amount = (_shares * token.balanceOf(address(this))) / totalSupply;
        _burn(msg.sender, _shares);
        token.transfer(msg.sender, amount);
    }
}
```

## Acknowledgement

A big thank you to the Metacrafters Team for providing this amazing opportunity to work on such an exciting project! Your guidance and platform have been instrumental in building this DeFi Kingdom clone on Avalanche, and we truly appreciate the learning experience you've enabled.
