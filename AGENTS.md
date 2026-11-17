# AGENTS.md

月の形よみ：月の絵や空の場面を見て、月の名前・見える時刻と方角・昔の呼び名を当てる1日5問の学習ゲーム。正解したら「なぜそうなる？」（光を受けて光る・太陽との位置・地球が回る・毎日おくれる・昔の暦）も選ぶ。`index.html` と `data.js`（問題の種類）を GitHub Pages で公開する。

## 準備（最初に一度）

```bash
bash scripts/setup.sh   # Node.js（版は .nvmrc）を確かめ、検査の依存とブラウザを入れる。何度実行してもよい
```

Claude Code のクラウドセッションでは `.claude/settings.json` のフックが自動で実行する。

## push する前の確認

```bash
node qa/run.mjs   # 問題（25種類×3000問：選び肢・答えは1つ・答えと理由と出典が答えの表どおり・時刻は平均の南中から計算・月の絵の光る側）・今日の5問と上級（3年分）・とことん・通しで遊ぶ・続きから・2段目・復習・幅320px・PWA・引き継ぎ・見やすさ（CI の QA と同じ）
```

## このリポジトリの決まり

- 中身は国立天文台 暦計算室（暦Wiki・こよみ用語解説 <https://eco.mtk.nao.ac.jp/koyomi/>）で確かめたことだけ（2026-11-17 に読んだ。要点は `data.js` の `SRC`）。種類を足すときも資料を読み、`src` に付け、`qa/run.mjs` の `TB`（別に書いた答えの表）にも足す。時刻の答えは `TIMEQ`（南中の平均の時刻±6時間）で計算して照らす。
- 時刻は平均のおよその値（新月 正午・上弦 夕方6時・満月 0時・下弦 朝6時に南中。のぼるのは約6時間前、しずむのは約6時間後）。見え方は日本（北半球）で、南を向いて見たとき（満ちていく月は右、欠けていく月は左が光る）。
- 扱うのは月の満ち欠け・見える時刻と方角・昔の呼び名だけ。惑星（太陽系くらべ taiyokei-kurabe）・星座（星座つなぎ seiza-tsunagi）・季節の暦（二十四節気 nijushi-sekki）・日食と月食とは重ねない（QA が見る）。
- 公式の情報の代わりにしない：遊び方と「このゲームについて」と README に、時刻は平均のおよその値で、その日の正しい月の出入りは国立天文台 暦計算室で確かめることを置く（QA が見る）。
- 問題は、やさしいが選び肢3つ、ふつう・むずかしいが4つ（種類ごとの5つの組 `POOLS` などから）。2回目のヒントはまちがいを消して2つにしぼり、見分けの言葉（`key`）も見せる。2段目は「なぜそうなる？」（5つ）で、種類（`grp`）で決まる。
- 今日の5問（やさしい2・ふつう2・むずかしい1、種類は重ねない）・上級（むずかしい3、今日のむずかしいと別）は日本時間の日付の種で決まり、だれでも同じ。`Math.random()` は練習・とことんの種だけ。乱数は `sort` の比較の中で引かない。`KINDS`・言い回しの並びは最後に足すだけ（QA の `PIN` が第1回の1問目を見る）。`data.js` を変える PR は自動ではマージされない（`hold_paths`）。
- ★はまちがい＋ヒントの数（0で★3・1で★2・2以上で★1）。上級はヒントなし。時間制限なし。
- 好きなだけ遊べる：チケット・ライフ・回数制限・🔒で開くモードを入れない（shared-workflows `routines/game-dev.md` F1〜F7。QA が見る）。とことん（`runs.e`）はまちがえても続き、「ここで降りる」で終わる。
- 1問を深くする：`depth.js` と `qa/depth-check.mjs` は shared-workflows `template/` の写しで、ここでは直さない。自信あり・2段目「なぜそうなる？」・復習（1→3→7日後）。記録 `tkk-v1`・文字 `tkk-fs`・復習 `tkk-depth`。
- 共有は遊ぶ人が押したときに開くリンクだけ。シェアの文に問題・答えを出さない。感想は Issue フォーム（`.github/ISSUE_TEMPLATE/feedback.yml`）を開くだけ。応援は Ko-fi へのただのリンクだけ。
- 外へ通信しない（CSP の `connect-src 'self'` を広げない）。見やすさ（`<!-- a11y:begin -->`〜`end`・`qa/a11y.mjs`）と引き継ぎ（`transfer.js`・`qa/transfer-check.mjs`）は shared-workflows `template/` の写しで、ここでは直さない。
- 画面の土台は憲法のしくみ（kenpo-shikumi）から写した。写し元の言葉が残っていないかを QA が見る。
- PWA：画面のファイルを足したら `sw.js` の `SHELL` にも足し、中身を大きく変えたら `CACHE` の版を上げる。アイコンは `node tools/make-icons.cjs`、`og.png` は `node tools/make-og.cjs`。
- 公開リポジトリなので、個人情報・鍵を入れない。

## 公開

- GitHub Pages は `main` のルート（`/`）を公開する。PR の QA と Secret scan が ✅ になると `automerge.yml` が自動でマージする。止めたいときは下書きにするか、ラベル「自動マージしない」を付ける。
- `sw.js`・`manifest.webmanifest`・`data.js` を変える PR は自動ではマージされない（`hold_paths`）。CodeRabbit のレビューを読み、手でマージする直前に今の head へのレビューを読み直す。CodeRabbit は PR に `@coderabbitai review` とコメントして頼む。

## 共通のきまり

- PR の作り方・マージの仕方は satou-aaaaa/shared-workflows の `routines/pr.md`、全体のきまりは `routines/common.md` に従う。
- CI の部品は shared-workflows の共通部品を呼ぶ。リポジトリ側の .github/workflows には呼び出しと設定値だけを書く。
- Claude 以外の AI・人が作業するときは、shared-workflows の `routines/tool-map.md` と `routines/without-claude.md` を読む。
