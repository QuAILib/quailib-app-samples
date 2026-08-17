# 配置・開始時の注意

## 同じリポジトリで進めてよいか

同じリポジトリ、同じClaude Desktopのプロジェクトフォルダで進めてよい。

ただし、qklearn開発と同じClaude Codeセッションは使用せず、新しいセッションを作成する。

理由：

- Claude Desktopの各Gitセッションは独立worktreeになる
- qklearnの未コミット変更と混在しない
- コンテキストとチュートリアル固有ルールを分離できる
- qklearn用venvを汚染しない

## 配置先

このZIPの`tutorials/cirquit_cut/`配下を、リポジトリの同じパスへ配置する。

既存の元Notebookは上書きしない。

## 元Notebook保護設定

既存`.claude/settings.json`を丸ごと上書きしない。

permissions.denyへ次のルールを追加する。

`Edit(/tutorials/cirquit_cut/transportation_curcuit_cut_tutorial.ipynb)`

## 専用venv

qklearn用venvは使用しない。

推奨：

- `CIRCUIT_CUT_VENV=C:\Users\E1804\work\venvs\circuit-cut-py311`
- `CIRCUIT_CUT_PYTHON=C:\Users\E1804\work\venvs\circuit-cut-py311\Scripts\python.exe`
- `CIRCUIT_CUT_CACHE=C:\Users\E1804\work\cache\circuit-cut`

Gate Aで実際の依存互換性を確認する。

## 新しいセッション

- Project folderはこれまでと同じリポジトリルート
- EnvironmentはLocal
- Permission modeはPlan
- qklearnの既存セッションはそのまま残す
- circuit-cut用に新規セッションを作る
