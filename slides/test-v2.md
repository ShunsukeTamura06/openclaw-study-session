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
  table { width: 100%; font-size: 19px; border-collapse: collapse; }
  th { background: #e0f2fe; color: #0f172a; }
  th, td { padding: 8px 10px; border: 1px solid #cbd5e1; vertical-align: top; }
  tr:nth-child(even) td { background: #f1f5f9; }
  .lead { text-align: center; }
  .lead h1 { position: static; margin: 0 auto 24px auto; padding: 0; border: none; font-size: 46px; line-height: 1.2; }
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

## 社内オンプレPoCで見えてきた、AIエージェント基盤の使いどころ

<div class="subtitle">社内エンジニア勉強会 / 20分</div>

---

# 今日のゴール

## OpenClawを構造で理解する

今日持ち帰ってほしいことは3つです。

1. **OpenClawはAIモデル本体ではなく、Gatewayを中心にしたエージェント基盤**である
2. **Claude Code / Codexとは主戦場が違う**。開発専用というより、チャネル・ツール・社内情報源をつなぐ基盤
3. 社内では、**GitLab・共有フォルダ・Obsidian・Symphonyに接続したときに価値が出る**

<div class="message">キーワードは「常駐」「Gateway」「社内情報源への接続」。</div>

---

# ChatGPTと何が違うのか

<div class="two-columns">
<div class="card">

## ChatGPT

- AIと会話する場所
- すぐ使える完成済みサービス
- 文章作成、要約、調査、コード支援に強い
- 基本はユーザーが画面を開いて依頼する

</div>
<div class="card">

## OpenClaw

- 自分の環境にAIを常駐させる仕組み
- チャット、CLI、Dashboard、外部チャネルから使う
- ツールや権限を設計して動かす
- 普段の業務環境にAIを入れる

</div>
</div>

<div class="message">ChatGPTは「AIがいるアプリ」。OpenClawは「自分の環境にAI秘書を作る基盤」。</div>

---

# OpenClawの基本イメージ

## 中心にいるのはGateway

```text
利用者
  ↓
Dashboard / CLI / TUI / Symphony / Slack / Discord
  ↓
OpenClaw Gateway
  ↓
Agent / Session / Memory / Skills / Tools
  ↓
LLM API / ローカルモデル / MCP / 社内API / ファイル / Shell
```

<div class="message">OpenClawは、複数の入口と複数の道具を束ねて、AIエージェントに渡す制御プレーン。</div>

---

# Gatewayとは何か

## OpenClawをCLIツールだけだと思うと分かりづらい

Gatewayは、OpenClawの中心で常駐するプロセスです。

主な役割：

- **どこから来た依頼か**を受け取る
- **どのセッションの続きか**を管理する
- **どのエージェント・ツールに渡すか**を決める
- **処理結果を元のチャネルへ返す**

<div class="note">OpenClawは「ターミナルでAIと会話するツール」ではなく、Dashboard・CLI・TUI・チャット連携がGatewayにつながる構成。</div>

---

# 主要要素

<div class="three-columns">
<div class="card">

## Channel

AIへの入口。

- Dashboard
- CLI / TUI
- Symphony
- Slack / Discord
- Webhook

</div>
<div class="card">

## Session / Memory

文脈を保つ仕組み。

- 会話の続き
- 作業履歴
- 長期メモ
- 日次ログ

</div>
<div class="card">

## Tools / Skills

AIの手足。

- File
- Shell
- Browser
- MCP
- 社内API

</div>
</div>

<div class="message">入口、文脈、道具を組み合わせて、自分たち用のAIエージェントを作る。</div>

---

# セッション：会話の混線を防ぐ単位

## チャット連携では特に重要

OpenClawでは、会話や作業の文脈を**セッション**として扱います。

たとえば、Symphony連携を考えると：

- 個人DMの会話
- グループチャットの会話
- ルームごとの会話
- 定期実行の会話
- Webhook経由の会話

<div class="message">社内利用では「誰が・どのチャットで・どの目的で話しているか」を分ける設計が大事。</div>

---

# メモリ：作業を継続させるノート

## 単なるプロフィール記憶ではない

OpenClawのメモリは、AI秘書が作業を継続するためのノートに近いです。

使い方のイメージ：

- PoCの進捗を覚える
- 日次の作業ログを残す
- 以前調べた結論を再利用する
- よく使う社内システムや手順を保持する

<div class="note">Obsidian VaultやGitLab Issueと組み合わせることで、人間の検討履歴とAIの作業履歴をつなぎやすくなる。</div>

---

# 始め方：まずは動かして構造を見る

## 最小の確認ポイント

```text
1. インストール
2. オンボーディング
3. Gateway起動確認
4. Dashboardを開く
5. TUI / CLIで会話する
6. チャネルやツールを追加する
```

確認すること：

- Gatewayが動いているか
- Dashboardから会話できるか
- どのモデルを使っているか
- セッションがどう管理されるか
- ツール実行がどこまで許可されているか

---

# Dashboard / CLI / TUI の位置づけ

| 入口 | 役割 | 社内PoCでの見方 |
|---|---|---|
| Dashboard | 管理画面・チャット・設定確認 | 状態確認、デモ、実行承認の確認に向く |
| TUI | ターミナル上の対話UI | 開発者が手元で試すのに向く |
| CLI | 1回限りの実行・スクリプト連携 | Symphony Bridgeなどの自動連携に向く |
| Chat Channel | Slack/Symphony等から利用 | 利用者体験として一番自然 |

<div class="message">社内利用で重要なのは、専用画面を開かず、普段のチャットやワークフローから使えること。</div>

---

# Claude Code / Codex とは何が違うのか

| 観点 | Claude Code / Codex | OpenClaw |
|---|---|---|
| 主戦場 | コーディング | 常駐型エージェント基盤 |
| 中心 | リポジトリ・作業ディレクトリ | Gateway・チャネル・セッション |
| 得意 | コード読解、修正、テスト | 複数入口・複数ツール・社内情報源の接続 |
| 使い方 | 開発者がCLI/IDEで使う | チャット、Dashboard、CLI、Webhookから使う |
| 社内価値 | 個人の開発生産性 | チームの業務文脈への接続 |

---

# 比較の結論

## 優劣ではなく、役割が違う

<div class="two-columns">
<div class="card">

## Claude Code / Codex

**コードを書く・直す**ためのエージェント。

- 既存コードの理解
- バグ修正
- テスト実行
- 差分作成

</div>
<div class="card">

## OpenClaw

**社内環境につないで動く**ためのエージェント基盤。

- チャット連携
- 定期実行
- 社内情報源の横断
- ツール・権限設計

</div>
</div>

<div class="message">コーディング単体ならClaude Code / Codex。社内情報源をつないだ常駐エージェントならOpenClaw。</div>

---

# 社内PoCで見たい価値

## 一般的なデモではなく、社内にしかない情報を扱う

世の中のデモ：

- ログを読む
- コードを修正する
- Todoアプリを作る
- Webで調べる

社内で本当に見たいもの：

- GitLabのREADME・Issue・Commitを読む
- 共有フォルダの資料を探す
- Obsidian Vaultの検討メモを整理する
- Symphonyから依頼して、元のチャットに返す

<div class="message">OpenClawの価値は、社内情報に接続した瞬間に一段上がる。</div>

---

# 社内システム連携のイメージ

```text
Symphony
  ↓ メッセージ受信
Python Bridge
  ↓ openclaw agent --message
OpenClaw Gateway / Agent
  ↓
Tools
  ├─ GitLab
  ├─ Shared Folder
  ├─ Obsidian Vault
  └─ Local Scripts
  ↓
Python Bridge
  ↓ send_message
Symphony
```

<div class="note">ポイントは、OpenClawを「チャットに表示するだけ」ではなく、社内情報源を調査して、結果を元のチャットへ戻すこと。</div>

---

# ユースケース1：GitLab調査

## 新しく担当するシステムを理解する

依頼例：

```text
この機能に関係するリポジトリと最近の変更を調べて。
README、Issue、最近のcommitを見て、影響範囲と次に確認すべき点をまとめて。
```

出力イメージ：

- 関連リポジトリ
- 重要なREADME記述
- 最近の変更内容
- 影響しそうなファイル
- 未解決Issue
- 次に確認すべきログや設定

---

# ユースケース2：共有フォルダ探索

## 「あの資料どこだっけ」を減らす

依頼例：

```text
米国雇用統計の説明に使えそうな過去資料を探して。
最新版、流用できそうな図表、勉強会向けに使える説明を分けてまとめて。
```

価値：

- ファイル名だけでなく中身を見て探せる
- 最新版と古い版を区別できる
- 流用しやすい資料を候補にできる
- 探す時間を減らせる

---

# ユースケース3：Obsidian Vault整理

## 思考履歴を再利用する

依頼例：

```text
OpenClaw PoCに関する過去メモを整理して。
現在の構成、詰まった点、意思決定の理由、次にやることをまとめて。
```

価値：

- 検討メモが埋もれにくい
- 途中の失敗や判断理由を再利用できる
- 新規参加者への説明資料に変換できる
- GitLabや共有フォルダと合わせて文脈を復元できる

---

# デモ案：PoC状況を横断整理する

## OpenClaw自身に、OpenClaw PoCを説明させる

```text
OpenClaw PoCの現状を、新しく参加した人向けに説明できるようにまとめて。

確認対象：
- GitLabのREADME、Issue、最近のcommit
- Obsidian Vault内の設計メモ
- 共有フォルダ内の説明資料

観点：
1. 目的
2. 現在の構成
3. できていること
4. 未解決課題
5. 次にやること
```

---

# デモで見せたいポイント

## すごいのは文章生成ではなく、文脈の再構成

OpenClawがやっていること：

1. 複数の社内情報源を見る
2. 最新情報と過去の検討経緯を分ける
3. 目的・構成・課題・次アクションに整理する
4. 新規参加者向けに説明可能な形へ変換する

<div class="message">これは検索ではなく、社内に散らばった業務文脈を復元する作業。</div>

---

# 社内利用での前提

## 今回は現実的なPoCに寄せる

今回の前提：

- OpenClawはコンテナ内の閉鎖環境で稼働
- 接続先は限定する
- LLM APIへのアップロードは了承済み
- 当部は投資部門であり、顧客情報を直接扱う環境ではない
- まずは読み取り系を中心に検証する

<div class="note">複数人利用や強い権限を持つツール実行では、ユーザー・チャネル・ツール・実行範囲の制限が必要。</div>

---

# どこから始めるべきか

## 最初に任せるのは「判断」ではなく「調査と整理」

| 始めやすい | 後回し |
|---|---|
| 読み取り専用 | 本番変更 |
| 調査補助 | 自動デプロイ |
| 要約 | Git push |
| 影響範囲整理 | 権限の強い操作 |
| オンボーディング資料作成 | 重要判断の自動化 |

<div class="message">まずは、低リスクで効果が見えやすい「読む・探す・まとめる」から始める。</div>

---

# まとめ

今日の結論：

1. OpenClawは、AIモデル本体ではなく**Gateway中心の常駐エージェント基盤**
2. ChatGPTやClaude Code / Codexとは違い、**チャネル・セッション・ツール・社内情報源をつなぐ設計**が主役
3. 社内では、GitLab・共有フォルダ・Obsidian・Symphonyを横断して、**業務文脈を再構成する用途**が強い
4. 最初は、読み取り専用の調査・整理・オンボーディング支援から始めるのが現実的

<div class="message">OpenClawは、AIに仕事を丸投げする道具ではなく、人間の判断を助けるために社内情報をつなぐ作業基盤。</div>

---

# 議論したいこと

## 自分たちの業務なら、何を読ませたいか

候補：

- GitLab
- 共有フォルダ
- 手順書
- Obsidian Vault
- 過去の障害対応メモ
- 定例資料
- 部内スクリプト
- Symphonyの特定ルーム

<div class="message">まず読み取り専用で任せるなら、どの作業が安全で効果が大きいか。</div>
