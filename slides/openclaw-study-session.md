---
marp: true
theme: default
paginate: false
size: 16:9
style: |
  /* ===== Base ===== */
  section {
    position: relative;
    font-family: 'Hiragino Sans', 'Yu Gothic', 'Meiryo', sans-serif;
    font-size: 27px;
    line-height: 1.45;
    color: #1f2937;
    background: #f8fafc;
    padding: 92px 64px 56px 64px;
  }

  section > * {
    position: relative;
    z-index: 1;
  }

  /* ===== Fixed slide title ===== */
  section h1 {
    position: absolute;
    top: 28px;
    left: 56px;
    right: 56px;
    margin: 0;
    padding: 0 0 10px 18px;
    font-size: 34px;
    line-height: 1.15;
    font-weight: 700;
    color: #0f172a;
    border-left: 7px solid #3b82f6;
    border-bottom: 1px solid #dbeafe;
  }

  /* ===== Hide page number ===== */
  section::after,
  section::before {
    display: none;
  }

  /* ===== Headings ===== */
  h2 {
    margin-top: 0;
    margin-bottom: 18px;
    font-size: 30px;
    line-height: 1.25;
    font-weight: 700;
    color: #1e3a8a;
  }

  h3 {
    margin-top: 20px;
    margin-bottom: 10px;
    font-size: 24px;
    line-height: 1.25;
    font-weight: 700;
    color: #334155;
  }

  p {
    margin: 0 0 16px 0;
  }

  ul, ol {
    margin-top: 8px;
    margin-bottom: 12px;
    padding-left: 1.25em;
    line-height: 1.4;
  }

  li {
    margin-bottom: 6px;
  }

  strong {
    color: #0f172a;
    font-weight: 700;
  }

  pre {
    margin: 12px 0;
    padding: 16px 18px;
    border-radius: 12px;
    background: #0f172a;
    color: #e5e7eb;
    font-size: 18px;
    line-height: 1.25;
  }

  code {
    font-size: 0.9em;
    font-family: 'SFMono-Regular', Consolas, 'Liberation Mono', Menlo, monospace;
  }

  pre code {
    font-size: inherit;
  }

  table {
    width: 100%;
    font-size: 19px;
    border-collapse: collapse;
  }

  th {
    background: #e0f2fe;
    color: #0f172a;
    font-weight: 700;
  }

  th, td {
    padding: 8px 10px;
    border: 1px solid #cbd5e1;
  }

  tr:nth-child(even) td {
    background: #f1f5f9;
  }

  .small {
    font-size: 21px;
  }

  .x-small {
    font-size: 18px;
  }

  .center {
    text-align: center;
  }

  .lead {
    text-align: center;
  }

  .lead h1 {
    position: static;
    margin: 0 auto 24px auto;
    padding: 0;
    border: none;
    font-size: 46px;
    line-height: 1.2;
    color: #0f172a;
  }

  .lead h2 {
    font-size: 28px;
    color: #1e3a8a;
    font-weight: 600;
  }

  .two-columns {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 34px;
    align-items: start;
  }

  .card {
    background: #ffffff;
    border: 1px solid #dbeafe;
    border-radius: 16px;
    padding: 20px 24px;
    box-shadow: 0 8px 24px rgba(15, 23, 42, 0.06);
  }

  .message {
    margin-top: 18px;
    padding: 14px 18px;
    border-left: 6px solid #3b82f6;
    border-radius: 12px;
    background: #eff6ff;
    color: #0f172a;
    font-weight: 700;
    font-size: 24px;
  }

  .note {
    padding: 12px 16px;
    border-radius: 12px;
    background: #f1f5f9;
    color: #334155;
    font-size: 22px;
  }
---
<!-- _class: lead -->

# OpenClaw PoCで見えてきた、社内AIエージェント活用の現実

## チャットAIから、社内情報に接続された作業実行エージェントへ

<div class="note">
OpenClaw = LLMにファイル読み・コマンド実行・社内ツール接続といった「手足」を与え、社内チャネルから呼び出せるようにするエージェント基盤。
</div>

社内エンジニア勉強会 / 20分

---

# 今日伝えたいこと

OpenClawは、単にLLMと会話するツールではない。

**社内GitLab、共有フォルダ、Obsidian Vault、Symphonyなどに接続することで、社内に散らばった情報を横断し、業務文脈を持って作業を支援できるエージェント基盤**として使える。

まずは、**読み取り専用・調査補助・整理業務**から始めるのが現実的。

---

# 背景：AI活用は「聞く」から「任せる」へ

| 従来のAI活用 | 最近のAI活用 |
|---|---|
| 質問する | ファイルを読ませる |
| 文章を書かせる | コマンドを実行させる |
| コード案を出させる | リポジトリを調査させる |
| 調査結果を要約させる | 社内ツールと連携させる |

<br>

**ChatGPTに聞く** から、**AIに作業を手伝わせる** へ。

---

# OpenClawとは何か

<div class="two-columns">

<div>

## LLMに「手足」を与える基盤

OpenClawは、LLMに対して以下のような操作手段を与える。

- ファイルを読む
- コマンドを実行する
- 社内ツールに接続する
- チャットから依頼を受ける
- 結果を返す

</div>

<div>

```text
利用者
  ↓
Chat / Web UI / Symphony
  ↓
OpenClaw
  ↓
LLM API + Tools
  ├─ Files
  ├─ Shell
  ├─ GitLab
  ├─ Shared Folder
  └─ Obsidian Vault
```

</div>

</div>

<div class="message">
OpenClawは、AIの脳ではなく、AIに手足と作業机を与える仕組み。
</div>

---

# Claude Code / Codex と何が違うのか

| 観点 | Claude Code / Codex | OpenClaw |
|---|---|---|
| 設計の主眼 | 開発者個人のコーディング体験 | 社内チャネル常駐の業務エージェント |
| 想定利用者 | 開発者（IDE/CLIから） | 業務担当者（チャット/Web UIから） |
| 拡張方式 | MCP等で接続可能（個人セットアップ前提） | コンテナ常駐＋社内チャネル接続を前提に設計 |
| 主な呼び出し導線 | ターミナル・IDE | Symphony等の社内チャット、Web UI |
| 強み | コーディング体験の完成度 | 閉域運用・チャネル統合・常駐化のしやすさ |

<div class="note">
両者ともMCP等で社内ツール接続自体は可能。違いは「何を前提に設計されているか」。
</div>

---

# 比較の結論

## 優劣ではなく、役割が違う

**Claude Code / Codex**
- 開発者個人向けに磨かれた coding agent
- IDE/CLIから、リポジトリを読んで修正・テスト実行
- 個人セットアップを前提に拡張する設計

**OpenClaw**
- 社内チャネル常駐を前提とした業務エージェント基盤
- コンテナで閉域運用し、社内チャットから呼び出す
- 複数情報源を横断した調査・整理に向く

<br>

**開発者個人のコーディング体験なら Claude Code / Codex。社内チャネルに常駐させる業務エージェントなら OpenClaw。**

---

# 一般的なOpenClawの使い方

## まず分かりやすいユースケース

1. **ログ解析**
   - エラーログを読む
   - 原因候補を出す
   - 関連ファイルを探す

2. **コード調査**
   - リポジトリ構成を読む
   - 関連実装を探す
   - 影響範囲を整理する

3. **作業補助**
   - コマンド実行
   - テスト実行
   - 設定ファイル確認

---

# ただし、社内で本当に効くのはここ

## 社内情報に接続したとき

社内にしかない情報：

- GitLab
- 共有フォルダ
- Obsidian Vault
- Symphony
- 手順書
- 過去の調査メモ
- 部内スクリプト
- 定例資料

<br>

**OpenClawは、社内情報に接続して初めて本領を発揮する。**

---

# 社内特有のユースケース案

| ユースケース | できること |
|---|---|
| GitLab調査エージェント | 関連リポジトリ、最近の変更、影響範囲を整理 |
| 共有フォルダ探索エージェント | 過去資料、最新版、流用可能な資料を探す |
| Obsidian Vault整理エージェント | 過去メモ、検討経緯、未解決課題を整理 |
| Symphony連携エージェント | チャットから依頼し、元のチャットへ回答を返す |

<br>

ポイントは、**情報検索ではなく、業務文脈の再構成**。

---

# 今回のPoC構成イメージ

```text
Symphony
  ↓
Python Bridge
  ↓
OpenClaw Container
  ↓
LLM API

OpenClaw Container
  ├─ GitLab
  ├─ Shared Folder
  ├─ Obsidian Vault
  └─ Local Tools / Scripts
```

<div class="small">

前提：
- OpenClawはコンテナ内の閉鎖環境で稼働
- LLM APIへのアップロードは了承済み
- 当部は投資部門であり、顧客情報を直接扱う環境ではない
- まずは読み取り系を中心に検証

</div>

---

# ライブデモ：社内情報を横断してPoC状況を整理する

<div class="message">
これからOpenClawに以下のプロンプトを実際に投げます。
</div>

## 入力プロンプト

```text
OpenClaw PoCの現状を、新しく参加した人向けに説明できるようにまとめてください。

以下の情報源を確認してください。
- GitLabのREADME、Issue、最近のcommit
- Obsidian Vault内の設計メモ
- 共有フォルダ内の説明資料

まとめる観点は以下です。
1. PoCの目的
2. 現在の構成
3. できていること
4. 未解決課題
5. 次にやるべきこと
```

---

# 想定アウトプット（デモが失敗したとき用のバックアップ）

<div class="note">
ライブデモがうまくいかなかった場合に備えて、過去実行時のアウトプット例をここに置いておきます。
</div>

```text
OpenClaw PoC 現状整理

1. 目的
- 社内閉域環境でAIエージェント基盤を検証する
- Symphony等の社内チャットから利用できるようにする

2. 現在の構成
- OpenClawはコンテナ内で稼働
- Python BridgeがSymphonyとOpenClawを接続
- GitLab、共有フォルダ、Obsidian Vaultとの連携を検証中

3. 未解決課題
- GitLab連携方式の整理
- 共有フォルダ検索の精度向上
- 長時間タスクの扱い
```

**単なる要約ではなく、複数情報源から業務文脈を復元する。**

---

# どこから始めるべきか

## いきなり「全部任せる」は危険

| 始めやすい領域 | 後回しにした方がよい領域 |
|---|---|
| 読み取り専用 | 本番環境の変更 |
| 調査補助 | 自動デプロイ |
| 要約 | Git push |
| 影響範囲整理 | 権限の強い操作 |
| 手順書作成 | 外部送信を伴う処理 |
| 新規参加者向けオンボーディング | 重要判断の自動化 |

<br>

**最初に任せるべきなのは、判断や変更ではなく、調査と整理。**

---

# まとめ

1. OpenClawは、チャットAIではなく**作業実行エージェント基盤**
2. 社内での価値は、**GitLab・共有フォルダ・Obsidian・Symphony**などに接続したときに大きくなる
3. 特に、情報源を横断して**業務文脈を整理する用途**と相性がよい
4. 最初は**読み取り専用・調査補助・オンボーディング支援**から始めるのが現実的

<br>

**OpenClawは、AIに仕事を丸投げするためのものではなく、社内に散らばった情報をつなぎ、人間の判断を助けるための作業基盤として使うのが現実的。**

---

# 議論したいこと

皆さんの業務で、AIが読めるようになると嬉しい社内情報源は何か？

- GitLab？
- 共有フォルダ？
- 手順書？
- Obsidian？
- 過去の障害対応メモ？
- 定例資料？

<br>

**まず読み取り専用で任せるなら、どの作業が安全で効果が大きいか。**
