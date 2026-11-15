# liffformapp

ふたり（佳祐・優衣）の家計簿「**kakeibo-together**」の入力フォーム（LINE の LIFF アプリ）です。
LINE のトークで開き、支出を入力して送信すると、Bot にメッセージが送られ、スプレッドシート「家計簿」に記録されます。

- 公開 URL（GitHub Pages）: https://keisuke-fbr.github.io/liffformapp/
- **main に push すると、GitHub Pages に反映されます。**（数分かかることがあります）

## 全体像

```
このフォーム（LIFF）──liff.sendMessages──▶ LINE Bot ──Webhook──▶ GAS ──▶ スプレッドシート「家計簿」
```

| 役割 | リポジトリ |
|---|---|
| 入力フォーム（このリポジトリ） | `liffformapp` |
| GAS（記録と返信） | `kakeibo-together-gas`（private） |

フォームは、GAS を直接呼びません。LINE のメッセージとして送り、Bot が受け取って GAS に渡す仕組みです。

## 入力項目

| 項目 | 形式 | 必須 |
|---|---|---|
| 日付 | 日付 | はい |
| 内容 | テキスト | はい |
| 金額 | 数値 | はい |
| 支払方法 | プルダウン（楽天Pay / 楽天カード / 三井住友カード / JALカード / 交通系 / 現金 / その他）。初期値は「指定なし」 | いいえ |
| 支払主 | ラジオボタン（佳祐 / 優衣） | はい |

## 送信されるメッセージ

次の5行のテキストを、LINE のトークに送ります。支払方法が「指定なし」のときは、4行目が空行になります。

```
日付
内容
金額
支払方法
支払主
```

GAS は、4行（支払主が4行目）の旧形式も受け付けます。

## ファイル

| パス | 内容 |
|---|---|
| `index.html` | フォーム本体。Bootstrap と LIFF SDK を CDN から読み込む、1ファイル構成 |

LIFF ID は `index.html` の `liffId` に書かれています（LINE Developers コンソールで発行したものです）。

## 支払方法の候補を変えるとき

次の2か所を、同じ内容にそろえます。

1. このリポジトリの `index.html` のプルダウン（`<select name="paymentMethod">`）
2. GAS（`kakeibo-together-gas`）の `src/コード.js` の `PAYMENT_METHODS`

GAS 側の候補にない値を送ると、記録されず、エラーが返信されます。

## 動作確認

LIFF は LINE アプリ内で動きます。ブラウザ単体では送信できません（`liff.sendMessages` が使えないため）。
LINE の LIFF URL（LINE Developers コンソールの LIFF タブで確認できます）から開いて確認してください。
