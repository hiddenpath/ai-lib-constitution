# Workspace Setup — ai-lib 约束生效说明

所有 ai-lib 生态的本地工作均受 **ai-lib-constitution** 和 **ai-lib-plans** 约束与支持。  
需正确配置 Cursor 工作区，规则才会生效。

---

## 1. 工作区必须包含两个仓库

在 Cursor 中打开工作区时，**必须同时包含**：

- `ai-lib-constitution`（或 `d:\ai-lib-constitution`）
- `ai-lib-plans`（或 `d:\ai-lib-plans`）

### 多根工作区配置

**方式 A：通过 File → Add Folder to Workspace**

1. 打开 Cursor
2. File → Add Folder to Workspace
3. 添加 `d:\ai-lib-constitution`
4. 再次 Add Folder to Workspace，添加 `d:\ai-lib-plans`
5. 添加你要工作的项目（如 `d:\rustapp\ai-lib-rust`）
6. File → Save Workspace As → 保存为 `ai-lib.code-workspace`

**方式 B：编辑 .code-workspace 文件**

```json
{
  "folders": [
    { "path": "d:\\ai-lib-constitution" },
    { "path": "d:\\ai-lib-plans" },
    { "path": "d:\\ai-protocol" },
    { "path": "d:\\rustapp\\ai-lib-rust" },
    { "path": "d:\\rustapp\\ai-lib-python" },
    { "path": "d:\\rustapp\\ai-lib-ts" },
    { "path": "d:\\rustapp\\ai-lib-go" },
    { "path": "d:\\rustapp\\ai-protocol-mock" }
  ]
}
```

---

## 2. 验证规则是否生效

1. **打开任意 ai-lib 项目**（如 ai-lib-rust）
2. **新建 Chat** 或 **Composer**
3. **输入**：`请按 AGENTS.md 的清单，说明你在修改代码前会做哪些事`
4. **预期**：Agent 应提到读取 SOUL.md、AGENTS.md、MEMORY.md，以及加载 constitution rules

若 Agent 能正确引用这些文件，说明约束已生效。

---

## 3. 已配置规则的项目

| 项目 | 规则路径 |
|------|----------|
| ai-lib-constitution | `.cursor/rules/ai-lib-constraint.mdc` |
| ai-lib-plans | `.cursor/rules/ai-lib-constraint.mdc` |
| ai-protocol | `.cursor/rules/ai-lib-constraint.mdc` |
| ai-lib-rust | `.cursor/rules/ai-lib-constraint.mdc` |
| ai-lib-python | `.cursor/rules/ai-lib-constraint.mdc` |
| ai-lib-ts | `.cursor/rules/ai-lib-constraint.mdc` |
| ai-lib-go | `.cursor/rules/ai-lib-constraint.mdc` |
| ai-protocol-mock | `.cursor/rules/ai-lib-constraint.mdc` |
| aidebate | `.cursor/rules/ai-lib-constraint.mdc` |
| aidebate-python | `.cursor/rules/ai-lib-constraint.mdc` |

规则为 `alwaysApply: true`，在以上任一项目中工作时都会自动应用。

---

## 4. 路径说明

规则中使用的路径 `ai-lib-constitution/`、`ai-lib-plans/` 依赖这两个仓库作为工作区根目录。  
若只打开单个项目（如仅 ai-lib-rust），这些路径可能无法解析，约束将无法完全生效。  
**务必使用包含 constitution 和 plans 的多根工作区。**
