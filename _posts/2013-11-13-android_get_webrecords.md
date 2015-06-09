---
title: 'Android 获取自带浏览器上网记录'
layout: post
tags:
    - tech
    - java
    - android
---
先是搜索了一下，在manifest里添加

`<uses-permission android:name="com.android.browser.permission.READ_HISTORY_BOOKMARKS"/>  `

有了这个权限就可以读取上网记录和书签了。开始时我以为只有上网记录，但是明显bookmarks是表示书签啊。而书签一般是没有时间这个内容的。所以对query语句进行了修改，添加搜索限制条件。

```java
contentResolver.query(Uri.parse("content://browser/bookmarks"), new String[] { 
        "title", "url", "date" }, "date!=?",new String[] { "null" }, "date desc"); 
```

这句表示在路径“content：……bookmarks”里搜索title， url， date这三列，条件是date!=null，并按照日期降序排序。

其实最开始的时候我是没有添加时间的，但是想想获取上网记录也关心时间，就想添加这个属性，可是发现在三星某款手机里不可以，因为一开始我搜索的时候没有添加限制条件，所以连书签都检索出来了，就像之前说的，书签是不会有时间这个属性的（这应该是一般情况）。而很奇怪的是，之前没有修改的代码在小米上就可以运行，而且只是检索出来上网记录，没有包括书签（这才是特殊情况……）。应该是小米做了修改，啊啊，android的碎片化好头疼啊。

以下是全部代码：

```java
public class GetInternetRecord {
    String records = null;
    StringBuilder recordBuilder = null;

    public void getRecords(ContentResolver contentResolver) {
        // ContentResolver contentResolver = getContentResolver();
        Cursor cursor = contentResolver.query(
                Uri.parse("content://browser/bookmarks"), new String[] {
                        "title", "url", "date" }, "date!=?",
                new String[] { "null" }, "date desc");
        while (cursor != null && cursor.moveToNext()) {
            String url = null;
            String title = null;
            String time = null;
            String date = null;

            recordBuilder = new StringBuilder();
            title = cursor.getString(cursor.getColumnIndex("title"));
            url = cursor.getString(cursor.getColumnIndex("url"));

            date = cursor.getString(cursor.getColumnIndex("date"));

            SimpleDateFormat dateFormat = new SimpleDateFormat(
                    "yyyy-MM-dd hh:mm;ss");
            Date d = new Date(Long.parseLong(date));
            time = dateFormat.format(d);

            System.out.println(title + url + time);
        }
    }
}
```