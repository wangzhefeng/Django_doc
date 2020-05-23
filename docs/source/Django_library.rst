
==============
Django 常用库
==============





------------
Library
------------




from django import forms

from django.contrib import admin
from django.contrib import auth
from django.contrib.auth.models import User
from django.contrib.contenttypes.models import ContentType
from django.contrib.contenttypes.fields import GenericForeignKey

from django.apps import AppConfig

from django.db import models
from django.db.models import Count

from ckeditor_uploader.fields import RichTextUploadingField

from django.test import TestCase

from django.urls import path
from django.urls import reverse
from django.urls import include

from django.shortcuts import render
from django.shortcuts import redirect
from django.shortcuts import get_object_or_404

from django.core.paginator import Paginator
from django.core.wsgi import get_wsgi_application


from django.conf import settings
from django.conf.urls.static import static

import os
import sys
import pymysql


------------
Introduction
------------