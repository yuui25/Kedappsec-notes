# 08_Inference_Playbook (If 観測 → Then 推論)

01〜07 で集めた情報をもとに、  
**「〇〇があるということは、〇〇できるのではないか？」**  
というロジックで BAC / Logic Flaw の仮説を機械的に出すためのプレイブック。

形式は以下で統一する：

- Observation（観測）  
- Reasoning（なぜ怪しいか）  
- Questions（自分に投げる問い）  
- Next Action（具体的にやること）

---

## 1. ID / Resource 関連の推論

### 1-1. list API に他ユーザの ID が混ざる

- **Observation**
  - /list や /search のレスポンスに、自分以外と思われる userId / accountId / itemId が含まれる

- **Reasoning**
  - 「一覧」レベルで他人のIDが見えるということは、
    「詳細閲覧」や「更新」も他人分で通る可能性が高い

- **Questions**
  - この ID を /detail や /update にそのまま使ったらどうなるか？
  - 自分の ID と他人の ID の区別をサーバは本当にやっているか？

- **Next Action**
  - GET /resource/{id} の {id} を一覧で拾った他人IDに差し替え
  - update/delete API の payload.id も同様に差し替え


### 1-2. detail API が URL パラメータの ID をそのまま使っている

- **Observation**
  - /item/{id} のようなパスで、JS 内で {id} を組み立てているだけ

- **Reasoning**
  - サーバサイドで「所有者チェック」をしていない場合、
    単純な IDOR になる典型パターン

- **Questions**
  - URL の {id} を他人IDにしたとき、ステータス・レスポンスはどう変わるか？

- **Next Action**
  - 自分が持っていない ID を使って /item/{id} にアクセス
  - 200 / 403 / 404 の挙動を確認


### 1-3. update API の payload に id フィールドが含まれている

- **Observation**
  - PUT/POST /item/update の body に itemId が入っている

- **Reasoning**
  - 「どのレコードを更新するか」をクライアントが指定しているということは、
    クライアント側の ID を信じている可能性がある

- **Questions**
  - itemId を他の ID に差し替えたら、そのレコードも更新できてしまうか？

- **Next Action**
  - 自分の itemId → 他人の itemId に変えて update 実行
  - 成功すれば IDOR-Write / 不適切な更新権限


---

## 2. tenant / org（組織境界）の推論

### 2-1. API に tenantId / orgId が露出している

- **Observation**
  - レスポンスや request body に tenantId / orgId が含まれる

- **Reasoning**
  - 組織境界の ID がクライアントに丸投げされているということは、
    「その値を変えることで他組織データに触れる」可能性がある

- **Questions**
  - tenantId を変えたらレスポンスの中身は変わるか？
  - そもそも tenantId を送らなくても動くか？

- **Next Action**
  - tenantId を削除 / null / 他の tenantId に差し替えて API を叩く
  - レスポンスの件数や内容に変化がないか確認


### 2-2. list API に tenantId が無いのに multi-tenant らしい

- **Observation**
  - 画面や仕様上 multi-tenant なのに、list API のパラメータに tenantId が見当たらない

- **Reasoning**
  - サーバ側がログインユーザから tenant を推定しているはずだが、
    その実装が甘いと cross-tenant exposure になりやすい

- **Questions**
  - 他テナントのユーザとしてログインした場合に、list の中身がどこまで変わるか？

- **Next Action**
  - 可能なら複数アカウントで同じ list API を比較
  - tenant を跨いだデータ混在がないか確認


---

## 3. Role / Permission 関連の推論

### 3-1. API に role / permission / isAdmin フィールドが存在

- **Observation**
  - JSON body や response に role, scope, permission, isAdmin などが含まれる

- **Reasoning**
  - 「権限ロジックの一部」をクライアント側でも扱っているということは、
    その値を書き換えることでロールを偽装できる可能性がある

- **Questions**
  - role を admin 相当の値に変えたら、何か変化するか？
  - 権限が必要な API に、この role を付けて叩くことはできないか？

- **Next Action**
  - update/create API の payload に role を追加/変更して送信
  - isAdmin=true にして挙動を確認


### 3-2. UI では role 変更不可だが API には role の更新エンドポイントあり

- **Observation**
  - /user/updateRole のような API があるが、画面側に相当する UI が見当たらない

- **Reasoning**
  - 管理用UIだけが別系統、または “消し忘れAPI” の可能性

- **Questions**
  - 一般ユーザ権限でその API を叩くとどうなるか？

- **Next Action**
  - 自分のセッションで /user/updateRole に対して一般→admin 変更を試行
  - 成功すれば role escalation


---

## 4. Ownership（ownerId / createdBy）の推論

### 4-1. item の response に ownerId / createdBy が含まれる

- **Observation**
  - /item/{id} の response 内に ownerId が明示されている

- **Reasoning**
  - サーバ側で所有権チェックをしていない場合、
    ownerId を書き換えることで「所有者になりすまし」が可能

- **Questions**
  - update API に ownerId を含めて送ったら変更できてしまうか？

- **Next Action**
  - update/create の payload に ownerId を追加/変更して送信
  - 別ユーザの ownerId を指定して挙動を見る


---

## 5. Flow / Wizard / Step の推論

### 5-1. /step-1 → /step-2 → /confirm → /finish の URL パターンがある

- **Observation**
  - URL や JS ロジックに明確なステップフローが見える

- **Reasoning**
  - 「中間ステップのサーバチェックが甘い」ことが多く、
    confirm や finish に直アクセスするとフローを飛ばせる

- **Questions**
  - /confirm や /finish を単独で叩くと何が起こるか？
  - hidden フィールドを弄って confirm を叩いたらどうなるか？

- **Next Action**
  - セッション有効 / 破壊済みの両状態で /confirm /finish を直打ち
  - hidden パラメータを改変 → confirm 送信


---

## 6. Hidden / Admin API の推論

### 6-1. JS 内に /admin/ や /internal/ の API があるが UI には出てこない

- **Observation**
  - JS ソースに admin / internal / debug といったパスが埋まっている

- **Reasoning**
  - 管理画面残骸 / 内部ツール用 API がそのまま公開されている可能性

- **Questions**
  - 一般ユーザ権限のクッキーでその API を叩くとどこまで見えるか？

- **Next Action**
  - 必要な header/cookie を付けた上で /admin/** /internal/** を direct-call
  - list / export / delete 系が通らないか確認


---

## 7. Cloud / SaaS Misconfig の推論

### 7-1. Subdomain が SaaS（Vercel/Netlify/Heroku 等）を指している

- **Observation**
  - CNAME が *.vercel.app / *.netlify.app / *.herokuapp.com 等に向いている

- **Reasoning**
  - SaaS 側の設定が消えている or 空の場合、サブドメインテイクオーバーの可能性

- **Questions**
  - その SaaS に同じホスト名でアプリを新規作成できる余地はないか？（※バグバウンティ前提）

- **Next Action**
  - ベンダの許容範囲内で takeover の可否を確認
  - 許される範囲を超えないように PoC を設計（プログラムに従う）


---

## 8. State Machine（status / workflow）の推論

### 8-1. status フィールドが存在し、UI では一部操作が制限されている

- **Observation**
  - status: "draft" / "published" / "archived" などが response にある
  - UIでは「削除済み」「アーカイブ済み」などは編集不可になっている

- **Reasoning**
  - API側の status チェックが甘いと、UIが禁止している遷移も通る

- **Questions**
  - APIで status を直接変更した場合、UI制限をバイパスできないか？

- **Next Action**
  - update API に status フィールドを追加/変更して送信
  - forbidden な遷移（archived→draft 等）を試す


---

## 9. UI vs API ギャップの総合推論

### 9-1. UI にない操作が API には存在している

- **Observation**
  - delete / bulk-update / export など、UI には見えない機能が API にある

- **Reasoning**
  - 「UIで隠したから安全」という勘違いが入りやすい部分

- **Questions**
  - 一般ユーザ権限でその API を叩いたとき、本当に拒否されるか？

- **Next Action**
  - UIに該当機能がないAPIを、今のセッションで direct-call
  - 200 が返れば高確度な BAC

---

## 10. 使い方のガイド

1. 01〜04 の作業で、**Observations** をこのファイルの各項目にメモする  
2. 観測内容に該当する **Inference Rule（推論パターン）** を拾う  
3. Rule に書いてある **Questions → Next Action** をそのまま 05〜07 で実行する  
4. 当たったものは Findings/Hypothesis に格上げする

こうすることで、  
「なんとなく怪しい」ではなく、  
**“観測 → 推論ルール → 仮説 → テスト” の流れを毎回再現** できる。