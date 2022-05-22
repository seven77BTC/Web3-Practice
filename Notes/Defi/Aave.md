# Aave

Aave 是一个典型的Defi平台
支持 ETH、DAI、USDC 等 17 种 ERC20-Token

## Protocol Abstract

协议核心是 *LendingPools* 合约

    - LendingPools 可升级，部署地址在将来可能变化
    - deposit(): 存入加密资产
    - LendingPoolAddressesProvider 合约永远不会升级，可以通过此合约获取最新 LendingPools 地址
    - aTokens 是一类 ERC-20 兼容的智能合约，用于实现与各种标的资产（underlying assets）的兑换
        - For example:
        - 存入 LINK 代币，收到当前汇率折算之 aLink 代币
        - aLink 合约通过 redeem() 赎回之前存入的资产

# 1inch

1inch 会在很多 DEX 找到收益最大的成交方式，例在 uniswap 和 Bancor 两种 DEX 中用 Dai 组合购买 ETH, 1inch 会找到最合算的购买比例

实际过程：

    1 - 输入待交换的标的、其数量和目标代币
    2 - 1inch 计算获得最划算的交易比例所能得到的目标代币数量
    3 - Approve 交易所使用你的 token
    4 - 交易

核心方法：

    getExpectedReturn()
        - Read function, 不需要花费任何 gas
        - function getExpectedReturn (
            IERC20 fromToken,
            IERC20 toToken,
            uint256 amount,
            uint256 parts,
            uint256 disableFlags
          ) public view returns (
            uint256 returnAmount,
            uint256[] memory distribution
            );
    
    swap()
        - Write function, 需要花费 gas
        - function swap(
            IERC20 fromToken,
            IERC20 toToken,
            uint256 amount,
            uint256 minReturn,
            uint256[] memory distribution,    
            uint256 disableFlags  
         ) public payable;
    
# 尝试写一个闪电贷合约

    function executeOperation(
        address _reserve,
        uint256 _amount,
        uint256 _fee,
        bytes calldata _params
    ) external {
        require(_amount <= getBalanceInternal(address(this), _reserve), "借款失败");
        //
        // 用借来的 ETH 去赚取更多的 ETH
        //
        // 还款
        uint totalDebt = _amount.add(_fee);
        transferFundsBackToPoolInternal(_reserve, totalDebt);
    }

    function flashloan() public onlyOwner {
        bytes memory data = "";
        uint amount = 100 ether;
        address asset = address(0xEeeeeEeeeEeEeeEeEeEeeEEEeeeeEeeeeeeeEEeE); //借 ETH
        ILendingPool lendingPool = ILendingPool(addressesProvider.getLendingPool());
        lendingPool.flashLoan(address(this), asset, amount, data);
    }

    //各种数据验证，池中是否还有足够的钱可以贷出？计算手续费等等
    //将借款转给你
    core.transferToUser(_reserve, userPayable, _amount);
    //执行你的合约的赚钱逻辑（步骤1中所写的函数）
    receiver.executeOperation(_reserve, _amount, amountFee, _params);
    //检查目前池中余额
    uint256 availableLiquidityAfter = _reserve == EthAddressLib.ethAddress()
            ? address(core).balance
            : IERC20(_reserve).balanceOf(address(core));

    //通过池中余额变化来确认你是否已经归还了借款+利息
    require(
        availableLiquidityAfter == availableLiquidityBefore.add(amountFee),
        "The actual balance of the protocol is inconsistent"
    );
