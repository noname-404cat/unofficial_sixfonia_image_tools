# unofficial_sixfonia_image_tools

シクフォニ（非公式）の推し活画像を作るツール。Google Takeout に入っている
自分のデータから、SNSに投稿できる画像を生成する。

| タブ | 作るもの | 入力 |
|---|---|---|
| 視聴TOP9タイル画像 | よく見た動画9本の 3×3 画像 | `watch-history.json` |
| コメントのワードクラウド | 自分のコメントから作る桜型の画像 | `comments*.csv` |

入力は Google Takeout の「YouTube と YouTube Music」から書き出す。

```
history/watch-history.json    視聴履歴
comments/comments*.csv        自分のコメント
live chats/live chats*.csv    ライブチャット（任意）
```

## 動かす

```bash
pip install -r requirements.txt
streamlit run apps/streamlit/app.py
```

画像に日本語を描くのでフォントが要る。Linux では `packages.txt` の
`fonts-ipafont-gothic` を入れる。手元の環境では OS 標準の日本語フォントを
自動で探す（`apps/streamlit/tiles.py` の `FONT_CANDIDATES`）。

## デプロイ（Streamlit Community Cloud）

| 設定 | 値 |
|---|---|
| Branch | `main` |
| Main file path | `apps/streamlit/app.py` |

依存は直下の `requirements.txt`、日本語フォントは直下の `packages.txt` から入る。
**どちらもリポジトリ直下に無いと読まれない。**

> `packages.txt` にコメントを書いてはいけない。中身がそのまま `apt-get` に渡されるため、
> `#` で始まる行もパッケージ名として扱われ `E: Unable to locate package #` で失敗する。
> パッケージ名だけを1行ずつ書く。`requirements.txt` は pip が読むのでコメント可。

### 任意の設定

環境変数 `SNAPSHOT_URL` に動画マスタ（7チャンネルの動画一覧JSON）のURLを渡すと、
コメントのチャンネル判定がその一覧との突合になる。未設定でも動くが、その場合は
視聴履歴からの推定になり、削除・非公開になった動画を取りこぼす。

## 知っておいてほしいこと

- **視聴履歴には視聴時間が入っていない。** 記録されているのは再生を始めたイベントだけなので、
  ここでいう「よく見た」は履歴に出てきた回数のこと
- **視聴履歴には保存期間がある。** Google 側の自動削除設定により古い分から消えるため、
  書き出しに含まれるのは直近の一定期間だけのことがある
- **コメントは全期間残る。** 視聴履歴と違って古いものも書き出される
- **Takeout のロケールは環境で変わる。** 英語（`Watched …`）と日本語（`… を視聴しました`）の
  両方を受け付ける
- **コメントCSVの `Channel ID` 列は自分のチャンネルID** であって、コメント先の動画のものではない。
  チャンネルの判定は `Video ID` 経由で行っている

## ファイルの扱い

アップロードされたファイルは、このアプリが動いているサーバーのメモリ上で処理する。
ディスクへの保存も外部への送信もしないが、**ブラウザ内だけで完結する処理ではない**。

## テスト

```bash
pip install -r requirements.txt pytest
pytest tests -q
```

個人の視聴履歴はコミットできないため、実データで確認した挙動を
`tests/fixtures/` の匿名フィクスチャで固定している。

## 非公式

ファンが個人で作った非公式ツールです。シクフォニおよび所属各位とは関係ありません。
