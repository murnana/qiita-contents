---
title: '[Claude Code] Plan実行時にコンテキストをクリアする'
tags:
  - ClaudeCode
  - Claude
private: false
updated_at: '2026-09-20T13:58:59+09:00'
id: 10bff815db2bb904f116
organization_url_name: null
slide: false
ignorePublish: false
posting_campaign_uuid: null
agreed_posting_campaign_term: false
---

Claude Codeには、作業前に計画を立てる「Plan Mode」というモードが備わっています。

しかし計画を立てていくうちに、コンテキストがいっぱいになっていたりすることがあります。
また計画は別途、マークダウンファイルとして保存されています。そのため大抵の場合、計画実行前にコンテキストを掃除することは理にかなっています。

そこで、`settings.json`に以下の設定を加えると良いです。

```json
{
  "showClearContextOnPlanAccept": true
}
```

これを行うと、計画実行前に「Yes, and ...」のほかに「Yes, clear context and...」という選択肢が登場します。

「Yes, clear context and...」を選択すると、コンテキストをクリアして計画を進めます。

## 参考
- [Claude Code 設定リファレンス - showClearContextOnPlanAccept](https://code.claude.com/docs/en/settings-reference#showclearcontextonplanaccept)
