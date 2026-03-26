# webフレームワーク「Django」の基礎
Django ジャンゴとは
pythonのwebフレームワーク、
flaskは軽量でシンプル
djangoはフルスタックで大規模開発OK

djangoをダウンロードする
pip install django

Projectを作成
django-admin startproject project

C:.
└─project　＜トップフォルダ
    └─project　＜設定フォルダ

アプリケーションを一つ作る
cd project
..\project> python manage.py startapp diary

urls.py
from django.urls import path
from . import views

app_name = "diary"
urlpatterns = [
    path("", views.index, name="index"),
]

path("", views.index, name="index"),
path(どんなパスか,動かす関数,名前),

views.py 
ブラウザ→urls.py→views.py　リクエストを受け取り、レスポンスをブラウザに送る


python側のデータを表示
from django.shortcuts import render
from django.views import View
from datetime import datetime <日時>
from zoneinfo import ZoneInfo <タイムゾーン>

class IndexView(View):
    def get(self, request):
        datetime_now = datetime.now(
            ZoneInfo("Asia/Tokyo")
        ).strftime("%Y年%m月%d日 %H:%M:%S")
        return render(
            request, "diary/index.html",
            {"datetime_now":datetime_now})｛"index.htmlの変数":python側の変数}
    
index = IndexView.as_view()


