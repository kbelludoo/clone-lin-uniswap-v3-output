# clone-lin-uniswap-v3-output

Experimental i64-scale LIN clone of [Uniswap/v3-core](https://github.com/Uniswap/v3-core) `v1.0.0` SqrtPriceMath **getNextSqrtPriceFromOutput**.

- **LIN clone:** [`src/lin_uniswap_v3_output.lin`](src/lin_uniswap_v3_output.lin)
- **C11 oracle:** [`test/oracles/uniswap_v3_output_c11.c`](test/oracles/uniswap_v3_output_c11.c)
- **Proofs (results only) live in lin-open:** https://github.com/kbelludoo/lin-open
- **Event receipt:** `docs/events/EVENT_UNISWAP_V3_OUTPUT_CLONE_LIN.rulel` in lin-open

This repository is the standalone LIN copy. It is **not** Uniswap V3, **not** a Pool / SwapRouter / NFT position manager, and does **not** claim Solidity uint256 / mainnet / Q64.96 parity (`Q96=2^96` exceeds signed i64 and is rejected). It is **not** getNextSqrtPriceFromInput ([clone-lin-uniswap-v3-next-sqrt](https://github.com/kbelludoo/clone-lin-uniswap-v3-next-sqrt)), **not** getAmount0Delta ([clone-lin-uniswap-v3-sqrt](https://github.com/kbelludoo/clone-lin-uniswap-v3-sqrt)), and **not** getSqrtRatioAtTick ([clone-lin-uniswap-v3-tick](https://github.com/kbelludoo/clone-lin-uniswap-v3-tick)). `getTickAtSqrtRatio` is an assembly-MSB reject, not a working inverse.

Upstream was BUSL-1.1 with Change Date 1 April 2023; the Change License is GPL-2.0-or-later (this run is 2026-10-12). This LIN kernel is an original uint64-scale implementation of the published exact-output next-sqrt formulas at Q32=2^32, not a verbatim Solidity dump.

Pinned upstream: commit `e3589b192d0be27e100cd0daaf6c97204fdb1899` (tag `v1.0.0`), `contracts/libraries/SqrtPriceMath.sol` sha256 `ddd62e3a94346248677f30f1ab009ef015e71e4b8696dcca890eeabc9dc6c149` blob `685f485da43402235c3a9ce7fd85a49597b6373a`.

Canonical goldens (uint64-scale, 128-bit product, Q32=2^32, L=1000000 unless noted):

- `next_from_output(Q, 1e6, 5e5, zfo=1)` = **2147483648** = Q/2 (remove token1)
- `as_input` of the same args (FromInput routing) = **2863311531** (named rejected path)
- `next_from_output(Q, 1e6, 5e5, zfo=0)` = **8589934592** = 2Q (remove token0)
- `as_input` zfo=0 = **6442450944** = 1.5Q (named rejected path)
- token1 remove amount=1 ceil = **4294963001**; floor = **4294963002** (1-unit solvency)
- wrapping `(L*sqrtP)/(L-amount*sqrtP)` = **-2**; real output = 2Q
- token1 amount=L+1 wrapping sub = **-4295**; `require(sqrtP>quot)` fail-closes to **0**
- output of L token1 fail-closes to **0**; FromInput routing of the same amount yields Q/2
- `sqrtP=0` / `L=0` fail-close to **0**; `amount=0` returns `sqrtP`
- `1e18 * 1e18 / 1` fail-closes to **0**
