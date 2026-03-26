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


SQLite リレーショナルデータベース
UUID ほかの識別子とはかぶらない識別子を生成する
from django.db import models
import uuid 

class Page(models.Model):
    id = models.UUIDField(primary_key=True, default=uuid.uuid4,editable=False,verbose_name="ID")
    title =models.CharField(max_length=100,verbose_name="タイトル")
    body = models.TextField(max_length=2000,verbose_name="内容")
    page_date =models.DateField(verbose_name="日付")
    created_at = models.DateTimeField(auto_now_add=True,verbose_name="作成日付")
    updated_at = models.DateTimeField(auto_now=True,verbose_name="更新日付")

    def __str__(self):
        return self.title
ほかにもいろんなフィールドがある


マイグレーション用のファイルを作るコマンド
 python manage.py makemigrations
 python manage.py sqlmigrate diary 0001
 python manage.py migrate