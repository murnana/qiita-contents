---
title: '[Claude Code] !<command> で実行した後に出てくるメッセージを止める'
tags:
  - ClaudeCode
  - Claude
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
posting_campaign_uuid: null
agreed_posting_campaign_term: false
---

Claude Codeでは、`!<任意のコマンド>`というように、`!`を入れることでシェルコマンドを直接実行できます。

デフォルトでは、このコマンドの実行後にClaude Codeによる応答があります。
しかし、コンテキストを節約したいときや、コマンドを順番に実行した後で応答してほしい時には必要ないと感じます。

そこで、`settings.json`に以下の設定を加えると良いです。

```json
{
  "respondToBashCommands": false
}
```

## 参考
- [All settings - Claude Code Docs](https://code.claude.com/docs/en/settings-reference#respondtobashcommands)
