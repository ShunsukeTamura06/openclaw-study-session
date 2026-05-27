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

## ChatGPTとの違いから、常駐AI秘書の仕組みを理解する

<div class="subtitle">社内エンジニア勉強会 / 20分</div>

---

# 今日のゴール

## OpenClawとは何かを、まず正しく捉える

この資料で扱うこと：

1. OpenClawとChatGPTの違い
2. OpenClawが「AI本体」ではなく「Gateway」であること
3. セッション、メモリ、チャネル、ツールの考え方
4. 常駐AI秘書として何ができるのか
5. 安全に使うための注意点

<div class="message">OpenClawは、AIに質問するアプリではなく、AIエージェントを自分の環境に常駐させる基盤。</div>

---

# OpenClawとは

## セルフホスト型のAIアシスタント基盤

OpenClawは、普段使っているチャットアプリなどからAIに指示し、設定した権限やツール連携に応じて作業させるための基盤。

扱えるものの例：

- チャットアプリからの指示
- メール、カレンダー
- ブラウザ
- ファイル操作
- 定期実行
- ツール連携

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

<div class="message">ChatGPTは「会話・作業支援のサービス」。OpenClawは「実行型エージェントの基盤」。</div>

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
    T --> F[ファイル / ブラウザ / メール / カレンダー]
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

## AIに「できること」を与える仕組み

OpenClawでは、AIにただ返答させるだけでなく、ツールやスキルを通じて作業手段を与える。

例：

- ファイルを読む
- ブラウザを扱う
- メールやカレンダーを見る
- 外部APIと連携する
- 定期的な処理を行う

<div class="message">LLMの知能に、現実の作業環境へ触れる手段を足すのがOpenClawの考え方。</div>

---

# OpenClawの強み

## 普段のチャットアプリから使える

ChatGPTのように専用画面を開くのではなく、仕事やコミュニティで使っているチャットアプリからそのままAIに話しかけられる。

<div class="message">AIが新しい場所にあるのではなく、普段の会話空間に入ってくる。</div>

---

# OpenClawの強み

## 常駐エージェントとして動かせる

OpenClawは、自分のPCやサーバー上で動かすAIエージェント基盤。

必要なときに質問するだけでなく、常に待機させておき、設定したツールや権限の範囲で作業させられる。

例：

- メール
- 予定
- 通知
- ブラウザ操作
- ファイル操作
- 定期実行

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

## 実際の作業環境に触れるため、権限設計が重要

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

1. OpenClawは、AIに質問するツールというより、**常駐するAIエージェントを作る基盤**
2. 中心には**Gateway**があり、チャネル、セッション、ルーティング、ツール実行をつなぐ
3. ChatGPTとの違いは、完成済みAIサービスではなく、**自分の環境に合わせて組み立てるAI作業場**であること
4. 普段のチャットアプリや複数の入口から使える
5. 実際の作業環境に触れるため、権限と安全設計が重要

<div class="message">OpenClawは、普段のチャット空間に常駐するAI秘書を、自分の環境で作るための仕組み。</div>
