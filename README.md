# Django 基礎学習まとめ（詳細版）

> 📹 参考動画: https://youtu.be/r9QUdzVGHJU?si=ygOr0dDQ0E35cCQ3
> ✅ 視聴進捗: `0:41:10` まで完了（次回 `0:41:10` から再開）

---

## 目次

1. [Djangoとは何か](#1-djangoとは何か)
2. [Webアプリの基本的な仕組み](#2-webアプリの基本的な仕組み)
3. [環境構築・プロジェクト作成](#3-環境構築プロジェクト作成)
4. [アプリ作成とルーティング](#4-アプリ作成とルーティング)
5. [Viewの作成とテンプレート](#5-viewの作成とテンプレート)
6. [Pythonのデータをテンプレートに渡す](#6-pythonのデータをテンプレートに渡す)
7. [データベースとモデル](#7-データベースとモデル)
8. [マイグレーション](#8-マイグレーション)
9. [新規作成画面の実装（CreateView）](#9-新規作成画面の実装createview)
10. [全体の流れまとめ](#10-全体の流れまとめ)

---

## 1. Djangoとは何か

### Flaskとの比較

Pythonには複数のWebフレームワークがあるが、代表的なのが **Flask** と **Django**。

| | Flask | Django |
|---|---|---|
| 規模感 | 小〜中規模向け | 中〜大規模向け |
| 特徴 | 軽量・シンプル・自由度が高い | フルスタック・機能が豊富 |
| 自分で用意するもの | 多い（ほぼ全部） | 少ない（最初から備わっている） |
| 学習コスト | 比較的低い | 少し高いが、慣れると速い |

> **フルスタック** とは？
> Webアプリに必要な機能（ログイン、DB操作、管理画面、フォーム処理など）が
> **最初から全部セットになっている** という意味。
> Flaskは自分でライブラリを選んで組み合わせる必要があるが、
> Djangoは「全部入りパッケージ」をイメージするとわかりやすい。

### Djangoに最初から備わっている主な機能

- ユーザーのログイン・ログアウト管理
- データベースの操作（ORM）
- 管理画面（なんと自動生成される！）
- フォームの作成・バリデーション
- セキュリティ対策（CSRF、SQLインジェクション対策など）

---

## 2. Webアプリの基本的な仕組み

Djangoを学ぶ前に、そもそも**Webアプリがどう動くか**を理解しておくことが大切。

### リクエストとレスポンスの流れ

```
① ユーザーがブラウザでURLを入力（またはリンクをクリック）
        ↓
② ブラウザが「リクエスト」をサーバーに送る
        ↓
③ サーバー（Django）がリクエストの内容を判断して処理する
        ↓
④ サーバーが「レスポンス」（HTMLなど）をブラウザに返す
        ↓
⑤ ブラウザがHTMLを受け取って画面に表示する
```

> **リクエスト** = 「このページ見せて！」という要求
> **レスポンス** = 「はい、どうぞ！」という返答（HTMLや画像など）

### DjangoのMVTアーキテクチャ

Djangoは **MVT（Model・View・Template）** という設計パターンで動いている。

```
M = Model    → データベースのデータを管理する
V = View     → リクエストを受け取り、処理して、レスポンスを作る
T = Template → ユーザーに見せるHTMLの雛形
```

それぞれの役割を人間に例えると…

| 役割 | 人間で例えると |
|---|---|
| Model | 倉庫係（データを保管・取り出す） |
| View | 店員（注文を受けて、倉庫から商品を取ってきて渡す） |
| Template | 商品の陳列棚・包装紙（見た目を整えて渡す） |

---

## 3. 環境構築・プロジェクト作成

### Djangoのインストール

```bash
pip install django
```

> `pip` はPythonのパッケージ管理ツール。
> これを実行すると、Djangoとその関連ライブラリがまとめてインストールされる。

インストールできたか確認するには：

```bash
python -m django --version
# 例: 5.0.1 などのバージョン番号が表示されればOK
```

---

### プロジェクトの作成

```bash
django-admin startproject project
```

> `django-admin` はDjangoの管理コマンド。
> `startproject project` で「projectという名前のプロジェクトを作る」という意味。

実行後、以下のフォルダ・ファイルが自動生成される：

```
project/                    ← トップフォルダ（プロジェクト全体の入れ物）
│
├── manage.py               ← Djangoのいろんな操作をするためのコマンドツール
│                             （サーバー起動・DB操作などほぼ全部ここ経由）
│
└── project/                ← 設定フォルダ（プロジェクト名と同じ名前）
    ├── __init__.py         ← 「これはPythonパッケージです」と示すファイル（中身は空でOK）
    ├── settings.py         ← プロジェクト全体の設定（DB設定・アプリ登録・言語など）
    ├── urls.py             ← URLとViewの対応表（ルーティング）
    ├── asgi.py             ← 非同期サーバー用の設定（今は気にしなくてOK）
    └── wsgi.py             ← 本番サーバー用の設定（今は気にしなくてOK）
```

> `manage.py` はDjangoプロジェクトの「リモコン」みたいなもの。
> ほぼ全ての操作はこのファイルを通して行う。

---

### 開発サーバーの起動

```bash
cd project          # プロジェクトのトップフォルダに移動
python manage.py runserver
```

ターミナルに以下のような表示が出る：

```
Starting development server at http://127.0.0.1:8000/
```

ブラウザで `http://127.0.0.1:8000/` を開いて
**ロケットのイラスト画面** が表示されれば成功！

> `127.0.0.1` は自分のPC自身を指すアドレス（ローカルホスト）。
> `8000` はポート番号（Djangoのデフォルト）。
> つまり「自分のPC上でDjangoが動いているよ」という意味。

---

## 4. アプリ作成とルーティング

### DjangoにおけるアプリとはPlanet

Djangoでは **「プロジェクト」** の中に複数の **「アプリ」** を作る構造になっている。

```
プロジェクト（project）
├── アプリA（diary）   ← 日記機能
├── アプリB（accounts）← ログイン機能
└── アプリC（shop）    ← ショッピング機能
```

> 機能ごとにアプリを分けることで、コードが整理しやすくなる。
> 今回は「diary（日記）」というアプリを1つ作る。

---

### アプリの作成コマンド

```bash
# まずプロジェクトフォルダに移動
cd project

# diaryというアプリを作成
python manage.py startapp diary
```

実行後、`diary` フォルダが生成される：

```
project/
├── manage.py
├── project/
│   ├── settings.py
│   └── urls.py
│
└── diary/                      ← 新しく作られたアプリフォルダ
    ├── __init__.py
    ├── admin.py                ← 管理画面の設定
    ├── apps.py                 ← アプリの設定情報
    ├── migrations/             ← DB変更履歴を管理するフォルダ
    │   └── __init__.py
    ├── models.py               ← データベースのテーブル定義（Model）
    ├── tests.py                ← テストコード
    └── views.py                ← リクエスト処理・レスポンス生成（View）
```

---

### URLルーティングの設定

**ルーティング** とは「このURLが来たらこのViewを動かす」という対応表のこと。

Djangoでは2段階でルーティングを設定する。

#### Step 1: `project/urls.py`（プロジェクト全体のURL管理）

```python
from django.contrib import admin
from django.urls import path, include  # include を追加でインポート

urlpatterns = [
    path("admin/", admin.site.urls),  # 最初から書かれている管理画面のURL

    # 「diary/」で始まるURLは、diary アプリの urls.py に任せる
    path("diary/", include("diary.urls")),
]
```

> `include("diary.urls")` の意味：
> 「`diary/` というパスが来たら、`diary/urls.py` を見に行ってね」という委譲。
> こうすることで、アプリごとにURLを管理できる。

#### Step 2: `diary/urls.py`（アプリ内のURL管理・新規作成）

このファイルは最初は存在しないので、**自分で作る**。

```python
from django.urls import path
from . import views           # 同じフォルダ（.）の views.py をインポート

app_name = "diary"            # このアプリの名前空間（後で名前でURLを参照するために使う）

urlpatterns = [
    # path(パス文字列, 呼び出すView, URLの名前)
    path("", views.index, name="index"),
    #    ↑               ↑             ↑
    #    空文字=トップ    indexという    "index"という名前でこのURLを呼べる
    #                    View関数       （reverse()などで使う）
]
```

> `path("", ...)` の空文字は「diary/ の後に何も付かない」という意味。
> つまり `http://127.0.0.1:8000/diary/` にアクセスしたときに動くView。

#### URLの流れを整理すると

```
http://127.0.0.1:8000/diary/
        ↓
project/urls.py が "diary/" を見つけて diary/urls.py に処理を渡す
        ↓
diary/urls.py が "" （空）を見つけて views.index を呼ぶ
        ↓
views.py の index 関数が実行される
```

---

## 5. Viewの作成とテンプレート

### views.py の基本形

```python
# diary/views.py

from django.shortcuts import render   # HTMLテンプレートを返すための関数
from django.views import View         # Viewの基底クラス

class IndexView(View):
    # GETリクエスト（ページを見るとき）が来たら実行されるメソッド
    def get(self, request):
        # render(リクエスト, "テンプレートのパス")
        return render(request, "diary/index.html")

# クラスベースViewを、関数として使えるように変換する（urls.pyで使うため）
index = IndexView.as_view()
```

> **なぜクラスを使うのか？**
> GETリクエスト（ページ表示）とPOSTリクエスト（フォーム送信）を
> `get()` と `post()` メソッドで分けて書けるので、コードがわかりやすくなる。

> **`as_view()` とは？**
> クラスを `urls.py` で使えるように「関数の形」に変換するメソッド。
> `urls.py` は関数しか受け付けないため、この変換が必要。

---

### テンプレート（HTML）の作成

テンプレートファイルは以下の場所に作成する：

```
diary/
└── templates/              ← 自分で作るフォルダ
    └── diary/              ← アプリ名と同じフォルダを作る（名前の衝突を防ぐため）
        └── index.html      ← テンプレートファイル
```

> **なぜ `templates/diary/` と二重にフォルダを作るのか？**
> Djangoは複数のアプリのテンプレートを一か所でまとめて管理する。
> もし `templates/index.html` だけだと、他のアプリの `index.html` と衝突してしまう。
> `templates/diary/index.html` のようにアプリ名フォルダを挟むことで区別できる。

`index.html` の例：

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>日記トップ</title>
</head>
<body>
    <h1>日記アプリへようこそ</h1>
</body>
</html>
```

---

## 6. Pythonのデータをテンプレートに渡す

### 現在時刻をHTMLに表示する例

`views.py` でPythonの変数を用意して、テンプレートに渡すことができる。

```python
# diary/views.py

from django.shortcuts import render
from django.views import View
from datetime import datetime   # 日付・時刻を扱うライブラリ
from zoneinfo import ZoneInfo   # タイムゾーンを扱うライブラリ（Python 3.9以降）

class IndexView(View):
    def get(self, request):

        # 現在の日本時間を取得して、表示用の文字列に変換
        datetime_now = datetime.now(
            ZoneInfo("Asia/Tokyo")   # タイムゾーンを日本（JST）に指定
        ).strftime("%Y年%m月%d日 %H:%M:%S")
        # strftime() = 日時を指定フォーマットの文字列に変換するメソッド
        # %Y=年, %m=月, %d=日, %H=時, %M=分, %S=秒

        # render の第3引数に辞書でデータを渡す
        return render(
            request,
            "diary/index.html",
            {"datetime_now": datetime_now}
            # {"テンプレート側での変数名": Python側の変数}
        )

index = IndexView.as_view()
```

### テンプレート側での受け取り方

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>日記トップ</title>
</head>
<body>
    <h1>日記アプリへようこそ</h1>

    <!-- {{ 変数名 }} でPythonから渡された値を表示できる -->
    <p>現在時刻: {{ datetime_now }}</p>
</body>
</html>
```

> **`{{ }}` はDjangoテンプレート言語の記法。**
> `views.py` の辞書で渡したキー名（`datetime_now`）を `{{ }}` で囲んで書くと、
> その場所にPythonの値が埋め込まれて表示される。

### データの流れをまとめると

```
views.py
  datetime_now = "2025年06月01日 12:00:00"
        ↓ render(request, "diary/index.html", {"datetime_now": datetime_now})
templates/diary/index.html
  {{ datetime_now }}  →  "2025年06月01日 12:00:00" に置き換わって表示される
```

---

## 7. データベースとモデル

### DjangoのORM（Object Relational Mapping）とは

通常、データベースを操作するには **SQL** という言語を書く必要がある：

```sql
-- SQL の例（Djangoでは直接書かなくていい）
SELECT * FROM diary_page WHERE id = 1;
INSERT INTO diary_page (title, body) VALUES ('今日の日記', '今日はいい天気');
```

DjangoのORMを使えば、**PythonのコードだけでDBを操作できる**：

```python
# Djangoでの書き方（SQLを書かなくていい！）
Page.objects.all()              # 全データを取得
Page.objects.get(id=1)          # id=1のデータを取得
Page(title="今日の日記", body="今日はいい天気").save()  # データを保存
```

---

### モデルの定義（`diary/models.py`）

モデルとは「データベースのテーブル設計をPythonのクラスで書いたもの」。

```python
# diary/models.py

from django.db import models   # Djangoのモデル機能
import uuid                    # UUID（一意のID）を生成するライブラリ

class Page(models.Model):
    # ===== 各フィールドの説明 =====

    # id: プライマリキー（主キー）＝各レコードを一意に識別するID
    id = models.UUIDField(
        primary_key=True,      # これを主キーとして使う
        default=uuid.uuid4,    # 自動でUUIDを生成する（例: "550e8400-e29b-41d4-..."）
        editable=False,        # 管理画面などで編集不可にする
        verbose_name="ID"      # 管理画面での表示名
    )

    # title: 日記のタイトル
    title = models.CharField(
        max_length=100,        # 最大100文字
        verbose_name="タイトル"
    )

    # body: 日記の本文
    body = models.TextField(
        max_length=2000,       # 最大2000文字（長文OK）
        verbose_name="内容"
    )

    # page_date: 日記の日付
    page_date = models.DateField(
        verbose_name="日付"
    )

    # created_at: レコード作成日時（自動設定）
    created_at = models.DateTimeField(
        auto_now_add=True,     # 作成時に自動で現在日時を設定、以降は変更されない
        verbose_name="作成日付"
    )

    # updated_at: レコード更新日時（自動更新）
    updated_at = models.DateTimeField(
        auto_now=True,         # 保存するたびに自動で現在日時に更新される
        verbose_name="更新日付"
    )

    def __str__(self):
        # このメソッドはオブジェクトを文字列で表示するときに使われる
        # 管理画面でレコードを一覧表示したとき、タイトルが表示されるようになる
        return self.title
```

> **UUIDとは？**
> Universally Unique Identifier の略。
> `550e8400-e29b-41d4-a716-446655440000` のような形式で、
> 世界中どこで生成しても絶対にかぶらない識別子。
> 連番（1, 2, 3...）を使うより、URLから件数が推測されにくいメリットがある。

### よく使うフィールドの種類

| フィールド | 用途 | 例 |
|---|---|---|
| `CharField` | 短い文字列 | タイトル、名前 |
| `TextField` | 長い文字列 | 本文、説明 |
| `IntegerField` | 整数 | 年齢、数量 |
| `FloatField` | 小数 | 価格、評価 |
| `BooleanField` | True/False | 公開フラグ |
| `DateField` | 日付のみ | 誕生日 |
| `DateTimeField` | 日付＋時刻 | 作成日時 |
| `EmailField` | メールアドレス | ユーザーのメール |
| `UUIDField` | UUID | 一意のID |
| `ForeignKey` | 他のテーブルへの参照（1対多） | 著者、カテゴリ |

---

## 8. マイグレーション

### マイグレーションとは

モデル（Pythonコード）で定義したテーブル構造を、
実際のデータベースに反映させる作業のこと。

```
models.py（Pythonコード）  →  migration（設計図）  →  データベース（実際のDB）
```

> **なぜ2ステップ（makemigrations → migrate）に分かれているのか？**
> - `makemigrations` で「設計図」を作り、変更内容をファイルに記録しておく。
> - チームで開発するとき、その設計図をGitで共有すれば全員が同じDB構造にできる。
> - また、過去のバージョンに戻すことも可能になる。

---

### 手順①: settings.py にアプリを登録する（必須！）

マイグレーションの前に、`settings.py` の `INSTALLED_APPS` に `diary` を追加する。
**これを忘れると makemigrations がdiaryを認識しない。**

```python
# project/settings.py

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'diary',    # ← これを追加！
]
```

---

### 手順②: makemigrations（設計図を作る）

```bash
python manage.py makemigrations
```

実行すると `diary/migrations/` フォルダに設計図ファイルが生成される：

```
diary/
└── migrations/
    ├── __init__.py
    └── 0001_initial.py     ← 自動生成された設計図
```

> この `0001_initial.py` は自動生成されるもので、直接編集する必要はない。
> モデルを変更するたびに `0002_...`、`0003_...` と増えていく。

---

### 手順③: sqlmigrate（SQL確認・任意）

```bash
python manage.py sqlmigrate diary 0001
```

> これは確認用のコマンドで、実行しなくてもOK。
> 「どんなSQLが実行されるか」を事前に確認できる。

出力例：

```sql
BEGIN;
CREATE TABLE "diary_page" (
    "id" char(32) NOT NULL PRIMARY KEY,
    "title" varchar(100) NOT NULL,
    "body" varchar(2000) NOT NULL,
    ...
);
COMMIT;
```

---

### 手順④: migrate（実際にDBに反映する）

```bash
python manage.py migrate
```

> これを実行して初めてデータベースに変更が適用される。
> 実行後は `db.sqlite3` というファイルが更新される。

---

### マイグレーションの流れまとめ

```
① settings.py に 'diary' を追加
        ↓
② python manage.py makemigrations
   → diary/migrations/0001_initial.py が生成される
        ↓
③ python manage.py sqlmigrate diary 0001  （任意・確認用）
   → 実行されるSQLを確認できる
        ↓
④ python manage.py migrate
   → 実際のDBにテーブルが作られる
```

---

## 9. 新規作成画面の実装（CreateView）

### 全体の流れ

日記を新しく作成する画面を実装する。
必要なファイルは以下の3つ：

```
diary/
├── forms.py        ← 入力フォームの定義（新規作成）
├── views.py        ← 画面の処理ロジック
└── templates/
    └── diary/
        └── page_form.html   ← 入力画面のHTML（新規作成）
```

---

### Step 1: フォームの作成（`diary/forms.py`）

`forms.ModelForm` を使うと、モデルの定義を元に入力フォームを**自動生成**してくれる。

```python
# diary/forms.py（新規作成）

from django import forms        # Djangoのフォーム機能
from .models import Page        # 同じフォルダの models.py から Page クラスをインポート

class PageForm(forms.ModelForm):
    class Meta:
        model = Page            # どのモデルと連動させるか
        fields = ["title", "body", "page_date"]
        # どのフィールドをフォームに表示するか（id, created_at, updated_at は自動なので除外）
```

> **ModelFormのメリット：**
> - フォームの入力項目をゼロから手書きしなくていい
> - バリデーション（入力チェック）も自動でやってくれる
>   （例：`max_length=100` の設定が自動的に効く）
> - モデルと直接連携しているので、保存も簡単

---

### Step 2: Viewの実装（`diary/views.py`）

`CreateView` という汎用Viewを使うと、作成処理の大部分を自動でやってくれる。

```python
# diary/views.py

from django.views.generic.edit import CreateView   # 作成用の汎用View
from django.urls import reverse_lazy               # URLを名前で取得する関数
from .forms import PageForm                        # 先ほど作ったフォーム

class PageCreateView(CreateView):
    template_name = "diary/page_form.html"  # 使うHTMLテンプレートのパス
    form_class = PageForm                   # 使うフォームクラス
    success_url = reverse_lazy("diary:index")
    # success_url = フォーム送信成功後にリダイレクトするURL
    # reverse_lazy("diary:index") = "diary"アプリの"index"という名前のURLを取得
    # reverse_lazyを使うのは、起動時にURLが解決される前に評価されるのを防ぐため
```

> **`CreateView` が自動でやってくれること：**
> - GETリクエスト → フォームを表示する
> - POSTリクエスト → フォームのバリデーションをして、OKならDBに保存して`success_url`にリダイレクト
> - エラーがあったら → フォームにエラーメッセージを表示して再表示

---

### Step 3: URLを追加（`diary/urls.py`）

```python
# diary/urls.py

from django.urls import path
from . import views

app_name = "diary"
urlpatterns = [
    path("", views.index, name="index"),
    path("create/", views.PageCreateView.as_view(), name="create"),  # ← 追加
]
```

---

### Step 4: HTMLテンプレートの作成（`diary/templates/diary/page_form.html`）

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <title>日記を書く</title>
</head>
<body>
    <h1>日記を新規作成</h1>

    <!--
        method="POST" → フォームのデータをPOSTリクエストで送る
        action属性を省略すると、現在のURLに送信される（=同じViewのpost()が動く）
    -->
    <form method="POST">

        <!--
            {% csrf_token %} は絶対に必要！
            CSRF（クロスサイトリクエストフォージェリ）攻撃を防ぐためのセキュリティ対策。
            これを書かないと、DjangoがPOSTリクエストを拒否してエラーになる。
        -->
        {% csrf_token %}

        <!--
            {{ form.as_p }} で、フォームの入力項目を <p> タグで囲んで自動展開してくれる。
            他にも {{ form.as_table }} や {{ form.as_ul }} などがある。
        -->
        {{ form.as_p }}

        <button type="submit">保存する</button>
    </form>
</body>
</html>
```

> **CSRFとは？**
> 悪意のあるサイトから、ユーザーが気づかないうちにリクエストを送らせる攻撃手法。
> `{% csrf_token %}` を書くと、Djangoが発行したトークンがフォームに埋め込まれ、
> そのトークンがないPOSTリクエストはDjangoが自動的に拒否してくれる。

---

### フォーム送信の流れ

```
① ユーザーが http://127.0.0.1:8000/diary/create/ にアクセス（GETリクエスト）
        ↓
② CreateView の get() が動き、空のフォームを含む page_form.html を表示
        ↓
③ ユーザーがタイトル・本文・日付を入力して「保存する」ボタンを押す（POSTリクエスト）
        ↓
④ CreateView の post() が動き、入力値のバリデーションを実行
        ↓
⑤-A バリデーションOK → DBにデータを保存 → diary:index（トップページ）にリダイレクト
⑤-B バリデーションNG → エラーメッセージ付きでフォームを再表示
```

---

## 10. 全体の流れまとめ

### Djangoのファイル構成と役割

```
project/
├── manage.py               ← コマンドツール（サーバー起動・マイグレーションなど）
│
├── project/
│   ├── settings.py         ← 全体設定（DBの種類・インストール済みアプリ・言語など）
│   └── urls.py             ← 最初のURLルーター（アプリのurls.pyに処理を振り分ける）
│
└── diary/
    ├── models.py           ← DBのテーブル設計（Pythonクラスで書く）
    ├── forms.py            ← 入力フォームの定義
    ├── views.py            ← リクエスト処理・レスポンス生成
    ├── urls.py             ← このアプリ内のURLルーター
    ├── migrations/         ← DB変更履歴（makemigrationsで自動生成）
    └── templates/
        └── diary/
            └── *.html      ← ユーザーに見せるHTML
```

---

### リクエストからレスポンスまでの全体フロー

```
ユーザーがブラウザで URL を入力
        ↓
project/urls.py
  どのアプリのurls.pyに渡すか判断
        ↓
diary/urls.py
  どのViewを呼ぶか判断
        ↓
diary/views.py（View）
  GETなら → テンプレートにデータを渡してHTML生成
  POSTなら → フォームのデータをバリデーション → DBに保存
        ↓ （必要に応じて）
diary/models.py（Model）
  DBからデータを取得・保存
        ↓
templates/diary/*.html（Template）
  Viewから渡されたデータをHTMLに埋め込んで完成
        ↓
ブラウザに HTML が返される → 画面に表示される
```

---

### よく使うコマンド一覧

| コマンド | 説明 |
|---|---|
| `pip install django` | Djangoをインストール |
| `django-admin startproject プロジェクト名` | プロジェクトを作成 |
| `python manage.py startapp アプリ名` | アプリを作成 |
| `python manage.py runserver` | 開発サーバーを起動 |
| `python manage.py makemigrations` | マイグレーションファイルを作成 |
| `python manage.py migrate` | マイグレーションをDBに適用 |
| `python manage.py sqlmigrate アプリ名 番号` | 実行されるSQLを確認 |
| `python manage.py createsuperuser` | 管理者ユーザーを作成 |
| `python manage.py shell` | PythonのREPLでDjangoを操作 |

---

### テンプレートタグ・フィルター早見表

| 記法 | 用途 | 例 |
|---|---|---|
| `{{ 変数 }}` | 変数の値を表示 | `{{ datetime_now }}` |
| `{% tag %}` | テンプレートタグ（制御文） | `{% csrf_token %}` |
| `{% if 条件 %}...{% endif %}` | 条件分岐 | `{% if user.is_authenticated %}` |
| `{% for x in リスト %}...{% endfor %}` | 繰り返し | `{% for page in pages %}` |
| `{% url '名前' %}` | URLを名前で取得 | `{% url 'diary:index' %}` |
| `{{ 変数\|フィルター }}` | 値を加工して表示 | `{{ body\|truncatechars:100 }}` |

---

> 次回は `0:41:10` から！
> 次のトピック：日記の**一覧表示・詳細表示**あたりになるはず。