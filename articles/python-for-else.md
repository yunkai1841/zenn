---
title: "Pythonでfor文の後にelseを使う"
emoji: "🌟"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["python"]
published: true
---


Pythonではforブロックの後にelseを使うことができます。
例えば、forブロックの中でフラグを立てて、forブロックの後でフラグを確認するようなことを、elseブロックで行うことができます。

forブロックの後のelseブロックは、forが最後まで実行されたときに、実行されます。
しかし、forブロックの中でbreakを使ってforを抜けた場合は、elseブロックは実行されません。

```python
for i in range(5):
    print(i)
else:
    print("loop done")

# 出力
# 0
# 1
# 2
# 3
# 4
# loop done
```

```python
for i in range(5):
    if i == 3:
        break
    print(i)
else:
    print("loop done")

# 出力
# 0
# 1
# 2
```

このようにflagを使わずに、forブロックの後にelseを使うことで、コードが簡潔になります。
また、flagに値を代入し忘れるというミスを防ぐこともできます。

flagを使う場合は、以下のようになります。

```python
flag = False
for i in range(5):
    if i == 3:
        flag = True
        break
    print(i)

if flag:
    print("loop done")
```