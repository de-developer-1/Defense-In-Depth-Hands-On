# Defense in Depth Hands-On Repository（多層防御ハンズオンリポジトリ）

メールセキュリティ機能をローカル環境でハンズオン形式で学習・検証

- SMTP 認証（SASL）
- POP-before-SMTP
- SPF（Sender Policy Framework）
- DKIM（DomainKeys Identified Mail）

Docker Compose を用いて複数のメール関連コンテナを一括で起動・検証可能な構成です。

---

## 検証概要

本ハンズオンでは、以下のような構成で「多層防御メール環境」を構築します。

- Postfix によるメール送信（SMTP）
- Dovecot によるメール受信（IMAP/POP3）と SASL 認証連携
- POP-before-SMTP による送信許可（簡易実装）
- OpenDKIM による DKIM 署名処理
- BIND ゾーンファイル形式による SPF・DKIM DNS レコードの設定


---

## リポジトリ構成
```
defense-in-depth-hands-on/
├── README.md             # 日本語の手順・解説
├── docker-compose.yml    # サービス定義
├── postfix/              # Postfix 設定（SMTP）
│   └── main.cf
├── dovecot/              # Dovecot 設定（IMAP/POP3）
│   └── dovecot.conf
├── opendkim/             # DKIM 署名設定
│   ├── opendkim.conf
│   ├── KeyTable
│   └── SigningTable
└── dns/                  # SPF/DKIM 用 DNS ゾーンファイル
    └── example.zone
```

---

## 実行手順（クイックスタート）

### 1. リポジトリをクローン
```bash
git clone git@github.com:de-developer-1/vm-vs-container-hands-on.git
cd defense-in-depth-hands-on
```

### 2. Docker Compose による起動
```bash
docker-compose up -d
```

### 3. ユーザー作成とパスワード設定（Dovecot）
```bash
# Dovecot コンテナ内で実行
useradd -m -s /sbin/nologin user1
# パスワード生成して /etc/dovecot/passwd に追記
```

### 4. メールクライアント設定（Thunderbird 推奨）
- SMTP: `localhost:25`
- POP3: `localhost:110` または IMAP: `localhost:143`
- 認証方式: PLAIN / LOGIN
- ユーザー名: `user1`
- パスワード: 上記で設定した値

### 5. SPF・DKIM の確認
- DNS レコード（`dns/example.zone`）をローカル BIND に適用
- 受信サーバ（Gmail など）で受信ヘッダーに SPF/DKIM の `pass` を確認

---

## 補足
- POP-before-SMTP 機能は Dovecot ログ監視や postfwd などのツールで簡易実装可能です。
- DKIM 鍵は OpenDKIM キーツールで生成して `KeyTable` および `SigningTable` を更新してください。
- DNS ゾーンファイルは `example.local` ドメインに合わせて編集してください。

---

本リポジトリは、セキュリティやメール配送の基礎をローカルで理解・検証するための簡易ハンズオンです！
実運用用途では、より厳密なセキュリティ設計・鍵管理が必要なため興味があれば継続して学習してみてください！
