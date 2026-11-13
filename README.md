# T3 Code 日本語化フォーク

[T3 Code](https://github.com/pingdotgg/t3code)（pingdotgg/t3code）の UI を日本語で使えるようにするための、非公式フォークです。

- **ベース**: 安定版タグ **v0.1.9**
- **ブランチ**: `local-ja`（このリポジトリのデフォルトブランチ）
- 本家の README は [README.upstream.md](./README.upstream.md) に原文のまま収録しています。ライセンスは本家に準拠します（MIT）。

## 本家との差分

v0.1.9 の上に、性質の異なる2層の変更が積まれています。

### 1. `[official-ja-settings]` — 本家の設定画面 i18n の先行取り込み

本家の言語切り替え機能（PR #31313）と日本語翻訳（PR #33164）は、v0.1.9 のタグ付け後に main へマージされたため、安定版には含まれていません。このフォークでは、その公式 PR を v0.1.9 へバックポートしています。

| コミット                           | 内容                                                                   |
| ---------------------------------- | ---------------------------------------------------------------------- |
| `[official-ja-settings]` #31313    | 言語切り替えの土台（`apps/web/src/i18n/`、設定画面の言語ピッカー）     |
| `[official-ja-settings]` #33164    | 日本語翻訳（`apps/web/src/i18n/translations/ja.json`、設定画面72項目） |
| `[official-ja-settings]` pnpm-lock | 上記に伴う依存関係のロックファイル解決分                               |

適用にあたっての手当ては最小限で、`ja.json` は公式ファイルと同一のものを使用しています。将来の安定版に公式の言語切り替えと日本語が含まれれば、この層はすべて外す予定です（手順は「アップデート方針」参照）。

なお、この翻訳の範囲は設定画面のみです。設定画面で言語を「日本語」に切り替えると、設定画面が日本語になります。

### 2. `[local-ja]` — UI 本体の日本語化

UI 本体の文字列は本家ではまだ言語ファイル化されていないため、コンポーネント内の英語文字列を直接日本語に置き換えています。対象は主要画面の表示範囲です（13ファイル・約85箇所）:

- サイドバー／スレッド一覧（「新規スレッド」、ピン留め／アクティブ／作業中の見出し、プロジェクト検索、右クリックメニュー）
- スレッドのアクションメニュー（ピン留め、名前を変更、アーカイブ、削除、コピー、スヌーズ、完了にする など）
- チャット入力欄のプレースホルダ
- 右パネルタブ（ブラウザ／ターミナル／ファイル／差分／プルリクエスト など）
- 差分画面の見出し（変更／未コミット）
- コマンドパレット、空状態の文言

全画面の網羅はしていません。英語が残っている箇所は、この層に置換を追加して対応します。

## セットアップ

前提: Git が使えること。

Node と pnpm の用意の仕方は2通りあります。どちらか一方を選んでください。

- **A. vp を使う**（本家と同じ手順）: `vp` がリポジトリ指定のバージョン（Node 26）を用意するため、Node の別途インストールは不要です。
- **B. vp を入れずに nvm を使う**（macOS / Linux）: 他のプロジェクトで nvm を使っていて、Node の管理ツールを nvm に一本化したい場合の手順です。

vp はインストールすると、他に Node が見つからないときの予備の `node` / `npm` を PATH に追加します。nvm が有効になっていないシェルでこの予備の Node が使われると、nvm 前提のビルドスクリプトが `nvm is not compatible with the "npm_config_prefix" environment variable` で止まることがあります。nvm を併用している場合は B を推奨します。

### 1-A. vp（Vite Plus）のインストール

Windows（PowerShell）:

```powershell
irm https://vite.plus/ps1 | iex
```

macOS / Linux:

```sh
curl -fsSL https://vite.plus | bash
```

### 1-B. nvm で Node と pnpm を用意する

[nvm](https://github.com/nvm-sh/nvm) をインストール済みであることが前提です。Node のバージョンはリポジトリの `.nvmrc`（`package.json` の `engines.node` と同じ値）に合わせます。

```sh
nvm install 26.12.1
nvm use 26.12.1
npm install -g pnpm@12.14.0   # package.json の packageManager と同じバージョン
```

Node 25 以降は corepack が同梱されていません。また、corepack 0.34 系は pnpm 12 を起動できない（`Cannot find module .../pnpm/12.14.0/bin/pnpm.cjs`）ため、pnpm は npm で直接インストールします。

`vp` コマンド自体はリポジトリの devDependencies（`vite-plus`）に含まれており、`pnpm` のスクリプト経由で実行すると `node_modules/.bin/vp` が使われます。グローバルな vp のインストールは不要です。

### 2. clone

```sh
git clone https://github.com/nagisora/t3code.git
cd t3code
```

デフォルトブランチが `local-ja` のため、チェックアウトは不要です。B の場合、clone したディレクトリで `nvm use` を実行すると `.nvmrc` のバージョンに切り替わります。

### 3. 依存関係のインストール

A（vp）:

```sh
vp i
```

B（nvm + pnpm）:

```sh
pnpm install
```

### 4. 起動

Web 版（サーバー＋Web UI）:

```sh
vp run dev      # A
pnpm dev        # B
```

デスクトップ版（Electron）:

```sh
vp run dev:desktop   # A
pnpm dev:desktop     # B
```

README やエラーメッセージに `vp run <script>` と書かれている箇所は、B では `pnpm <script>` と読み替えてください。

Web 版は起動時にペアリング URL が表示されます。新しいブラウザでは素の URL だけでは認証されないため、表示されたペアリング URL を開いてください。

### 5. 設定画面を日本語にする

設定（Settings）→ 一般（General）→ 言語（Language）で「日本語」を選択します。

### 6. エージェント CLI（別途必要）

T3 Code 自体は AI モデルを持たず、既存のエージェント CLI を束ねるためのツールです。利用するエージェント（例: Claude Code）のインストールとログインが別途必要です。各エージェントの公式手順に従ってください。

## アップデート方針

- ベースは安定版タグに追随します（Nightly は追いません）。新しい安定版が出たら `local-ja` をそのタグへ rebase します。
- rebase の際、`[official-ja-settings]` 層と `[local-ja]` 層で競合しうる箇所が分かれているため、層ごとに対処できます。
- 取り込み先の安定版に公式の言語切り替えと日本語が含まれている場合（`apps/web/src/i18n/translations/ja.json` が存在し、`apps/web/gt.config.json` の `locales` に `"ja"` がある場合）は、`[official-ja-settings]` の3コミットを drop してこの層を外します。
- rebase 後に `package.json` の `engines.node` が変わっていたら、`.nvmrc` と README の「1-B」のバージョンを合わせて更新します。`packageManager` の pnpm バージョンが変わった場合も「1-B」を更新します。

## 注意点

- 本家は 0.1.x の開発途上のソフトウェアです。破壊的変更や不具合が含まれる場合があります。
- T3 Code にはサンドボックス機能がなく、エージェントは連携した CLI の権限でそのまま動作します。実行させる範囲の管理は、利用する環境に合わせて行ってください。
- このリポジトリは公開されています。認証情報や個人情報などの秘密情報をコミットしないでください。
- 本家の CONTRIBUTING は、チーム外からの新機能 PR を受け付けない方針です。このフォークの変更を本家へ PR する前提にはしていません。
