# SETUP（Claude Code 向けの手順書）

> このファイルは、利用者がこのリポジトリの URL を Claude Code に渡して「セットアップして」と頼んだときに、Claude が上から順に実行するための手順です。人が読んでも分かるように書いています。

## 0. 前提の確認

- Claude Code が使えること、Web 検索（WebSearch / WebFetch）が使えること
- 利用者が Obsidian で日記（1日1ファイル）を書いていること。日記が無くても動きますが、関心テーマの精度が落ちます

## 1. リポジトリを取得する

```bash
git clone <渡されたリポジトリのURL> /tmp/obsidian-daily-news
```



## 2. 利用者に聞く（まとめて1回で）

vault を軽く探して候補を出してから、次を確認する。推測で決めず、候補を示して選んでもらう。

1. vault の場所（例：`find ~ -maxdepth 4 -name ".obsidian" -type d` で候補を出す）
2. 日記フォルダとファイル名の形（vault 内で `YYYY-MM-DD.md` の形のファイルが多いフォルダを候補に出す）
3. 日記のどの見出しの前にニュースのリンクを入れるか（直近の日記を1つ開いて見出しを示す。無ければ先頭）
4. ニュースノートの保存先フォルダ（既定：`News`）
5. 必ず入れたいテーマ（任意。例：研究・仕事の業界）
6. 毎朝何時に作るか（既定：6:30。定時実行は標準でオンにする。不要と言われたときだけ外す）

## 3. スキルを置く

```bash
mkdir -p ~/.claude/skills/daily-news
cp /tmp/obsidian-daily-news/skills/daily-news/SKILL.md ~/.claude/skills/daily-news/
cp /tmp/obsidian-daily-news/skills/daily-news/config.example.md ~/.claude/skills/daily-news/config.md
```

`~/.claude/skills/daily-news/config.md` の値を、2 の回答で埋める。`NEWS_DIR` と `DATA_DIR` のフォルダが無ければ vault 内に作る（作る前に利用者に一言伝える）。

## 4. 「今日気をつけること」でも呼べるようにする（任意）

利用者が vault で使っている CLAUDE.md（vault 直下）があれば、次の1行を足してよいか聞き、了承されたら足す。

```markdown
- 「今日気をつけること」「今日のニュース」と言われたら daily-news スキルを使う
```

## 5. 毎朝の自動実行（標準）

定時実行を標準で登録する。利用者が「要らない」と言ったときだけ飛ばす。Claude Code の定時実行（デスクトップアプリなら scheduled tasks、CLI なら `/schedule`）で、次のプロンプトを毎朝の指定時刻に登録する。

```
daily-news スキル（~/.claude/skills/daily-news/SKILL.md）を読み、その手順どおりに今日の分を作ってください。無人実行なので、確認待ちで止まらないよう SKILL.md の「定時実行のとき」に従ってください。
```

- 定時実行は、許可の設定によっては確認待ちで止まる。登録後、利用者に「明日の朝、ノートができているか見てください」と伝える
- 止まるときは、vault への Read / Write / Edit と WebSearch / WebFetch を許可に入れるよう案内する
- PC がスリープ中だと動かないことがある（アプリの仕様による）

## 6. 試しに1回動かす

daily-news スキルをその場で1回実行し、できたニュースノートのパスと「今日の1本」を利用者に見せて終える。
