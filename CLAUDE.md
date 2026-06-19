# CLAUDE.md — s-kenken.com

写真家・澤井健太（けんけん / @s_ken.ken）のポートフォリオサイト。
このファイルはプロジェクトの前提・ルールをまとめたもの。毎回説明しなくていいように、ここを読んで作業して。

## 基本

- 日本語でやり取りする。カジュアルでOK。
- 公開URL: https://sawakenken.github.io （GitHub Pages）
- リポジトリ: github.com/sawakenken/s-kenken.com（`main` ブランチ）
- 本体は **`index.html` 1枚**（自己完結型。`<style>` と `<script>` を内包、約2000行）
- 画像の命名ルール表: `画像ファイル名_仕様書.md`
- スタイルは基本 index.html 内のインライン。`style.css` もあるが、編集は基本 index.html 側で完結させる。

## 作業の進め方

- ファイルは直接編集して、commit できる状態に仕上げる。「ここを手で直して」みたいな指示出しではなく、こちらで書き換える。
- 編集後は必ず括弧の対応をチェックする（壊れてないか）:
  `python3 -c "s=open('index.html').read();print(s.count('{')-s.count('}'),s.count('(')-s.count(')'))"` が `0 0` であること。
- 大きめの構造変更（データ追加・差し替え）は、対象を一意に特定してから差し替える。同じ文言が複数あることが多いので、キー（例 `"w43":`）で位置特定する。
- 修正は「どういう条件で起きるバグか」を確認してから直す。再現条件の取り違えに注意。
- 変更したら commit & push まで頼まれたらやる（GitHub Pages なので push で公開反映）。

## サイト構造（index.html 内の主要データ）

- **WORKS オブジェクト**: キー `"w1"`〜。`w1`〜`w6` がトップの Selected Works（`highlight-1〜6.jpg`）。`w7`以降が実績ページ一覧（`#wpGrid`）。
  - エントリ書式: `{photo, cat, catFull, client, title, img, desc, instagram, images?, projects?}`
  - `cat` は `Ambassador | Tourism | Commercial | Official | Hotel | Exhibition` 等。フィルターの `data-cat` は小文字（`ambassador` 等）。
  - `desc` の改行はリテラルの `\n\n`（`white-space:pre-line` で表示）。
  - `img` はグラデのプレースホルダー class（`ph-city / ph-sakura / ph-bamboo / ph-okinawa / ph-portrait`）。画像が来るまでこれが表示される。
- **★ 1クライアント=1カード + 案件タイムライン（重要・新設計）**: 複数案件を持つクライアントは **1エントリに集約**し、`projects:[]` を持たせる。`populateWork` が `projects` 有りのとき詳細を**アコーディオン式タイムライン**で描画（ヒーロー画像＋概要＋案件一覧）。
  - `projects[]` の各要素: `{title, titleEn, date, cat, photo, img, desc, descEn, posts:[]}`
  - `posts:[]` の各要素: `{date, link}`（複数インスタ投稿。`link` 空なら ig プロフィールへ）。
  - 描画は `.wd-timeline / .wd-tl-item / .wd-tl-row(button) / .wd-tl-panel`。行タップで `.wd-tl-item.open` をトグル（document 委譲クリックで処理）。`pc` map が cat→pill クラス（`.pill-official` 等）を引く。
  - **実例: NAKED, Inc.（w30）が集約済み**（平安神宮/伏見稲荷/八坂/二条城/宇治/ガウディ展）。八坂は複数投稿のデモ3件入り。`projects` 無しのエントリ（w44 DJI 等）は従来の `images[]` ギャラリー描画。
- **POSTS オブジェクト**: キー `"p1"`〜（News）。`populatePost` が描画。
- **★ News 単一ソース（重要・新設計）**: News ページ `#npGrid`（年グループ `.np-year-group` ＋ `.news li[data-cat][data-slug]`）が**正本**。TOPの `#homeNews` は空で、起動時に npGrid の**新着6件をクローン**して表示（編集は News 側のみで TOP に自動反映）。`NEW` バッジは最新（2026）だけに付ける運用。
- **ルーター**: `#work-<slug>` / `#post-<slug>` のハッシュで詳細表示。`populateWork(slug)` / `populatePost(slug)` が描画。クリックは `document` 委譲（`.work[data-slug]` / `li[data-slug]` / `.wd-tl-row` 等を判定）。

## インスタ埋め込みのルール（重要）

- `renderIG(permalink)` が詳細ページ下部（`#wdIg`）を描画する。
- WORKS の `instagram` に**投稿URLがある作品 → その投稿を埋め込む**。
- `instagram` が**空の作品 → インスタTOP（@s_ken.ken）に飛ぶCTAボタン**「この作品をInstagramで見る →」を出す（`.ig-cta`）。
- 埋め込み用 `embed.js` は読み込み済み。

## 画像ファイル

- 作品画像は `images/works/<名前>.jpg`。一覧の番号付き（`01.jpg`〜）か、名前付き（例 `dji-osmo-pocket4pro.jpg`）。
- クライアントロゴは `images/clients/<slug>.png`（透過PNG）。フォルダに入れれば自動表示、無ければブランド名テキストでフォールバック。
- 画像を増やしたら `画像ファイル名_仕様書.md` も更新する（件数の数字も）。

## ロゴウォール（PARTNERS / Clients）の決まり

- iOS対策で **JS transform マーキー**（CSSキーフレーム % や `width:max-content` は使わない。iOSで崩れる）。
- ロゴセルのテキストは**デフォルト表示**、画像が `onload` したら隠す（最初のチラ見え防止）。
- 行1と行3は**箱数を必ず揃える**（グリッド整列のため）。同様に行2と行4も揃える。
- 全行のアニメ時間は **100秒** で統一。

## 実績記事をインスタ投稿から作る手順（よくやる作業）

1. けんけんがインスタ投稿のスクショ（キャプション）＋クライアント名を渡す。
2. **既存クライアントなら** そのカードの `projects:[]` に案件を追加（投稿は `posts:[]`）。**新規クライアントなら** WORKS に新 `wN` エントリ＋`#wpGrid` カードを追加。
3. News にも載せるべき内容（展示/受賞/メディア/告知性）なら `POSTS`＋`POSTS_EN`＋`#npGrid` カード（年グループ）にも追加。Works と News は両立OK（実績の記録 vs お知らせ）。
4. 必要なら画像ファイル名を決めて仕様書に追記。
5. デフォルトはフル一覧に追加（トップ Selected Works には入れない。頼まれたら入れる）。
6. **どの投稿を取り込んだかは `インスタ取り込みログ.md`（git管理・非公開メモ）に必ず追記**。インスタURL/画像の未対応・バックログもここで管理。
- ⚠ **インスタURL問題**: スクショに投稿URLが無いため、現状リンクは**全てプロフィール飛び（CTA）**。URLが揃ったら一括差し替え予定（取り込みログ参照）。
- ⚠ **Galaxy/Team Galaxy 系は投稿が大量**。単独Worksにせず、Samsung を1クライアント集約する時に `projects` へ畳む方針。

## フォント / 表示の方針メモ

- 日本語は Zen Kaku Gothic New 主体。JS注入テキストは中華フォント化しやすいので、`document.fonts.load('1rem "Zen Kaku Gothic New"', テキスト)` で字形を先読みさせる。
- 作品タイトルの折り返しは `word-break:keep-all; word-break:auto-phrase`（`overflow-wrap:break-word` は単語途中で割れるので使わない）。**ただし News 見出し（`.news .nt`）は長文なので `word-break:normal`。`auto-phrase` は iOS Safari 非対応で `keep-all` だけ効いて折り返さずはみ出すので注意。**
- 変更後はブラウザのハードリロード（キャッシュ）を案内すること。**iOS はキャッシュが特に強い。`?v=N` 付きURL かプライベートタブで確認してもらう。**

---

# 🟢 引き継ぎ / 現在の状況（最終更新: 2026-06-19）

新しいセッション（アプリ版含む）はここを読めば続きから作業できます。

## デプロイ / 確認URL
- **作業＆確認用（本番の実体）**: `https://sawakenken.github.io/s-kenken.com/`
- 独自ドメイン `s-kenken.com` は**まだ旧サイト**（Google系）に向いたまま。**画像を入れてから移転**予定（DNS/Custom domain設定は README 参照）。急がない。
- 公開フロー: `git push origin main` で GitHub Pages に反映（数十秒〜数分）。コミット/プッシュは頼まれたら実施。
- git push 認証はオーナーの Mac のキーチェーンに保存済み（このサンドボックスからは push 可。token を URL に焼くフォールバックは使わない）。

## 実装済みの大きな機能：JP/EN 言語切替（i18n）
1ファイル内で完結。`<script>` 冒頭に `I18N` / `t(ja,en)` / `applyLang()` がある。
- 静的テキスト: `data-en="..."` 属性（JPがHTML本文、ENが属性）。`applyLang` が `innerHTML` を入替。フォームは `data-en-ph`。
- 動的データ: `WORKS_EN`（作品の `t`=英題, `c`=英クライアント。説明文は `workDescEn()` がカテゴリ別テンプレで生成）, `POSTS_EN`（ニュース英題）+ `POST_BODY_EN`（pill別の英文本文）。`populateWork/populatePost` が `t()` で出し分け。
- グリッドの作品カードは `rerenderForLang()` が言語切替時に `.wt`/`.wclient` を更新。
- トグルUI: デスクトップ=ナビ内 `.lang-toggle`、モバイル=`.nav-actions` 内（ハンバーガーの外）。`localStorage 'lang'` 記憶、`<html lang>` 同期。
- 値は curly quote “”/単一引用符を使い、`data-en` 属性内に生の `"` を入れない（属性が壊れる）。
- SEO用の `<title>`/meta は意図的に日本語のまま（必要なら言語連動可）。

## このセッション（2026-06-19）で入った大きな変更
- **Works を「1クライアント=1カード→案件→投稿」の3階層に再設計**（上の★参照）。NAKED で試作済み、残りクライアントは横展開待ち。
- **News を単一ソース化**（TOPは新着自動クローン）。重複キー掃除済み。
- インスタスクショから取り込み: w45 そうだ京都 / w46 INNOCN / w47 森の京都 / NAKEDにガウディ展案件 / News p36-38（2026グループ）。
- **クロスデバイス対応**: sticky hover を `@media(hover:hover)` でガード（フィルタ/メニュー/about/SNS等）、`html,body` に `overflow-x:hidden`、`backdrop-filter` に `-webkit-` 併記、スクロール復元の堅牢化、ヒーロー戻り時のズーム再生抑止。
- メニューのチェブロンを挙動別に: 展開グループ=˅（開くと˄）/ 直リンク=›。
- **権限**: `.claude/settings.local.json`（gitignore済）で git/sips/python3 等を許可＋`acceptEdits`。`.claude/` は gitignore 済み。
- **進捗メモ: `インスタ取り込みログ.md`**（取り込み済み/未対応URL・画像/バックログ）。

## 残タスク
1. **画像投入（最大の宿題）**: `_intake/`（gitignore済）に元画像 → `sips` で変換 → `images/` へ仕様書名で配置。
   - **済み**: gallery/japan（01-10、横は16:10）＋ TOPギャラリー（images/gallery/01-10、japanのコピー・仮）。
   - **未**: gallery 残り21カテゴリ / works 各画像（souda-kyoto, naked-gaudi, innocn-40u1c, morinokyoto-phototour 等）/ hero / about / クライアントロゴ `images/clients/*.png`。
2. **インスタURL一括差し替え**（現状プロフィール飛び。対策は要検討）。
3. **クライアント集約の横展開**: Samsung（Galaxy大量）/サウジ/Adobe/Turkish/Trip.com を NAKED と同じ `projects` 形式へ。
4. 画像が揃ったら**独自ドメイン移転**（GitHub Pages Custom domain + DNS）。
5. 仮データ確認: NAKED各案件の日付・八坂のデモ投稿、INNOCN投稿日。

## 画像最適化の指針（軽量化優先）
- ギャラリー縦=1200×1500 / 横=1600×1000、works 長辺1600、hero スマホ1080×1920・PC2400×1350、OGP 1200×630。各 0.3〜0.5MB 以内、JPEG q80（WebP化も可）。
- ギャラリーは非表示カテゴリの背景画像はDLされない作りなので、圧縮さえすれば十分軽い。
