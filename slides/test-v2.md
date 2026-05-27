---
marp: true
theme: default
paginate: false
size: 16:9
style: |
  section { font-family: 'Hiragino Sans','Yu Gothic','Meiryo',sans-serif; font-size: 26px; color: #1f2937; background: #f8fafc; padding: 92px 64px 52px 64px; line-height: 1.45; }
  section h1 { position: absolute; top: 28px; left: 56px; right: 56px; margin: 0; padding: 0 0 10px 18px; font-size: 33px; color: #0f172a; border-left: 7px solid #2563eb; border-bottom: 1px solid #dbeafe; }
  section::before, section::after { display: none; }
  h2 { margin: 0 0 18px 0; font-size: 30px; color: #1e3a8a; }
  h3 { margin: 18px 0 8px 0; font-size: 23px; color: #334155; }
  p { margin: 0 0 15px 0; }
  ul, ol { margin-top: 8px; padding-left: 1.25em; }
  li { margin-bottom: 6px; }
  strong { color: #0f172a; font-weight: 700; }
  pre { margin: 12px 0; padding: 15px 18px; border-radius: 12px; background: #0f172a; color: #e5e7eb; font-size: 18px; line-height: 1.25; }
  code { font-size: 0.9em; font-family: 'SFMono-Regular', Consolas, 'Liberation Mono', Menlo, monospace; }
  table { width: 100%; font-size: 19px; border-collapse: collapse; }
  th { background: #e0f2fe; color: #0f172a; }
  th, td { padding: 8px 10px; border: 1px solid #cbd5e1; vertical-align: top; }
  tr:nth-child(even) td { background: #f1f5f9; }
  .lead { text-align: center; }
  .lead h1 { position: static; margin: 0 auto 24px auto; padding: 0; border: none; font-size: 48px; line-height: 1.2; }
  .lead h2 { font-size: 28px; color: #1e3a8a; }
  .subtitle { color: #475569; font-size: 23px; }
  .two-columns { display: grid; grid-template-columns: 1fr 1fr; gap: 34px; align-items: start; }
  .three-columns { display: grid; grid-template-columns: 1fr 1fr 1fr; gap: 18px; align-items: stretch; }
  .card { background: #ffffff; border: 1px solid #dbeafe; border-radius: 16px; padding: 18px 22px; box-shadow: 0 8px 24px rgba(15,23,42,0.06); }
  .message { margin-top: 18px; padding: 14px 18px; border-left: 6px solid #2563eb; border-radius: 12px; background: #eff6ff; color: #0f172a; font-weight: 700; font-size: 24px; }
  .note { padding: 12px 16px; border-radius: 12px; background: #f1f5f9; color: #334155; font-size: 21px; }
  .small { font-size: 21px; }
---
<!-- _class: lead -->

# OpenClaw入門

## Coding Agentではなく、常駐AI秘書のGatewayとして理解する

<div class="subtitle">社内エンジニア勉強会 / 20分</div>

---

# 今日のゴール

## OpenClawを「Claude Code / Codexっぽいもの」と誤解しない

この資料で扱うこと：

1. OpenClawとChatGPTの違い
2. OpenClawとCoding Agentの違い
3. OpenClawが「AI本体」ではなく「Gateway」であること
4. セッション、メモリ、チャネル、ツールの考え方
5. 常駐AI秘書としての使いどころ

<div class="message">OpenClawの本質は、Toolsを使えることではなく、AIを普段のチャット空間に常駐させるGatewayであること。</div>

---

# OpenClawとは

## セルフホスト型のAIアシスタント基盤

OpenClawは、普段使っているチャットアプリなどからAIに指示し、設定した権限や連携先に応じて作業させるための基盤。

ただし、ここで大事なのは「ファイル編集やコマンド実行ができる」ことだけではない。

OpenClawは、AIを以下のように扱う。

- 普段のチャットにいる
- 継続的な文脈を持つ
- 必要な外部サービスとつながる
- 自分の環境で動く

<div class="message">ChatGPTが「AIと会話する場所」なら、OpenClawは「自分の環境に常駐するAI秘書を作る仕組み」。</div>

---

# ChatGPTとの違い

<div class="two-columns">
<div class="card">

## ChatGPT

- 完成済みのAIサービス
- 画面を開いてAIと会話する
- 文章、コード、調査、要約、ファイル分析などを扱える
- すぐに使えることが強み

</div>
<div class="card">

## OpenClaw

- AIアシスタントを動かす基盤
- チャネル、スキル、メモリ、ツールを組み合わせる
- 自分の用途に合わせて設計する
- 常駐エージェントとして動かせる

</div>
</div>

<div class="message">ChatGPTは「会話・作業支援のサービス」。OpenClawは「実行型アシスタントを自分の環境に置く基盤」。</div>

---

# Coding Agentとの違い

## Toolsを使えること自体は差別化ポイントではない

Claude CodeやCodexも、指示に応じてファイルを読み、編集し、コマンドを実行できる。

したがって、OpenClawを次のように説明すると弱い。

> MCPやToolsやSkillsを使って、ファイル編集やコマンド実行ができます

それだけだと、聞き手は自然にこう感じる。

> それ、Claude CodeやCodexでよくない？

<div class="message">OpenClawの違いは「何が実行できるか」より、「どこに常駐し、どこから呼ばれ、どの文脈を持つか」。</div>

---

# OpenClawの位置づけ

## Coding Agentではなく、Personal Assistant Gateway

<div class="two-columns">
<div class="card">

## Claude Code / Codex

開発者が起動して使う、開発作業の相棒。

- リポジトリ中心
- CLI / IDE中心
- コード編集・テストが主戦場
- 人間が作業中に呼び出す

</div>
<div class="card">

## OpenClaw

普段のチャット空間にいる、常駐AI秘書。

- チャットチャネル中心
- Gateway中心
- セッションとメモリを持つ
- 必要なときに呼び出される

</div>
</div>

<div class="message">OpenClawは「開発環境の中のAI」ではなく、「日常のチャット空間にいるAI」。</div>

---

# OpenClawはAI本体ではない

## 重要なのはGatewayという考え方

OpenClaw自体は巨大なAIモデルではない。

外部LLM、ローカル/クラウドのモデル設定、チャットチャネル、ツール実行環境を組み合わせて、AIアシスタントを動かす基盤。

```mermaid
flowchart TD
    C[チャットアプリ / Dashboard / CLI / TUI] --> G[OpenClaw Gateway]
    G --> A[AIエージェント]
    A --> L[外部LLM / ローカルモデル]
    A --> T[ツール実行環境]
    T --> F[メール / カレンダー / ブラウザ / ファイル]
    A --> G
    G --> C
```

---

# Gatewayの役割

## セッション、ルーティング、チャネル接続を管理する制御プレーン

Gatewayが行うこと：

- SlackやDiscordなどから届いたメッセージを受け取る
- どの会話の続きかを判断する
- 適切なAIエージェント、スキル、ツールへ振り分ける
- 処理結果を元のチャットへ返す

<div class="message">OpenClawは、いろいろなチャットアプリをAIの受付窓口に変えるルーターのようなもの。</div>

---

# OpenClawの体験

## 新しいAIアプリを開くのではなく、普段の場所にAIがいる

```mermaid
flowchart LR
    U[ユーザー] --> S[Slack / Discord / Telegram / WhatsApp]
    S --> G[OpenClaw Gateway]
    G --> E[AIエージェント]
    E --> G
    G --> S
    S --> U
```

ユーザーは、ChatGPTの画面を開く代わりに、普段使っているチャット空間からAIに話しかける。

<div class="message">OpenClawの面白さは、AIが普段の会話空間に常駐する体験にある。</div>

---

# セッション

## AIが「どの会話の続きか」を判断する単位

OpenClawでは、会話はセッションとして管理される。

ChatGPTのチャットスレッドに近いが、複数のサービスから話しかけられるため、考え方が少し違う。

セッション分離の例：

- DM
- グループチャット
- ルーム / チャンネル
- 定期実行
- Webhook経由の処理

<div class="message">複数の入口があるからこそ、文脈が混ざらないようにセッション設計が重要。</div>

---

# チャネルとセッションの違い

## 「チャネル」はAIに接続する入口

OpenClawでいうチャネルは、AIに接続するサービスのこと。

例：

- Slack
- Discord
- Telegram
- WhatsApp
- Dashboard
- CLI / TUI

一方で、Slackのチャンネルは `#general` や `#project-a` のような、Slack内の会話場所を指す。

<div class="note">OpenClawでは、送信元チャネル、送信者、アカウントなどをもとにセッションを分けられる。</div>

---

# メモリ

## エージェントが作業を継続するためのノート

OpenClawのメモリは、エージェントが作業文脈を引き継ぐための仕組み。

保持するものの例：

- 長期記憶
- 日次メモ
- 作業ログ
- 会話履歴
- 以前の作業文脈

<div class="message">ChatGPTのメモリがユーザーの好みを覚える機能に寄りがちな一方、OpenClawのメモリは作業を継続するための文脈保持に近い。</div>

---

# ツールとスキル

## 差別化要素ではなく、Gatewayの先につながる作業手段

OpenClawでも、AIにただ返答させるだけでなく、ツールやスキルを通じて作業手段を与える。

ただし、これはOpenClaw固有の強みというより、AIエージェント全般に共通する要素。

OpenClawで大事なのは、これらを**常駐Gateway・チャネル・セッション・メモリ**の文脈で扱えること。

<div class="message">ToolsやSkillsは「手足」。OpenClawの本体は、それらを普段のチャット空間につなぐGateway。</div>

---

# OpenClawの強み

## 普段のチャットアプリから使える

ChatGPTのように専用画面を開くのではなく、仕事やコミュニティで使っているチャットアプリからそのままAIに話しかけられる。

<div class="message">AIが新しい場所にあるのではなく、普段の会話空間に入ってくる。</div>

---

# OpenClawの強み

## 常駐エージェントとして動かせる

OpenClawは、自分のPCやサーバー上で動かすAIエージェント基盤。

必要なときに質問するだけでなく、常に待機させておき、設定した範囲で作業させられる。

例：

- 通知を見る
- 予定を確認する
- メールやチャットに反応する
- 定期的な処理を行う

---

# OpenClawの強み

## 自分の環境に合わせて拡張できる

OpenClawでは、以下を組み合わせて自分用のAI秘書を作る。

- チャネル
- スキル
- ツール
- メモリ
- MCP
- Webhook

<div class="message">ChatGPTが完成されたAIサービスなら、OpenClawは自分のワークフローに合わせて組み立てるAI作業場。</div>

---

# OpenClawの強み

## チームやコミュニティでも使える

OpenClawは個人用のAI秘書としてだけでなく、Slack BotやDiscord Botのようなチャットエージェントとしても使える。

例：

- チームのSlackに置く
- DiscordコミュニティにAI Botとして参加させる
- チャットからAIに質問できるようにする

<div class="note">ただし、共有利用や複数人利用では追加のロックダウンが必要。</div>

---

# セキュリティ面の注意

## 常駐し、複数チャネルから呼ばれるため、権限設計が重要

OpenClawは、設定次第でメール、カレンダー、ファイル、ブラウザ、チャットアプリなどに触れる。

注意点：

- 誰が話しかけられるか
- どのチャンネルで使えるか
- どのツールを実行できるか
- 外部LLM APIへ何が送られるか
- ログ保存、APIキー管理、アクセス権限

<div class="message">安全に使うには、チャネル、ユーザー、ツール、実行範囲を絞り、必要に応じて人間の確認を挟む。</div>

---

# まとめ

## OpenClawとは何か

1. OpenClawは、AIに質問するツールというより、**常駐するAIエージェントを作るGateway**
2. Claude Code / Codexとの違いは、ファイル編集やコマンド実行ではなく、**チャット空間に常駐する体験**
3. 中心には**Gateway**があり、チャネル、セッション、ルーティング、ツール実行をつなぐ
4. 普段のチャットアプリや複数の入口から使える
5. 実際の作業環境に触れるため、権限と安全設計が重要

<div class="message">OpenClawは、開発環境の中のAIではなく、普段のチャット空間に常駐するAI秘書を作るための仕組み。</div>
