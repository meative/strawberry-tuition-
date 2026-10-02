# strawberry-tuition- 開発ガイド（Claude Code 用）

## 0. 作業フォルダ

- 作業フォルダは **`~/dev/strawberry-tuition-/`**（2026-10-02 に iCloud 同期の外へ引っ越し）
- **`~/Documents/strawberry-tuition-_OLD_iCloud/` は編集禁止**（旧フォルダの残置。origin より遅れた古い状態。参照のみ）
- 姉妹リポジトリは `~/dev/strawberry-field/`（同日に引っ越し済み。詳細はそちらの CLAUDE.md）
- **作業マシンは2台**（このMac＋Mac mini）。「このMacが正」という前提は置けない

## 1. デプロイ手順（この順を必ず守る）

push した main がそのまま本番（GitHub Pages: https://meative.github.io/strawberry-tuition-/ ）。

1. **pull** — 毎回いちばん最初に実行。古いファイルを編集すると本番の変更を巻き戻す
   ```bash
   cd ~/dev/strawberry-tuition-
   git fetch origin main
   git log --oneline HEAD..origin/main   # 空でなければ、このMacが遅れている
   git log --oneline origin/main..HEAD   # 空でなければ、未pushがある
   git pull --ff-only origin main        # 作業ツリーがきれいな前提。衝突したら勝手にマージせずユーザーに相談
   ```
2. **編集** — アプリ本体は `index.html` 1ファイル（HTML＋インラインJS 約4800行）。操作説明は `manual.html`
3. **node --check** — インラインJSを抜き出して構文チェック
   ```bash
   python3 -c "import re;open('/tmp/app.js','w').write('\n;\n'.join(re.findall(r'<script>(.*?)</script>',open('index.html').read(),re.S)))" && node --check /tmp/app.js
   ```
4. **検証** — ヘッドレスで改修版と pristine（`git show HEAD:index.html`）を突き合わせ、変更対象以外の出力が完全一致・JSエラー0 を確認する。Playwright は `~/Documents/strawberry-field_OLD_iCloud/node_modules/playwright` を `require` して流用できる（ブラウザ本体は `~/Library/Caches/ms-playwright`）
5. **push** — 検証が通ってから `git push origin main`（= 本番反映）

## 2. 履歴書き換え禁止

- **`git push --force` / `git rebase` / `git commit --amend`（push 済みコミットに対して）/ `git reset --hard origin より前` は禁止。** 2台運用のため履歴を書き換えるともう一方のマシンが壊れる
- 取り消しは `git revert` で前に進める

## 3. その他の約束

- 保存データ（localStorage／Firestore のデータ構造）は原則書き換えず、挙動変更は出力・表示時のルール適用で行う（例: SF-OAKTREE-TATEKAE-20261002）
- コミットメッセージは日本語で「何を・なぜ」＋案件タグ（例: `SF-XXXX-YYYYMMDD`）
- Edit は「適用したつもりで未適用」が起きるので、必ず grep / sed で実物を確認する
