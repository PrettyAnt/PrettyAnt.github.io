---
layout: post
title:  TableLayout
categories: Android
keywords: Android
mathjax: true
prism: [Android]
---
#  让TabLayout内部的Tab有间隔
```
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:state_selected="true">
        <!--<shape>
         <solid android:color="#ffffff"/>
         <corners android:topLeftRadius="10dp" android:topRightRadius="10dp" />
        </shape>-->
        <!--为了让TabLayout内部的Tab有间隔，暂时找不到其他设置方法，只能在背景图形里面设置间隔-->
        <layer-list>
            <item>
                <shape>
                    <solid android:color="@android:color/transparent"/>
                </shape>
            </item>
            <item android:left="5dp" android:right="5dp">
                <shape>
                    <corners android:topLeftRadius="2dp" android:topRightRadius="2dp" />
                    <solid android:color="@color/white"/>
                    <stroke android:color="@color/white"/>
                </shape>
            </item>
        </layer-list>
    </item>
    <item android:state_selected="false">
        <!--<shape>
         <solid android:color="#bcbcbc"/>
         <corners android:topLeftRadius="10dp" android:topRightRadius="10dp" />
        </shape>-->
        <layer-list>
            <item>
                <shape>
                    <solid android:color="@android:color/transparent"/>
                </shape>
            </item>
            <item
                android:drawable="@drawable/hotissue_background"
                android:left="5dp" android:right="5dp">
                <!--<shape>-->
                    <!--<corners android:topLeftRadius="16px" android:topRightRadius="16px" />-->
                    <!--<solid android:color="#b3e2fe"/>-->
                    <!--<stroke android:color="#b3e2fe"/>-->
                <!--</shape>-->
            </item>
        </layer-list>
    </item>
</selector>
```
