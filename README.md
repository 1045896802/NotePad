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
    
  3.便签排序
  
  (1)list_options_menu.xml
  
    <item
          android:id="@+id/menu_sort"
          android:icon="@android:drawable/ic_menu_sort_alphabetically"
          android:showAsAction="always"
          android:title="@string/menu_sort">
          <menu>
              <item
                  android:id="@+id/menu_sort_createtime"
                  android:title="@string/menu_sort_createtime" />
              <item
                  android:id="@+id/menu_sort_updatetime"
                  android:title="@string/menu_sort_updatetime" />
              <item
                  android:id="@+id/menu_sort_title"
                  android:title="@string/menu_sort_title" />
          </menu>
      </item>
      
  (2)NoteList.java
    
    case R.id.menu_sort_createtime:
        cursor = managedQuery(
                getIntent().getData(),
                PROJECTION,
                null,
                null,
                NotePad.Notes._ID
        );
        adapter = new SimpleCursorAdapter(
                this,
                R.layout.noteslist_item,
                cursor,
                dataColumns,
                viewIDs
        );
        setListAdapter(adapter);
        return true;

    case R.id.menu_sort_updatetime:
        cursor = managedQuery(
                getIntent().getData(),
                PROJECTION,
                null,
                null,
                NotePad.Notes.DEFAULT_SORT_ORDER
        );
        adapter = new SimpleCursorAdapter(
                this,
                R.layout.noteslist_item,
                cursor,
                dataColumns,
                viewIDs
        );
        setListAdapter(adapter);
        return true;

    case R.id.menu_sort_title:
        cursor = managedQuery(
                getIntent().getData(),
                PROJECTION,
                null,
                null,
                NotePad.Notes.COLUMN_NAME_TITLE
        );
        adapter = new SimpleCursorAdapter(
                this,
                R.layout.noteslist_item,
                cursor,
                dataColumns,
                viewIDs
        );
        setListAdapter(adapter);
        return true;
        
  4.主题更改
  
  更改默认主题为白色
  
  AndroidManifest.xml
  
    <activity
        android:name=".NotesList"
        android:label="@string/title_notes_list"
        android:theme="@android:style/Theme.Holo.Light">
        <intent-filter>
            <action android:name="android.intent.action.MAIN" />

            <category android:name="android.intent.category.LAUNCHER" />
        </intent-filter>
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <action android:name="android.intent.action.EDIT" />
            <action android:name="android.intent.action.PICK" />

            <category android:name="android.intent.category.DEFAULT" />

            <data android:mimeType="vnd.android.cursor.dir/vnd.google.note" />
        </intent-filter>
        <intent-filter>
            <action android:name="android.intent.action.GET_CONTENT" />

            <category android:name="android.intent.category.DEFAULT" />

            <data android:mimeType="vnd.android.cursor.item/vnd.google.note" />
        </intent-filter>
    </activity>
    
    
    //日间模式
    case R.id.menu_theme_daytime:
        linearLayout = (LinearLayout) findViewById(R.id.layout);
        linearLayout.setBackgroundColor(Color.WHITE);
        title = (TextView) findViewById(R.id.title);
        title.setTextColor(Color.BLACK);
        timeStamp = (TextView) findViewById(R.id.timeStamp);
        timeStamp.setTextColor(Color.BLACK);
        return true;

    //夜间模式
    case R.id.menu_theme_night:
        linearLayout = (LinearLayout) findViewById(R.id.layout);
        linearLayout.setBackgroundColor(Color.BLACK);
        title = (TextView) findViewById(R.id.title);
        title.setTextColor(Color.WHITE);
        timeStamp = (TextView) findViewById(R.id.timeStamp);
        timeStamp.setTextColor(Color.WHITE);
        return true;
  
  5.笔记导出
  
    package com.example.android.notepad;

    import android.app.Activity;
    import android.database.Cursor;
    import android.net.Uri;
    import android.os.Environment;
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import android.view.View;
    import android.widget.EditText;
    import android.widget.Toast;

    import java.io.File;
    import java.io.FileOutputStream;
    import java.io.OutputStreamWriter;
    import java.io.PrintWriter;

    public class NoteExport extends Activity {
        private static final String[] PROJECTION = new String[]{
                NotePad.Notes._ID, // 0
                NotePad.Notes.COLUMN_NAME_TITLE, // 1
                NotePad.Notes.COLUMN_NAME_NOTE, // 2
                NotePad.Notes.COLUMN_NAME_CREATE_DATE, // 3
                NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE, // 4
        };
        private String TITLE;
        private String NOTE;
        private String CREATE_DATE;
        private String MODIFICATION_DATE;

        private Cursor mCursor;

        private EditText mName;
        private Uri mUri;
        private boolean flag = false;
        private static final int COLUMN_INDEX_TITLE = 1;

        public void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.note_export);
            mUri = getIntent().getData();
            mCursor = managedQuery(
                    mUri,        // The URI for the note that is to be retrieved.
                    PROJECTION,  // The columns to retrieve
                    null,        // No selection criteria are used, so no where columns are needed.
                    null,        // No where columns are used, so no where values are needed.
                    null         // No sort order is needed.
            );
            mName = (EditText) findViewById(R.id.export_name);
        }

        @Override
        protected void onResume() {
            super.onResume();
            if (mCursor != null) {
                // The Cursor was just retrieved, so its index is set to one record *before* the first
                // record retrieved. This moves it to the first record.
                mCursor.moveToFirst();
                mName.setText(mCursor.getString(COLUMN_INDEX_TITLE));
            }
        }

        @Override
        protected void onPause() {
            super.onPause();
            if (mCursor != null) {
                TITLE = mCursor.getString(mCursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_TITLE));
                NOTE = mCursor.getString(mCursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_NOTE));
                CREATE_DATE = mCursor.getString(mCursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_CREATE_DATE));
                MODIFICATION_DATE = mCursor.getString(mCursor.getColumnIndex(NotePad.Notes.COLUMN_NAME_MODIFICATION_DATE));
                if (flag == true) {
                    write();
                }
                flag = false;
            }
        }

        public void OutputOk(View v) {
            flag = true;
            finish();
        }

        private void write() {
            try {
                if (Environment.getExternalStorageState().equals(
                        Environment.MEDIA_MOUNTED)) {
                    File sdCardDir = Environment.getExternalStorageDirectory();
                    File targetFile = new File(sdCardDir.getCanonicalPath() + "/" + mName.getText() + ".txt");
                    PrintWriter ps = new PrintWriter(new OutputStreamWriter(new FileOutputStream(targetFile), "UTF-8"));
                    ps.println(TITLE);
                    ps.println(NOTE);
                    ps.println("创建时间：" + CREATE_DATE);
                    ps.println("修改时间：" + MODIFICATION_DATE);
                    ps.close();
                    Toast.makeText(this, "保存成功,保存位置：" + sdCardDir.getCanonicalPath() + "/" + mName.getText() + ".txt", Toast.LENGTH_LONG).show();
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }

 


  ## 三. 实验结果及截图
  
  1.增加时间戳显示
  
  ![Image text](https://github.com/1045896802/NotePad/blob/master/img/%E5%A2%9E%E5%8A%A0%E6%97%B6%E9%97%B4%E6%88%B3%E6%98%BE%E7%A4%BA.png)
  
  2.根据标题查找便签
  
  (1)查找页面
  
  ![Image text](https://github.com/1045896802/NotePad/blob/master/img/%E6%90%9C%E7%B4%A2%E6%A0%87%E9%A2%98-1.png)
  
  (2)查找结果
  
  ![Image text](https://github.com/1045896802/NotePad/blob/master/img/%E6%90%9C%E7%B4%A2%E6%A0%87%E9%A2%98-2.png)
  
  3.便签排序
  
  (1)排序前
  
  ![Image text](https://github.com/1045896802/NotePad/blob/master/img/便签排序-1.png)
  
  (2)创建时间排序结果
  
  ![Image text](https://github.com/1045896802/NotePad/blob/master/img/便签排序-2.png)
  
  4.主题更改
  
  (1)日间模式:白底黑字
  
  (2)夜间模式:黑底白字

  5.笔记导出
  
  
  
  



