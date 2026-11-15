# AGENTS.md

at proto 向けの小さなアプリの design token。jgs.me 用の [laque](https://github.com/jgsme/laque) と同じ形で、中身は `src/theme.css` 1 枚。build は無い。

## 何が入っているか

- `@theme static`: 色 (`surface` / `surface-raised` / `line` / `ink` / `ink-secondary` / `ink-muted` / `accent` / `on-accent` / `danger`、色相 6 種 × 4 段)、font (`sans` = Figtree、`mono` = Roboto Mono)、文字サイズ (`title` / `brand` / `section` / `body` / `small` / `label` / `code`)、角丸 (`cell` / `sm` / `lg`)
- `@layer base`: dark の値 (`prefers-color-scheme` で `:root` の変数を差し替える)、`body` / `a` / `:focus-visible` / `code` の規則

入れないもの: アプリ固有の token (pix のグラフの地と文字、mote の暗い動画ページの `stage-*`)、幅 (`--container-*`)、font の読み込み (使う側が Google Fonts の `<link>` を置く)、class の定数や `cn` の設定。

## 使う側の約束

- `@import "tailwindcss"` の後、自分の `@theme` より前に import する。`--color-*` / `--font-*` / `--text-*` / `--radius-*` を `initial` で空にしてから定義するので、順序が逆だと使う側の token が消える
- tailwindcss v4 は使う側で入れる (peerDependencies)
- tailwind-merge を使うアプリは、`--text-*` と `--radius-*` の key を `extendTailwindMerge` に登録する。未登録だと `text-brand` (文字サイズ) と `text-ink` (色) を衝突と見なして片方を落とす。key を足したらアプリ側の登録も足す

## 値を変えるとき

- 値の元は pix の `apps/appview/src/styles/app.css` (2026-11 に写した)。pix のグラフ SVG (`svg.ts`) の配色と揃っているので、色相の段を変えるなら pix の SVG も合わせる
- 文字色は `surface` と `surface-raised` の上で 4.5:1 以上を保つ。dark も同じ

## リリース

`package.json` の `version` を上げて commit し、同じ値の tag (`v0.1.1` など) を push すると、GitHub Actions が npm の staged publishing に上げる。npmjs.com の Staged Packages タブで Approve するか、`npm stage approve <stage-id>` を打つと公開される (どちらも 2FA が要る)。CI の認証は trusted publishing で、token は使わない。

初回だけ、npm 側で package `livree` に trusted publisher (repo `jgsme/livree`、workflow `release.yml`) を登録する必要がある。
