# Orbit L3 ERC20 Review

## Overview

Этот репозиторий содержит короткий review `Orbit` `L3` parent-to-child `ERC20` bridge path.

Главная идея здесь не в том, чтобы подавать `Orbit` как полностью отдельную full bridge-систему.

Главная идея в том, чтобы выделить `Orbit`-specific часть `ERC20` flow и показать, где именно она расходится со знакомой унаследованной bridge-моделью.

## Reviewed Orbit-Specific Functions

- `L1GatewayRouter.outboundTransferCustomRefund(...)`
- `L1OrbitERC20Gateway.outboundTransferCustomRefund(...)`
- `L1ERC20Gateway.outboundTransferCustomRefund(...)`

## Flow

```mermaid
flowchart TD
    A["L1GatewayRouter.outboundTransferCustomRefund(...)"]
    B["L1OrbitERC20Gateway.outboundTransferCustomRefund(...)"]
    C["L1ERC20Gateway.outboundTransferCustomRefund(...)"]
    D["L1ArbitrumGateway.outboundTransferCustomRefund(...)"]
    E["дальше идет знакомая gateway logic"]

    A --> B --> C --> D --> E
```

## Review Shape

Главное отличие здесь сосредоточено в wrapper-layer.

После этих wrapper-функций path продолжает знакомую gateway logic, как и в стандартном `L1 -> L2` `ERC20` bridge flow.

## Files

- [`diff-review.md`](./diff-review.md) — `Orbit`-specific wrapper и точка, где path возвращается в знакомую gateway logic
- [`trail-of-bits-findings.md`](./trail-of-bits-findings.md) — relevant `Trail of Bits` findings, привязанные к этому bridge path
