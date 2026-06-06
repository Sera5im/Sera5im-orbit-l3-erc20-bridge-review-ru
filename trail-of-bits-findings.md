# Trail of Bits Findings

## Source

- [Trail of Bits audit PDF](https://docs.arbitrum.io/assets/files/2024_08_01_trail_of_bits_security_audit_custom_fee_token-7ce514634632f4735a710c81b55f2d27.pdf)

## Relevant Paths

Для этого review наиболее полезные `Trail of Bits` findings матчятся в два path:

- reviewed `ERC20` bridge initiation path
- token-behavior assumptions path

## 1. ERC20 Bridge Initiation Path

**Vulnerability found:**  
`Token bridge will receive and lock ether`

**Affected function:**  
`L1OrbitERC20Gateway.outboundTransferCustomRefund(...)`

**Source function:**  
[L1OrbitERC20Gateway.sol](https://github.com/OffchainLabs/token-bridge-contracts/blob/main/contracts/tokenbridge/ethereum/gateway/L1OrbitERC20Gateway.sol)

**Before:**  
`ERC20` bridge entrypoint был `payable` и мог принять `ETH`, хотя этот path не должен был работать с `ETH`.

**After:**  
в path добавили guard:

```solidity
require(msg.value == 0, "NO_VALUE");
```

**Function:**

```solidity
function outboundTransferCustomRefund(
    address _l1Token,
    address _refundTo,
    address _to,
    uint256 _amount,
    uint256 _maxGas,
    uint256 _gasPriceBid,
    bytes calldata _data
) public payable override returns (bytes memory res) {
    require(msg.value == 0, "NO_VALUE");
    require(_l1Token != _getNativeFeeToken(), "NOT_ALLOWED_TO_BRIDGE_FEE_TOKEN");

    return super.outboundTransferCustomRefund(
        _l1Token,
        _refundTo,
        _to,
        _amount,
        _maxGas,
        _gasPriceBid,
        _data
    );
}
```

**Meaning:**  
`ERC20` bridge initiation path больше не принимает `ETH`, который мог бы застрять в bridge.
