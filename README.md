# NotePad笔记本应用

## 一. 实验内容

本实验将基于NotePad应用做功能扩展

1.下载NotePad源码：https://github.com/llfjfz/NotePad

2.阅读NotePad的源代码并做如下扩展：

(1)基本要求：每个人必须完成

NoteList中显示条目增加时间戳显示

添加笔记查询功能（根据标题查询）
  
(2)附加功能：根据自身实际情况进行扩充，以下是建议的扩展功能
UI美化

更改记事本的背景

导出笔记，笔记的云备份和恢复

添加代办功能

笔记分类

支持多种资源，如保存图片、语音、视频等（参考印象笔记）

语音搜索？

笔记便签

。。。。。。

3.参考应用

(1)彩色笔记：http://www.cncrk.com/downinfo/100168.html

(2)印象笔记：https://help.yinxiang.com/hc/zh-cn/articles/219925607

4.参考文献

(1)数据存储的基本知识：

https://developer.android.google.cn/guide/topics/data/data-storage

(2)使用SQLite数据库进行数据存储

https://developer.android.google.cn/training/data-storage/sqlite

(3)ContentProvider: ContentProvider用于数据共享，如果你不提供数据共享机制，可以不使用

https://developer.android.google.cn/guide/topics/providers/content-provider-basics

https://developer.android.google.cn/guide/topics/providers/content-provider-creating

## 二. 关键代码

  1.增加时间戳显示
  
  (1)notelist_item.xml
  
    <?xml version="1.0" encoding="utf-8"?>
    <!-- Copyright (C) 2010 The Android Open Source Project

         Licensed under the Apache License, Version 2.0 (the "License");
         you may not use this file except in compliance with the License.
         You may obtain a copy of the License at

              http://www.apache.org/licenses/LICENSE-2.0

         Unless required by applicable law or agreed to in writing, software
         distributed under the License is distributed on an "AS IS" BASIS,
         WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
         See the License for the specific language governing permissions and
         limitations under the License.
    -->

    <!--添加一个垂直的线性布局-->
    <LinearLayout  xmlns:android="http://schemas.android.com/apk/res/android"
        android:id="@+id/layout"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">
        <!--原标题TextView-->
        <TextView xmlns:android="http://schemas.android.com/apk/res/android"
            android:id="@android:id/title"
            android:layout_width="match_parent"
            android:layout_height="?android:attr/listPreferredItemHeight"
            android:textAppearance="?android:attr/textAppearanceLarge"
            android:gravity="center_vertical"
            android:paddingLeft="5dip"
            android:singleLine="true"/>
        
        <!--添加显示时间的TextView-->
        <TextView
            android:id="@+id/timeStamp"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:textAppearance="?android:attr/textAppearanceSmall"
            android:paddingLeft="5dip"/>
    </LinearLayout>
  
  （2）NotePadProvider.java
   
    // Gets the current system time in milliseconds
    Long now = Long.valueOf(System.currentTimeMillis());
    Date date = new Date(now);
    SimpleDateFormat format = new SimpleDateFormat("yyyy.MM.dd HH:mm:ss");
    String dateTime = format.format(date);

    // If the values map doesn't contain the creation date, sets the value to the current time.
    if (values.containsKey(NotePad.Notes.COLUMN_NAME_CREATE_DATE) == false) {
        values.put(NotePad.Notes.COLUMN_NAME_CREATE_DATE, dateTime);
    }
    
  2.根据标题查找便签
  
  (1)note_search.xml
  
    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical">

        <SearchView
            android:id="@+id/search_view"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_alignParentTop="true"
            android:iconifiedByDefault="false"
            android:queryHint="请输入搜索标题"></SearchView>

        <ListView
            android:id="@android:id/list"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"></ListView>
    </LinearLayout>  

  ## 三. 实验结果及截图
  
  1.增加时间戳显示
  
  ![Image text]    (https://github.com/1045896802/NotePad/blob/master/img/%E5%A2%9E%E5%8A%A0%E6%97%B6%E9%97%B4%E6%88%B3%E6%98%BE%E7%A4%BA.png)
  
  2.根据标题查找便签
  
  ![Image text](https://github.com/1045896802/UIcomponents/blob/master/img/2r.png)
  
  3.使用XML定义菜单
  
  ![Image text](https://github.com/1045896802/UIcomponents/blob/master/img/3r.png)
  
  4.创建上下文操作模式(ActionMode)的上下文菜单
  
  ![Image text](https://github.com/1045896802/UIcomponents/blob/master/img/4r.png)



