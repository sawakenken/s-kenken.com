# Sawai Kenta — Photographer Site

澤井健太（けんけん）ポートフォリオサイト。GitHub Pages でホスティング、独自ドメイン `s-kenken.com` で公開する構成です。

## ファイル構成

```
/
├── index.html          ... サイト本体（1ファイル完結。CSS/JSもこの中）
├── 404.html            ... 存在しないURL用のエラーページ
├── CNAME               ... 独自ドメイン設定（中身: s-kenken.com）
├── .nojekyll           ... GitHub PagesのJekyll処理を無効化（空ファイル）
├── robots.txt          ... 検索エンジン向け
├── sitemap.xml         ... サイトマップ
├── site.webmanifest    ... PWA/ホーム画面追加用
├── favicon.svg         ... ブラウザタブのアイコン
├── apple-touch-icon.png... iOSホーム画面アイコン (180px)
├── icon-192.png        ... manifest用 (192px)
├── icon-512.png        ... manifest用 (512px)
└── ogp.jpg             ... SNSシェア時のカード画像 (1200×630)【※後で実写真に差し替え推奨】
```

---

## 1. GitHub Pages で公開する

1. GitHub で新しいリポジトリを作成（例: `s-kenken-site`、Public）
2. この一式のファイルを **すべて** リポジトリ直下にアップロード（`.nojekyll` や `CNAME` も忘れずに）
3. リポジトリの **Settings → Pages** を開く
4. **Source** を「Deploy from a branch」、Branch を `main` / フォルダ `/ (root)` に設定して Save
5. 数十秒〜数分でビルドされ、`https://<ユーザー名>.github.io/<リポジトリ名>/` で確認できます

---

## 2. 独自ドメイン `s-kenken.com` を Studio から移管する

ドメイン自体（レジストラ）はそのまま使い、**向き先（DNS）を Studio → GitHub Pages に変更** します。
※ レジストラ（お名前.com / Google Domains / Cloudflare 等）の DNS 設定画面で行います。

### ① GitHub 側でドメインを登録
- Settings → Pages → **Custom domain** に `s-kenken.com` と入力して Save
  （リポジトリに含めた `CNAME` ファイルと同じ内容です）

### ② レジストラの DNS レコードを変更

**apex（s-kenken.com）— A レコードを4つ:**
```
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```

**IPv6対応する場合 — AAAA レコードを4つ（任意・推奨）:**
```
2606:50c0:8000::153
2606:50c0:8001::153
2606:50c0:8002::153
2606:50c0:8003::153
```

**www もカバーする場合 — CNAME レコード（任意・推奨）:**
```
www  →  <ユーザー名>.github.io
```

> ⚠️ Studio が設定していた既存の A / CNAME レコードは **削除** してから上記を入れてください。
> ⚠️ 反映には最大24時間（通常は数分〜1時間）かかります。切替前日にDNSのTTLを短く（300秒など）しておくとスムーズです。

### ③ HTTPS を有効化
- DNSが反映されたら Settings → Pages の **「Enforce HTTPS」** にチェック
  （証明書の自動発行に少し時間がかかる場合があります）

---

## 3. お問い合わせフォームを有効化する（FormSubmit）

フォームは [FormSubmit](https://formsubmit.co)（無料・登録不要）で `s.kenkenn@gmail.com` に届く設定です。
**初回だけ有効化が必要** です。

1. 公開後、サイトのお問い合わせフォームから **テスト送信を1回** 行う
2. `s.kenkenn@gmail.com` に FormSubmit から確認メールが届く
3. メール内の **「Activate Form」リンクをクリック** → 以降の送信がすべて受信されるようになります

### 補足
- 迷惑メール対策に honeypot（ボット用の隠しフィールド）を仕込み済みです。
- メールアドレスをページのソースに出したくない場合は、FormSubmit の「ランダムエイリアス」に切り替え可能です。`index.html` 内の
  `https://formsubmit.co/ajax/s.kenkenn@gmail.com`
  を、発行されるエイリアス（例: `https://formsubmit.co/ajax/abcd1234...`）に置き換えてください。

---

## 4. これからやること（次のステップ）

- [ ] **写真の差し替え** — 現在はグラデーションのプレースホルダー。`images/` に実写真を入れて差し替え
- [ ] **OGP画像 `ogp.jpg` を実写真に** — 今は仮のテキスト画像。ベスト1枚（1200×630）に置き換え推奨
- [ ] **About のプロフィール文・数字** の最終確認
- [ ] **News の内容** を最新に更新

---

## メモ
- サイトは1ページ内で表示を切り替える方式（`#works-page` などのハッシュ）です。検索エンジンには `s-kenken.com/` 1ページとして認識されます。
- 編集は `index.html` 1ファイルを直接いじればOK。CSSは `<style>`、JSは下部の `<script>` 内にあります。
