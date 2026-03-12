# AI Recorder - Chrome Extension

一个用于录制用户浏览器操作并生成 Playwright 自动化脚本的 Chrome 扩展。

## 截图

### 录制前
![录制前](screenshots/录制前.png)

### 录制中
![录制中](screenshots/录制中.png)

### 录制完成
![录制完成](screenshots/录制完成.png)

## 功能特性

- ✅ **操作录制**：录制点击、输入、选择、滚动、导航等用户操作
- ✅ **SOP 管理**：保存录制的操作为标准操作程序 (SOP)
- ✅ **脚本生成**：自动生成 Playwright TypeScript 测试脚本
- ✅ **JSON 导出**：导出结构化 JSON 供 AI 系统解析执行
- ✅ **Markdown 摘要**：生成人类可读的操作步骤文档
- ✨ **实时日志面板**：页面内显示录制进度（新功能！）
- ✨ **独立窗口模式**：失去焦点不关闭（新功能！）

## 项目结构

```
AIRecorder/
├── manifest.json          # Chrome 扩展配置文件
├── background/
│   └── background.js      # 后台服务，处理消息和存储
├── content/
│   └── content.js         # 内容脚本，录制用户操作
├── popup/
│   ├── popup.html         # 弹出界面
│   ├── popup.css          # 样式文件
│   └── popup.js           # UI 逻辑
├── sidebar/
│   ├── sidebar.html       # 悬浮侧边栏界面
│   ├── sidebar.css        # 侧边栏样式
│   └── sidebar.js         # 侧边栏逻辑
├── utils/
│   ├── helpers.js         # 工具函数
│   ├── selector.js        # 选择器生成
│   ├── storage.js         # 存储管理
│   └── playwright-generator.js  # Playwright 脚本生成
└── icons/
    ├── icon16.png
    ├── icon48.png
    └── icon128.png
```

## 安装方法

### 方式 1：开发者模式安装

1. 打开 Chrome 浏览器
2. 访问 `chrome://extensions/`
3. 打开右上角的"开发者模式"
4. 点击"加载已解压的扩展程序"
5. 选择 `AIRecorder` 项目目录
6. 扩展图标将出现在浏览器工具栏

## 使用方法

### 打开悬浮侧边栏（推荐）

**方式 1：右键菜单**
1. 在任意网页上点击鼠标右键
2. 选择"打开 AI Recorder 侧边栏"
3. 侧边栏会悬浮在屏幕右侧

**方式 2：Popup 按钮**
1. 点击浏览器工具栏的 AI Recorder 图标
2. 点击"打开悬浮侧边栏"按钮

### 录制操作

1. **开始录制**
   - 在侧边栏中点击"开始录制"
   - 或在 popup 中输入 SOP 名称后点击"开始录制"
   - 页面右上角会出现红色录制指示器

2. **执行操作**
   - 在页面中执行需要录制的操作
   - 侧边栏会实时显示录制的步骤
   - 可以点击链接跳转到其他页面（录制继续）

3. **停止录制**
   - 在侧边栏或 popup 中点击"停止录制"
   - SOP 被保存
   - 可以在列表中查看和管理

### 查看和管理 SOP

1. 打开 popup 查看 SOP 列表
2. 点击"查看"按钮查看详细步骤
3. 支持四种导出格式：
   - **Playwright 脚本** - TypeScript 测试代码，可直接运行
   - **JSON 格式** - 结构化数据供 AI 使用
   - **Skill 格式** - Trae 可复用技能文件
   - **Markdown 摘要** - 人类可读文档

## 导出格式说明

### 1. Playwright 脚本 (`.spec.ts`)

生成可直接运行的 Playwright 自动化测试脚本：

```bash
# 安装 Playwright
npm init playwright@latest

# 运行测试
npx playwright test your-sop.spec.ts
```

### 2. JSON 格式 (`.json`)

结构化数据，适合 AI 系统解析和执行：

```json
{
  "name": "用户登录流程",
  "steps": [
    { "step": "navigation", "url": "https://example.com/login" },
    { "step": "input", "selector": "#username", "value": "testuser" }
  ]
}
```

### 3. Skill 格式 (`.md`)

生成 Trae 可复用的技能文件，包含完整的操作流程描述：

```markdown
---
name: "login-action"
description: "Executes 登录流程. Invoke when user wants to perform this recorded workflow."
---

# 登录流程

## Purpose
Automates the workflow: 登录流程

## Parameters
- `url`: Target URL (default: https://example.com/login)
- `#username`: Input value (default: testuser)

## Steps
1. **NAVIGATE**: 导航到登录页面
2. **INPUT**: 输入用户名
3. **CLICK**: 点击登录按钮

## Example Usage
"Please execute the 登录流程 workflow"
```

**使用 Skill 文件：**
1. 将导出的 `.md` 文件放入项目的 `.trae/skills/` 目录
2. Trae 会自动识别并可复用该技能

### 4. Markdown 摘要 (`.md`)

人类可读的操作文档，适合作为 SOP 文档分享。

## 生成的 Playwright 脚本示例

```typescript
import { test, expect, Page } from '@playwright/test';

test.describe('用户登录流程', () => {
  test('用户登录流程', async ({ page }) => {
    test.setTimeout(30000);

    await page.goto('https://example.com/login');
    await page.locator('#username').fill('testuser');
    await page.locator('#password').fill('password123');
    await page.locator('#login-btn').click();
  });
});
```

## AI 集成

导出的 JSON 格式可以被 AI 系统解析和执行：

```json
{
  "name": "用户登录流程",
  "description": "演示用户登录的完整流程",
  "steps": [
    {
      "step": "navigation",
      "selector": "",
      "description": "Navigate to https://example.com/login",
      "url": "https://example.com/login"
    },
    {
      "step": "input",
      "selector": "#username",
      "description": "input username",
      "value": "testuser"
    }
  ]
}
```

## 录制的操作类型

- **click** - 点击操作
- **dblclick** - 双击操作
- **input** - 文本输入
- **select** - 下拉选择
- **scroll** - 页面滚动
- **keydown** - 键盘按键
- **navigation** - 页面导航
- **wait** - 等待操作

## 技术特点

- **智能选择器生成**：优先生成稳定、唯一的 CSS 选择器
- **去抖动处理**：对输入和滚动事件进行智能合并
- **录制状态指示**：实时显示录制状态
- **本地存储**：所有数据存储在浏览器本地
- **Manifest V3**：使用最新的 Chrome 扩展 API

## 注意事项

1. 扩展在录制时会显示红色录制指示器
2. 所有操作数据存储在浏览器本地，不会上传
3. 导出脚本前请确保 SOP 操作完整
4. 生成的 Playwright 脚本可能需要根据实际环境调整

## 开发说明

### 修改代码后重新加载

1. 访问 `chrome://extensions/`
2. 找到 AI Recorder 扩展
3. 点击刷新按钮重新加载

### 调试

- **Content Script**: 在开发者工具的 Console 中选择对应的内容页面
- **Background Script**: 在扩展卡片中点击 "Service Worker" 链接
- **Popup**: 右键点击扩展图标 -> "检查弹出内容"

## 许可证

MIT License
