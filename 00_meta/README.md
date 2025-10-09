# 00_meta 運用ガイド

このフォルダは **Kedappsec-notes の運用方針・命名規約・安全上の注意**を一元管理するためのメタドキュメント置き場です。(いつか更新)

|No|asvs|level|wstg|payloadsallthethings|mitre|scope|notes|
|--:|:--:|:--:|:--|:--|:--:|:--:|:--|
|1|1.1.1|l1|WSTG-INPV-01|`<script>alert(1)</script>` を name パラに挿入。反映箇所でアラート確認。|T1059|in|ステージングで試す、XSSフィルタ回避は記録|
|2|1.1.2|l1|WSTG-INPV-02|`' OR '1'='1` を検索フォームに入れてエラー/挙動確認|T1190|in|DB型の検証は非破壊手順優先|