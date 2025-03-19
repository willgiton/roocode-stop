# Roo Code 技术文档

## 项目概述

Roo Code (原名 Roo Cline) 是一个 VS Code 扩展，它将一个完整的 AI 代理团队集成到编辑器中，旨在通过自动化各种开发任务来提高开发效率。

### 目标

- 为开发者提供一个智能的 AI 助手，帮助他们更快、更好地完成开发任务。
- 减少重复性工作，让开发者专注于更具创造性的工作。
- 提供一个可定制的、可扩展的平台，以适应不同的开发需求。

### 主要功能

- **代码生成：** 根据自然语言描述生成代码。
- **代码补全：** 提供智能的代码补全建议。
- **代码调试：** 自动诊断和修复代码中的错误。
- **代码重构：** 帮助开发者改进代码结构和质量。
- **文档生成：** 自动生成代码文档。
- **测试生成:** 自动生成单元测试。
- **多模式支持：** 提供多种 AI 代理模式，例如代码编写模式、调试模式、架构设计模式等。
- **自定义模式：** 允许用户创建自己的 AI 代理模式。
- **上下文感知：** 能够理解代码上下文，提供更准确的建议。
- **可扩展性：** 支持通过插件扩展功能。

### 目标用户

- 所有使用 VS Code 进行开发的软件工程师。

### 技术栈

- **前端：** TypeScript, React, Tailwind CSS
- **后端：** TypeScript, Node.js
- **代码解析：** Tree-sitter
- **AI 模型：** OpenAI, Anthropic, Mistral, Gemini, 以及其他开源模型

## 项目结构

```
.
├── .clinerules
├── .eslintrc.json
├── .git-blame-ignore-revs
├── .gitattributes
├── .gitconfig
├── .gitignore
├── .npmrc
├── .nvmrc
├── .prettierignore
├── .prettierrc.json
├── 🔒 .rooignore
├── .roomodes
├── .vscodeignore
├── CHANGELOG.md
├── ellipsis.yaml
├── esbuild.js
├── flake.lock
├── flake.nix
├── jest.config.js
├── knip.json
├── LICENSE
├── package-lock.json
├── package.json
├── PRIVACY.md
├── README.md
├── technical_documentation.md
├── assets/
│   ├── docs/
│   │   └── demo.gif
│   └── icons/
│       └── rocket.png
├── audio/
│   ├── celebration.wav
│   ├── notification.wav
│   └── progress_loop.wav
├── bin/
├── cline_docs/
│   └── settings.md
├── e2e/
│   ├── .env.local.sample
│   ├── .vscode-test.mjs
│   ├── package-lock.json
│   ├── package.json
│   ├── tsconfig.json
│   ├── VSCODE_INTEGRATION_TESTS.md
│   └── src/
│       └── suite/
│           ├── extension.test.ts
│           ├── index.test.ts
│           ├── modes.test.ts
│           ├── subtasks.test.ts
│           ├── task.test.ts
│           └── utils.ts
├── scripts/
│   └── update-contributors.js
└── src/
    ├── activate/        # 扩展激活相关
    │   ├── handleUri.ts  # 处理自定义 URI scheme
    │   ├── humanRelay.ts # 处理用户输入
    │   ├── index.ts      # 激活入口
    │   ├── registerCodeActions.ts # 注册代码操作
    │   ├── registerCommands.ts    # 注册命令
    │   └── registerTerminalActions.ts # 注册终端操作
    ├── api/             # API 调用
    │   ├── index.ts      # API 入口
    │   │   - **ClineOptions 接口：** 包含 API 配置和扩展选项。
    │   │   - **SingleCompletionHandler 接口：** 完成单一提示。
    │   │   - **ApiHandler 接口：** 创建消息、获取模型信息、计算 token 数量。
    │   │   - **buildApiHandler 函数：** 根据 API 配置构建相应的 API 提供商实例。
    │   │   - **getModelParams 函数：** 获取模型参数。
    │   ├── __tests__/     # API 测试
    │   └── providers/     # API 提供商实现（支持多种模型）
    │       ├── anthropic.ts     # Anthropic API
    │       ├── base-provider.ts  # 基础提供商类
    │       ├── bedrock.ts       # AWS Bedrock API
    │       ├── deepseek.ts      # DeepSeek API
    │       ├── gemini.ts        # Google Gemini API
    │       ├── glama.ts         # Glama API
    │       ├── human-relay.ts   # 用户输入中继
    │       ├── lmstudio.ts      # LM Studio API
    │       ├── mistral.ts       # Mistral API
    │       ├── ollama.ts        # Ollama API
    │       ├── openai-native.ts # OpenAI Native API
    │       ├── openai.ts        # OpenAI API
    │       ├── openrouter.ts    # OpenRouter API
    │       └── __tests__/  # 提供商测试
    ├── core/            # 核心逻辑
    │   ├── Cline.ts      # Cline 类，核心类
    │   │   - **Cline 类：** 负责任务管理、消息管理、API 调用、工具调用、检查点管理、与 VS Code 编辑器交互。
    │   │   - **initiateTaskLoop 方法：** 负责启动任务循环，处理用户输入、调用 AI 模型、执行工具等。
    │   │   - **presentAssistantMessage 方法：** 负责展示助手消息，包括文本、代码、按钮等。
    │   │   - **recursivelyMakeClineRequests 方法：** 递归处理用户内容，支持多轮对话和流式输出。
    │   │   - **getEnvironmentDetails 方法：** 获取环境详细信息，例如可见文件、打开的标签页等。
    │   │   - **checkpointSave、checkpointRestore、checkpointDiff 方法：** 检查点管理，用于保存、恢复和比较代码状态。
    │   ├── CodeActionProvider.ts # 代码操作提供器，用于提供快速修复等操作。
    │   ├── contextProxy.ts       # 上下文代理，用于管理和传递上下文信息。
    │   ├── EditorUtils.ts        # 编辑器工具类, 提供与 VS Code 编辑器交互的实用功能。
    │   ├── mode-validator.ts     # 模式验证器, 验证自定义模式的定义是否合法。
    │   └── __tests__/      # 核心逻辑测试
    ├── integrations/    # 与 VS Code 集成
    │   ├── diagnostics/  # 代码诊断信息，例如错误和警告
    │   ├── editor/       # 编辑器交互，例如光标位置、选择文本
    │   ├── misc/         # 杂项功能
    │   ├── terminal/     # 终端集成，例如执行命令
    │   ├── theme/        # 主题相关，例如获取当前主题颜色
    │   └── workspace/    # 工作区文件跟踪，例如监听文件变化
    ├── services/        # 服务
    │   ├── browser/      # 浏览器服务
    │   │   - **UrlContentFetcher 类：** 抓取 URL 内容并转换为 Markdown。
    │   │   - **BrowserSession 类：** 管理浏览器会话，提供导航、点击、输入等操作。
    │   ├── checkpoints/   # 检查点服务
    │   │   - **RepoPerTaskCheckpointService 类：** 每个任务一个仓库的检查点服务，用于保存和恢复任务状态。
    │   │   - **RepoPerWorkspaceCheckpointService 类：** 每个工作区一个仓库的检查点服务, 用于保存和恢复工作区状态。
    │   │   - **ShadowCheckpointService 类：** 检查点服务的抽象基类。
    │   ├── glob/         # 文件搜索服务
    │   │   - **listFiles 函数：** 列出指定目录下的文件，支持 glob 模式匹配。
    │   ├── mcp/          # MCP 服务
    │   │   - **McpHub 类：** 管理 MCP 服务器连接、工具调用、资源访问等。
    │   │   - **McpServerManager 类：** 管理 MCP 服务器实例，用于与外部进程通信。
    │   ├── ripgrep/      # 正则表达式搜索服务
    │   │   - **regexSearchFiles 函数：** 在文件中进行正则表达式搜索。
    │   ├── telemetry/    # 遥测数据收集服务
    │   │   - **TelemetryService 类：** 收集并发送遥测数据，用于分析用户行为和改进产品。
    │   ├── tree-sitter/  # 代码解析和语法分析服务
    │   │   - **parseSourceCodeForDefinitionsTopLevel 函数：** 解析代码提取顶级定义，例如类、函数、变量等。
    │   │   - **languageParser.ts：** 加载和初始化 tree-sitter 解析器，支持多种编程语言。
    ├── shared/          # 共享工具和常量
    │   ├── api.ts                # API 相关类型定义
    │   ├── array.ts              # 数组工具函数
    │   │   - **findLastIndex 函数：** 查找数组中最后一个满足条件的元素索引。
    │   │   - **findLast 函数：** 查找数组中最后一个满足条件的元素。
    │   ├── checkExistApiConfig.ts # 检查 API 配置
    │   │   - **checkExistKey 函数：** 检查 API 密钥是否存在。
    │   ├── checkpoints.ts         # 检查点相关类型定义
    │   ├── combineApiRequests.ts  # 合并 API 请求
    │   │   - **combineApiRequests 函数：** 合并多个 API 请求为一个。
    │   ├── combineCommandSequences.ts # 合并命令序列
    │   │   - **combineCommandSequences 函数：** 合并多个命令序列为一个。
    │   ├── context-mentions.ts     # 上下文提及相关
    │   │   - **formatGitSuggestion 函数：** 格式化 Git 建议。
    │   ├── experiments.ts          # 实验性功能
    │   ├── getApiMetrics.ts       # 获取 API 指标
    │   │   - **getApiMetrics 函数：** 获取 API 调用指标，例如请求次数、错误次数等。
    │   ├── globalFileNames.ts     # 全局文件名
    │   ├── globalState.ts          # 全局状态管理
    │   ├── HistoryItem.ts         # 历史记录项类型定义
    │   ├── language.ts            # 语言相关工具函数
    │   │   - **formatLanguage 函数：** 格式化语言名称。
    │   ├── modes.ts               # 模式管理
    │   │   - **getGroupName、getModeBySlug、getAllModes 等函数：** 模式管理和工具权限控制。
    │   ├── support-prompt.ts      # 支持提示
    │   ├── TelemetrySetting.ts    # 遥测设置
    │   ├── tool-groups.ts         # 工具组
    │   │   - **getToolName 函数：** 获取工具名称。
    │   │   - **getToolOptions 函数：** 获取工具选项。
    │   ├── vsCodeSelectorUtils.ts # VS Code 选择器工具函数
    │   │   - **stringifyVsCodeLmModelSelector 函数：** 将 VS Code 语言模型选择器转换为字符串。
    │   ├── WebviewMessage.ts      # Webview 消息类型定义
    │   └── __tests__/     # 共享工具测试
    ├── utils/            # 通用工具函数
    │   ├── cost.ts          # API 成本计算
    │   │   - **calculateApiCostAnthropic、calculateApiCostOpenAI 函数：** 计算 API 调用成本。
    │   ├── fs.ts            # 文件系统工具函数
    │   │   - **createDirectoriesForFile、fileExistsAtPath 函数：** 文件系统相关操作，例如创建目录、检查文件是否存在。
    │   ├── git.ts           # Git 工具函数
    │   │   - **checkGitRepo、checkGitInstalled、searchCommits 等函数：** Git 相关操作，例如检查是否为 Git 仓库、检查 Git 是否安装、搜索提交记录等。
    │   ├── path.ts          # 路径工具函数
    │   │   - **toPosix、arePathsEqual、getReadablePath 函数：** 路径处理相关操作，例如转换为 Posix 路径、比较路径是否相等、获取可读路径等。
    │   ├── shell.ts         # Shell 工具函数
    │   │   - **getWindowsTerminalConfig、getMacTerminalConfig、getLinuxTerminalConfig 函数：** 获取不同操作系统的终端配置。
    │   ├── single-completion-handler.ts # 单一完成处理
    │   │   - **singleCompletionHandler 函数：** 处理单一完成请求。
    │   ├── sound.ts         # 声音播放
    │   ├── xml-matcher.ts   # XML 匹配器
    │   │   - **XmlMatcher 类：** XML 匹配器，用于匹配 XML 标签。
    │   ├── __tests__/     # 通用工具函数测试
    └── webview-ui/          # Webview UI (VS Code 扩展界面)
        ├── .eslintrc.json   # ESLint 配置
        ├── .gitignore       # Git 忽略文件
        ├── .npmrc           # npm 配置
        ├── components.json  # 组件配置
        ├── index.html       # HTML 入口文件
        ├── jest.config.cjs  # Jest 配置
        ├── package-lock.json # npm 锁文件
        ├── package.json     # npm 包信息
        ├── tsconfig.json    # TypeScript 配置
        ├── vite.config.ts   # Vite 配置
        ├── public/         # 公共文件
        │   └── .gitkeep
        └── src/            # 源代码
            ├── App.tsx      # 应用主组件
            ├── index.css    # 全局样式
            ├── index.tsx    # 应用入口
            ├── preflight.css # 预设样式
            ├── setupTests.ts # 测试设置
            ├── vite-env.d.ts # Vite 环境变量定义
            ├── __mocks__/    # 模拟模块
            ├── __tests__/    # 测试文件
            ├── components/   # UI 组件
            ├── context/      # 上下文
            ├── i18n/         # 国际化
            ├── lib/          # 工具库
            ├── stories/      # Storybook 故事
            └── utils/        # 工具函数
```
