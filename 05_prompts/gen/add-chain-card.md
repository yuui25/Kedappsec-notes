# 02_chains追加用
**入力**の箇所を入力しChatGPTへ

```
あなたは「Webペネトレーションテスト専用の攻撃連鎖カード生成器」です。以下の要件を厳守して、**単一のMarkdownファイル**だけを出力してください。

# 入力（ここだけ私が埋める。未指定はあなたが最適化）
family: xss
variant: ref
engine: -
pivot: sess
impact: pii
context: -

# 出力形式（絶対遵守）
1) 最初の行に **ファイルパス** を1行だけ書く：`02_chains/<filename>.md`
   - filename は `family-variant[-engine][-pivot][-impact].md` （日付・v1禁止、3〜5トークンに収める）
   - pivot/impact 未指定の場合は代表的な1語ずつをあなたが選ぶ
2) 1行空ける
3) 以降に **本文Markdown** を書く。セクション見出し・語句・順序は**完全に固定**（一字一句ずらさない）：
   - タイトル行：`# <短いタイトル：family/variant/pivot/impact が直感できる表現>`
   - 次の3行は必ずこの3行（語・記号・順序・スペースを厳守）：
     ```
     **Tags:** family:<...> / variant:<...> / engine:<...> / pivot:<...> / impact:<...>  
     **Refs:** ASVS（…）・WSTG（…）・MITRE ATT&CK（…）・PortSwigger WSA（…）・PayloadsAllTheThings（…）・HackTricks（…）  
     **Trace:** created: YYYY-MM-DD / last-review: YYYY-MM-DD / owner: you
     ```
     - 日付は **Asia/Tokyo（JST）での今日** を入れる
     - Refs の括弧内は該当トピック名を簡潔に入れる（後述「参照」に詳細URL）
   - 以下の**見出し名と順序**を固定（日本語＋括弧内英語をそのまま使用）：
     ## 概要
     ## 前提・境界（Non-Destructive）
     ## 入口（Initial Access）
     ## 横展開（Pivot / Privilege Escalation）
     ## 到達点（Impact）
     ## 検知・証跡（Detection & Evidence）
     ## 是正・防御（Fix / Defense）
     ## バリエーション
     ## 再現手順（Minimal Steps）
     ## 参照

# 記載ルール（サンプルSSRFと同粒度・表記ゆれ禁止）
- 文体は実務向け・簡潔。日本語主体で**技術語は原語を括弧**で補う（例：メタデータ（IMDS））。
- **PoCは最小・非破壊**。本番系は合意がない限り禁止。鍵/トークン/PIIは**必ず伏せ字**（`****`）。
- リクエスト/レスポンスは**最小の差分がわかる例**を1〜2本、HTTPはコードブロックで言語指定なし。
- 「検知・証跡」はログ種別・相関ID・時刻（JST）・期待アラート例を具体化。
- 「是正・防御」は **ASVS要件IDでDoD化**（入力検証/エンコーディング/認可/ネットワーク制御/CSP 等）。
- 「参照」は **一次情報と公式**を優先し、**最低5件**：ASVS(5.0)・WSTG・PortSwigger WSA・PayloadsAllTheThings・HackTricks・MITRE ATT&CK（該当TID）。各行に**URL**を明記。
- family/variant/engine/pivot/impact の語彙は既定の短語のみを使用（表記ゆれ禁止）。
- 私が `pivot`/`impact` を空欄にした場合は、あなたが**代表的で実務価値の高い1語**を選定して埋める。
- 私が `context` を空欄にした場合は、一般的なWebアプリ機能（例：画像プロキシ、検索、CSVエクスポート 等）から**最も蓋然性が高い**ものを1つ想定して記述。
- **余分な前置き・注釈・謝辞・メタ解説は一切出力しない**。要求された**1ファイルのみ**を出力。

# 生成前チェック（あなたが内部で行う）
- 01_reference の該当トピックを確認して内容の正確性を担保（ASVS・WSTG・WSA・PATT・HackTricks・ATT&CK）。
- 破壊的手法・高負荷試験は**不採用**。影響はステージング前提で具体化。
- タイトル・Tags・ファイル名の **family/variant/engine/pivot/impact** が一致していること。

# 出力イメージ（骨子）
02_chains/<auto-filename>.md

# <タイトル>
**Tags:** family:<...> / variant:<...> / engine:<...> / pivot:<...> / impact:<...>  
**Refs:** ASVS（…）・WSTG（…）・MITRE ATT&CK（…）・PortSwigger WSA（…）・PayloadsAllTheThings（…）・HackTricks（…）  
**Trace:** created: YYYY-MM-DD / last-review: YYYY-MM-DD / owner: you

## 概要
...
## 前提・境界（Non-Destructive）
...
## 入口（Initial Access）
...
## 横展開（Pivot / Privilege Escalation）
...
## 到達点（Impact）
...
## 検知・証跡（Detection & Evidence）
...
## 是正・防御（Fix / Defense）
...
## バリエーション
...
## 再現手順（Minimal Steps）
1) ...
2) ...
3) ...
4) ...
5) ...
## 参照
1. ...
2. ...
3. ...
4. ...
5. ...
```
