# Project Overview: Building a DeFi Kingdom Clone on Avalanche

Welcome to a hands-on adventure in blockchain-based gaming. We’re developing a DeFi Kingdom clone on the Avalanche network, where players collect, build, and battle with digital assets, earning rewards through engaging gameplay.

While the inner workings of DeFi and value generation are important, our focus here is on setting up an EVM subnet on Avalanche and deploying essential smart contracts for the foundation of this clone. Let's break down the key steps to guide you on this journey to creating a successful DeFi gaming platform.

### Key Steps:

1. **Set Up Your EVM Subnet**: Follow our guide and refer to the [Avalanche documentation](https://docs.avax.network/) to create a custom EVM subnet on Avalanche.
   
2. **Define Native Currency**: Set up your in-game currency to fuel your DeFi Kingdom ecosystem.

3. **Connect to MetaMask**: Seamlessly connect your EVM subnet to MetaMask following the outlined steps.

4. **Deploy Smart Contracts**: Use Solidity and Remix to deploy the essential smart contracts, covering aspects like battling, exploring, and trading, which define game rules, liquidity pools, and tokens.

---

### Avalanche Subnet

An Avalanche subnet is an independent, custom blockchain network that operates within the broader Avalanche network. Subnets are flexible and scalable, enabling specialized environments for specific use cases.

**Install Avalanche CLI**: The fastest way to install the Avalanche CLI is via the following script:

```bash
curl -sSfL https://raw.githubusercontent.com/ava-labs/avalanche-cli/main/scripts/install.sh | sh -s
```

You’ll use Subnet-EVM, Avalanche’s fork of the Ethereum Virtual Machine, to create an EVM-based subnet. It supports features like airdrops, configurable gas fees, and custom fee tokens. Check out [Subnet-EVM](https://github.com/ava-labs/subnet-evm) for more information.

**Creating a Subnet**:

To create a new subnet with Avalanche CLI, use the command below:

```bash
avalanche subnet create mySubnet
```

Follow the prompts to set up your EVM subnet, including defining the chain ID, native token symbol, and deployment options. Example setup:

- ChainId: `12345567`
- Token symbol: `MYSUBNET`
- Gas throughput: `1.5 million gas/s` (default C-Chain setting)
- Airdrop: 1 million tokens to the default address (for testing only)

**Deploy the Subnet**: Deploy the subnet locally using:

```bash
avalanche subnet deploy mySubnet
```

After deployment, the console will provide details about your subnet, allowing you to interact with and build your smart contracts.

---

### ERC20 Token Contract

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.17;

contract ERC20 {
    uint public totalSupply;
    mapping(address => uint) public balanceOf;
    mapping(address => mapping(address => uint)) public allowance;
    string public name = "Solidity by Example";
    string public symbol = "SOLBYEX";
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

**Explanation**: This contract follows the ERC20 standard for fungible tokens. Key elements include:

- **totalSupply**: Tracks the total supply of tokens.
- **balanceOf**: Stores the token balance for each address.
- **allowance**: Manages token allowances for spending on behalf of another address.
- **transfer, approve, transferFrom**: Functions for token transfers and allowance management.
- **mint, burn**: Functions for minting (creating) and burning (destroying) tokens.

---

### Vault Contract

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

**Explanation**: The Vault contract interacts with ERC20 tokens for deposit and withdrawal operations. It uses shares to track deposits and allows users to exchange shares for the corresponding amount of tokens during withdrawals.

- **IERC20 Interface**: Standard interface for interacting with ERC20 tokens.
- **deposit()**: Users deposit tokens in exchange for shares.
- **withdraw()**: Users burn shares to withdraw corresponding token amounts.

---

This guide outlines the core components of building a DeFi Kingdom clone on Avalanche, from setting up an EVM subnet to deploying essential contracts. As you progress, these building blocks will enable a functional DeFi gaming ecosystem.
