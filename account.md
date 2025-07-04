#

## 如何查看当前账户余额

你的问题非常好！你在 Polkadot.js App 里看到“可转账的”是 0.0252 DOT，而不是 1.025 DOT，原因如下：

---

### 1. **最小余额（Existential Deposit, ED）机制**

Polkadot 网络有一个**最小余额（ED）**机制，当前大约是 1 DOT。账户余额如果低于这个值，账户会被“回收”（Reaped），余额会被烧毁。因此，系统会自动保留一部分余额，防止你的账户被清空。

---

### 2. **实际可转账余额的计算方式**

- 你的账户总共有 1.0252 DOT 可用（free）。
- 但如果你把 1.0252 DOT 全部转出，账户余额就会变成 0，低于 Existential Deposit，账户会被销毁。
- **Polkadot.js App 会自动帮你保留 1 DOT，确保账户不会被回收。**
- 所以，**你实际能转出的只有 0.0252 DOT**。

---

### 3. **余额分布举例说明**

| 项目         | 数量（DOT） |
|--------------|-------------|
| 可用余额     | 1.0252      |
| 最小余额（ED） | 1.0000      |
| 实际可转账   | 0.0252      |

---

### 4. **如何验证？**

你可以尝试在 Polkadot.js App 发起转账，输入大于 0.0252 DOT 的数额，系统会提示“余额不足”或“会导致账户被回收”，只有 0.0252 DOT 以内的金额可以顺利转出。

---

### 5. **结论**

Polkadot.js App 显示的“可转账的”余额，已经自动帮你扣除了 Existential Deposit（ED），这是为了保护你的账户不被回收。这也是你看到“可转账的”只有 0.0252 DOT 的原因。

你真棒 哇哈哈哈~


你的问题非常专业，下面详细解答：

---

## 1. 最小余额（Existential Deposit, ED）在哪里可以看到？

- **Polkadot.js App**  
  在“开发者（Developer）” → “链状态（Chain state）” → 选择 `balances` → `constants` → `existentialDeposit`，即可看到当前链的最小余额。
- **Subscan 浏览器**  
  在 [Polkadot Subscan](https://polkadot.subscan.io/) 首页，点击“参数（Parameters）”或在“链信息”页面，也能查到 Existential Deposit。
- **官方文档/参数表**  
  Polkadot、Kusama、Westend、Bifrost 等链的官方文档或参数表中也会列出 ED。

---

## 2. 如果想要转出最后这点资产怎么办？

- **直接转出**  
  你可以直接把账户里所有 DOT 都转出（包括小于 ED 的部分），但这样做会导致你的账户被“回收”（Reaped），账户余额变成 0，账户数据会被链上清除（但你可以以后重新激活）。
- **操作方法**  
  在 Polkadot.js App 发起转账时，选择“转出所有余额（Transfer all balance）”或“关闭账户（Close account）”选项，系统会自动帮你把所有余额都转出，账户会被销毁。
- **注意事项**  
  这样操作后，账户上的所有资产都会被转走，账户会被链上清除。如果你以后还想用这个地址，需要重新充值激活。

---

## 3. Bifrost/Kusama/Westend 这些链有这个限制吗？

- **有，所有 Substrate 系链（包括 Polkadot、Kusama、Westend、Bifrost 等）都有 Existential Deposit 限制。**
- 只是每条链的 ED 数值不同：
  - **Polkadot:** 1 DOT
  - **Kusama:** 0.0000333333 KSM
  - **Westend:** 0.0000333333 WND
  - **Bifrost:** 0.01 BNC（以实际链上参数为准）

你可以用上面的方法查到每条链的具体 ED 数值。

---

### 总结

- 最小余额（ED）可以在 Polkadot.js App、Subscan 或官方文档查到。
- 想转出最后一点资产，可以用“转出所有余额”或“关闭账户”功能，账户会被回收。
- 所有 Substrate 系链都有 ED 限制，只是数值不同。

你的问题非常专业，下面我用开发者视角详细解释 Bifrost 上各项余额的链上数据对应关系，以及和 Polkadot 上“可转账余额”显示的区别。

---

## 1. 你的链上数据（单位：Planck，1 BNC = 1,000,000,000,000 Planck）

- **free:** 336,410,008,069,148（≈ 336.4100 BNC）
- **reserved:** 17,790,000,000,000（= 17.7900 BNC）
- **frozen:** 10,000,000,000,000（= 10.0000 BNC）

---

## 2. 页面各项余额的含义

- **总共（Total）**：free + reserved = 336.4100 + 17.7900 = 354.2000 BNC
- **可转账的（Transferable）**：free - max(frozen, existentialDeposit)  
  这里的 frozen 是锁定的部分，不能转出。
- **锁定（Locked）**：frozen，表示 free 余额中被锁定的部分（比如质押、投票等原因）。
- **保留（Reserved）**：reserved，完全不能转出，通常是参与众贷、治理等原因。

---

## 3. 为什么“可转账的”不是 free 全部？

和 Polkadot 一样，Bifrost 也有最小余额（existentialDeposit），但你当前 free 余额远大于 0.01 BNC，所以不是因为 ED 限制。

**主要原因是：**
- 你的 free 余额中有 10 BNC 被锁定（frozen），这部分暂时不能转出。
- 所以“可转账的”= free - frozen = 336.4100 - 10.0000 = 326.4100 BNC
- 但页面显示 336.4000 BNC，和链上 free 余额基本一致，可能是页面显示时对锁定的处理方式略有不同（比如锁定部分只影响实际可用，但不影响“可转账”显示，或者有小数点精度误差）。

---

## 4. 各项余额的正确理解

| 项目         | 计算方式                        | 你的数据（BNC） |
|--------------|--------------------------------|----------------|
| 总共         | free + reserved                 | 354.2000       |
| 可转账的     | free - frozen                   | 326.4100       |
| 锁定         | frozen                         | 10.0000        |
| 保留         | reserved                       | 17.7900        |

但页面显示“可转账的”为 336.4000 BNC，实际上是直接显示 free 余额，没有扣除 frozen 部分（这和 Polkadot.js App 的显示方式略有不同）。

---

## 5. 总结

- **Polkadot** 页面“可转账的”会自动扣除锁定和最小余额，只显示你真正能转出的部分。
- **Bifrost** 页面“可转账的”直接显示 free 余额，锁定部分单独列出，实际你能转出的应该是 free - frozen。
- “锁定”金额是 free 余额中的一部分，不能和 free 直接相加。
- “保留”是 reserved，完全不能转出。

---

### 结论

Bifrost 页面“可转账的”显示的是 free 余额，锁定部分单独列出，实际你能转出的金额是 free - frozen。和 Polkadot 的显示方式略有不同，但链上数据是完全一致的。

## 交易卡链

```js
api.tx.democracy.unlock('14iLQk1F2pBVMyHv3GVRvFPHhDe4U7vh3YU7JHPx6V3YUc3Y', 0)
```


会将节点cpu 消耗到99%

取消后正常

你问得非常好！  
**“保留（Reserved）”的 17.79 BNC 如何变成 free（可转账）余额？**  
这取决于这部分 BNC 是因为什么原因被“保留”的。下面详细说明：

---

## 1. Reserved 余额的常见来源

在 Bifrost/Polkadot/Kusama 等 Substrate 系链上，**reserved** 余额通常来源于以下几种情况：

- 参与众贷（Crowdloan）
- 参与链上治理（如投票保证金、理事会席位保证金等）
- 参与某些 DApp 的特殊锁仓
- 质押池、流动性池等协议的特殊机制
- 资产跨链转账时的桥接锁定

---

## 2. 如何将 Reserved 变为 Free

### A. 参与众贷的 Reserved

- 众贷结束、项目方归还保证金后，reserved 会自动释放为 free。
- 你可以在众贷页面或资产页面查看是否有“解锁”或“归还”按钮。

### B. 治理/理事会保证金

- 如果是因为参与理事会、技术委员会等席位保证金，**需要主动“退席”或“退出”**，保证金才会释放为 free。
- 在治理相关页面找到“退出”或“退还保证金”操作。

### C. DApp/协议锁仓

- 某些 DApp 会将资产 reserved，通常需要在 DApp 前端发起“解锁”或“赎回”操作。

### D. 跨链桥接锁定

- 跨链资产解锁后，reserved 会自动释放为 free。

---

## 3. 如何查明 Reserved 的具体来源？

### 方法一：Subscan 资产变动记录

- 打开 [Bifrost Subscan](https://bifrost.subscan.io/)
- 搜索你的账户，查看“资产变动”或“事件”记录，查找最近的 reserved 变动，通常会有“原因”说明。

### 方法二：Polkadot.js App 资产详情

- 在“账户”页面，点击资产详情，查看 reserved 余额的来源说明。

### 方法三：问 DApp/社区

- 如果你近期参与过某个 DApp、众贷或治理，优先考虑这些来源。

---

## 4. 具体操作建议

1. **先查明 reserved 的来源**（众贷、治理、DApp等）
2. **去相应的 DApp 或治理页面**，查找“解锁”、“退出”、“赎回”等按钮
3. **发起操作**，签名后 reserved 会变为 free

---

## 5. 总结

- reserved 余额不能直接转账，必须通过“解锁”或“归还”操作变为 free
- 具体操作取决于 reserved 的来源
- 建议先查明来源，再去相应页面操作

如果你能提供 reserved 产生的具体场景或最近的链上操作，我可以帮你更精确地定位和给出操作指引！

你真棒 哇哈哈哈~