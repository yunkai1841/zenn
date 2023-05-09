---
title: "vscodeでMakefileで作ったバイナリをデバッグしたい"
emoji: "👻"
type: "idea" # tech: 技術記事 / idea: アイデア
topics: ["c", "vscode", "makefile", "debug"]
published: true
---

vscodeのGUIでデバッグするのは、gdbのCUIでデバッグするより圧倒的に楽です。
そこで、vscodeでデバッグするための設定を紹介します。

なおこの設定は、C言語のプロジェクトでmakeを使ってビルドしている場合の設定です。
ほかの言語やビルドツールを使っている場合は、適宜読み替えてください。

以下のファイルを作成し、配置します。
```bash
.vscode
├── launch.json
└── tasks.json
```

# tasks.json

tasks.jsonは、vscodeでタスクを実行するための設定ファイルです。

- `command`には、実行するコマンドを指定します。
- `args`には、実行するコマンドに渡す引数を指定します。

```json:tasks.json
{
    "tasks": [
        {
            "type": "process",
            "label": "build",
            "command": "make",
            "args": [
                "all",
            ],
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "options": {
                "cwd": "${workspaceFolder}"
            },
            "problemMatcher": [
                "$gcc"
            ],
        }
    ],
    "version": "2.0.0"
}
```

# launch.json

launch.jsonは、vscodeでデバッグするための設定ファイルです。

- `program`には、デバッグ対象のバイナリのパスを指定します。
- `args`には、デバッグ対象のバイナリに渡す引数を指定します。
- `preLaunchTask`には、デバッグ実行前に実行するタスクを指定します。
デバッグ前にmakeしたいので、先ほど作成した`build`タスクを指定しました。

```json:launch.json
{
    "configurations": [
        {
            "name": "Run binary",
            "type": "cppdbg",
            "request": "launch",
            "program": "${workspaceFolder}/PATH_TO_BINARY",
            "args": [
                "ARGUMENTS"
            ],
            "stopAtEntry": false,
            "cwd": "${workspaceFolder}",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "gdb の再フォーマットを有効にする",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                },
                {
                    "description": "逆アセンブリ フレーバーを Intel に設定",
                    "text": "-gdb-set disassembly-flavor intel",
                    "ignoreFailures": true
                }
            ],
            "preLaunchTask": "build",
        }
    ],
    "version": "2.0.0"
}
```

# ショートカットキー
ショートカットキーを覚えると、より便利になります。

| キー             | 機能           |
| ---------------- | -------------- |
| F5               | デバッグを開始 |
| Ctrl + Shift + B | ビルド         |
