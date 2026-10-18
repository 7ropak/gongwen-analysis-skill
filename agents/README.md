# agents/ — 执行件索引

> 项目入口协议：`公文分析Agent.md`（宿主 Agent 路由至此）。

## 调用顺序

```
用户／宿主 Agent
   └─ 公文分析Agent（编排 S1–S12，调用 skills/ 下各技能）
         ├─ skills/公文类型与结构解析.md      （S1）
         ├─ skills/事件背景调研.md            （S2）
         ├─ skills/利益相关者建模.md          （S3 ＋ S6）
         ├─ skills/声明语用标注.md            （S4）
         ├─ skills/财务与合规信号扫描.md      （S5）
         ├─ skills/分析报告生成.md            （S8）
         ├─ skills/跟踪立项管理.md            （S9）
         └─ 分析审计Agent                     （S10 独立复核）
```

| Agent | 职责 | 何时调用 |
|---|---|---|
| `公文分析Agent.md` | 主入口；流水线编排、结论层、写入文件 | 每次分析 |
| `分析审计Agent.md` | 独立上下文八类硬检 | 每次分析收尾（S10）；也可由用户单独点名复核 |

## 读取频次

- **每次触发**：`公文分析Agent.md` 的 L0／L1／L2 能力1 表格。
- **按需**：该 Agent 全文、被点名的那一个 skill。
- **执行中**：`knowledge-base/` 各件（由 skill 在段内按需取）。
