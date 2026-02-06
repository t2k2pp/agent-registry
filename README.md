# Agent Registry

AIエージェント向けスキル・サブエージェント・ワークフローの統合レジストリ。

## 構成

```
agent-registry/
├── core/                    # 普遍原則（言語・FW非依存）
│   └── skills/
├── domains/
│   ├── mobile/              # モバイル開発
│   │   ├── common/          # モバイル共通
│   │   ├── flutter/
│   │   ├── expo/
│   │   └── react-native/
│   ├── web/                 # Web開発（将来）
│   ├── desktop/             # デスクトップ開発（将来）
│   └── server/              # サーバー開発（将来）
├── fetcher/                  # スキル取得ツール
└── mcps/                     # MCP定義
```

## 使い方

### 1. fetcherスキルを配置

**Windows (PowerShell)**
```powershell
New-Item -ItemType Directory -Force -Path ".agent/skills/skill-fetcher"
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/t2k2pp/agent-registry/main/fetcher/SKILL.md" -OutFile ".agent/skills/skill-fetcher/SKILL.md"
```

**macOS / Linux**
```bash
mkdir -p .agent/skills/skill-fetcher
curl -sL "https://raw.githubusercontent.com/t2k2pp/agent-registry/main/fetcher/SKILL.md" -o .agent/skills/skill-fetcher/SKILL.md
```

### 2. AIアシスタントに指示

```
このプロジェクトで使用できるスキルを取得してください
```

## 階層構造

| 層 | 判断基準 |
|----|---------|
| `core/` | プラットフォーム・言語に一切依存しない原則 |
| `domains/mobile/common/` | モバイル特有だがFW非依存 |
| `domains/mobile/flutter/` | Flutter固有 |

## ライセンス

MIT
