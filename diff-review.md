# Orbit Diff Review

## Review Style

Этот review рассматривает `Orbit` как узкий `ERC20` bridge diff, а не как полностью отдельный full bridge lifecycle.

Главный review interest сосредоточен в дополнительном `Orbit`-specific gateway wrapper перед возвратом в знакомую унаследованную bridge logic.

## Orbit-Specific Functions

### 1. `L1GatewayRouter.outboundTransferCustomRefund(...)`

Этот wrapper важен потому, что он является routed entrypoint, который доводит вызов до выбранного gateway.

Для этого review полезно держать его видимым, потому что `Orbit` path все равно достигается через знакомую router surface до появления дополнительного gateway wrapper.

### 2. `L1OrbitERC20Gateway.outboundTransferCustomRefund(...)`

Это главный `Orbit`-specific entrypoint в рассматриваемом `ERC20` path.

Он важен потому, что:

- сохраняет `ERC20` bridge initiation surface
- режет `ETH` value для этого path
- запрещает bridging native fee token
- передает execution дальше в `super.outboundTransferCustomRefund(...)`

### 3. `L1ERC20Gateway.outboundTransferCustomRefund(...)`

Этот wrapper важен потому, что path не прыгает напрямую из `Orbit` wrapper в финальную знакомую gateway function.

Вместо этого он еще проходит через `ERC20` wrapper layer перед тем, как дойти до более глубокой parent gateway logic.

## Where The Familiar Flow Continues

После `Orbit`-specific wrapper path еще проходит через:

- `L1ERC20Gateway.outboundTransferCustomRefund(...)`
- `L1ArbitrumGateway.outboundTransferCustomRefund(...)`

С этого места path продолжает знакомую gateway logic, как и в стандартном `L1 -> L2` `ERC20` bridge flow.

## Conclusion

Главная мысль этого review в том, что `Orbit` здесь не создает полностью отдельный `ERC20` bridge lifecycle.

Вместо этого он добавляет дополнительный wrapper-layer перед тем, как path продолжает знакомую gateway logic стандартного `L1 -> L2` `ERC20` bridge flow.
