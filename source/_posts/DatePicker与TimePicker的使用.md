---
title: DatePicker与TimePicker的使用
date: 2017-07-20 21:09:16
tags: [andriod,安卓]
categories: [安卓学习]
---

### 安卓系统DatePicker与TimePicker的妙用

  其实安卓系统自带的DatePicker和TimePicker的界面都已经很好看啦~
可以直接拿过来直接用，当然也可以自定义。
  它们可以直接以界面的形式显示，也可以用new DatePickerDialog()的方法以弹框的形式显示，使用特别方便。不信？你看~
  main.xml:定义一个需要用到的Datepicker和TimePicker，首先然后先设置visibility为gone，<!-- more -->我想通过按钮来实现它们的显示效果，而不会两个同时显示时，无法完全显示在界面上。
```
	<DatePicker
        android:id="@+id/datePicker"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:visibility="gone"
        android:layout_weight="1" />

    <TimePicker
        android:id="@+id/timePicker"
        android:layout_width="match_parent"
        android:visibility="gone"
        android:layout_height="0dp"
        android:layout_weight="1" />
```

MainActivity.java类：添加四个按钮，分别实现不同的点击事件（事件的监听，三种方法：1.匿名内部类 2.在MainActivity类中实现 View.OnClickListener接口，3.自定义监听类，实现接口），完成TimePicker、DatePicker以及它们的弹框形式的分别显示。
```
package com.example.datetimepicker;

import android.app.DatePickerDialog;
import android.app.TimePickerDialog;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.DatePicker;
import android.widget.TimePicker;

import java.util.Calendar;

public class MainActivity extends AppCompatActivity implements View.OnClickListener{

    private TimePicker timePicker;
    private DatePicker datePicker;
    private Calendar calendar;
    private int year;
    private int month;
    private int day;
    private int hour;
    private int minute;

    private Button timeBtn;
    private Button dateBtn;
    private Button timeBtnDia;
    private Button dateBtnDia;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        timeBtn= (Button) findViewById(R.id.timePickerBtn);
        dateBtn= (Button) findViewById(R.id.datePickerBtn);
        timeBtnDia= (Button) findViewById(R.id.timePickerDia);
        dateBtnDia= (Button) findViewById(R.id.datePickerDia);

        //获取日历对象
        calendar=Calendar.getInstance();
        //获取年月日时分秒的信息
        year=calendar.get(Calendar.YEAR);
        month=calendar.get(Calendar.MONTH)+1;  //Calendar.MONTH获取的月份从0开始
        day=calendar.get(Calendar.DAY_OF_MONTH);
        hour=calendar.get(Calendar.HOUR_OF_DAY);
        minute=calendar.get(Calendar.MINUTE);
        setTitle(year+"-"+month+"-"+day+" "+hour+":"+minute);

        datePicker= (DatePicker) findViewById(R.id.datePicker);
        timePicker= (TimePicker) findViewById(R.id.timePicker);

        //datePicker的初始化日期Picker的年月日初始值
        datePicker.init(year, month-1, day, new DatePicker.OnDateChangedListener() {
            @Override
            public void onDateChanged(DatePicker datePicker, int i, int i1, int i2) {
                setTitle(i+"-"+(i1+1)+"-"+i2);
            }
        });

        timePicker.setOnTimeChangedListener(new TimePicker.OnTimeChangedListener() {
            @Override
            public void onTimeChanged(TimePicker timePicker, int i, int i1) {
                setTitle(i+":"+i1);
            }
        });

        //添加监听，在MainActivity中，直接添加this
        timeBtn.setOnClickListener(this);
        timeBtnDia.setOnClickListener(this);
        dateBtn.setOnClickListener(this);
        dateBtnDia.setOnClickListener(this);
        //使用匿名内部类的方法实现监听
        //initEvent();
    }

    public void showDatePickerDialog(){
        new DatePickerDialog(this, new DatePickerDialog.OnDateSetListener() {
            @Override
            public void onDateSet(DatePicker datePicker, int i, int i1, int i2) {
                setTitle(i+"-"+(i1+1)+"-"+i2);
            }
        },year,month-1,day).show();
    }

    public void showTimePickerDialog(){
        new TimePickerDialog(this, new TimePickerDialog.OnTimeSetListener() {
            @Override
            public void onTimeSet(TimePicker timePicker, int i, int i1) {
                setTitle(i+":"+i1);
            }
        },hour,minute,true).show();
    }

    @Override
    public void onClick(View view) {
        switch (view.getId()){
            case R.id.datePickerBtn:
                datePicker.setVisibility(View.VISIBLE);
                timePicker.setVisibility(View.GONE);
                break;
            case R.id.timePickerBtn:
                datePicker.setVisibility(View.GONE);
                timePicker.setVisibility(View.VISIBLE);
                break;
            case R.id.timePickerDia:
                showTimePickerDialog();
                break;
            case R.id.datePickerDia:
                showDatePickerDialog();
                break;
        }
    }

    public void initEvent(){
        dateBtn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                datePicker.setVisibility(View.VISIBLE);
                timePicker.setVisibility(View.GONE);
            }
        });
        timeBtn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                datePicker.setVisibility(View.GONE);
                timePicker.setVisibility(View.VISIBLE);
            }
        });
        dateBtnDia.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                showDatePickerDialog();
            }
        });
        timeBtnDia.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                showTimePickerDialog();
            }
        });
    }
}

```
注意事项：

![datetimepicker][1]

最后的显示效果：
<center>

 <img src="http://hexoblog-1253306922.cosgz.myqcloud.com/photo2017/TimePicker.jpg" width="150" height="150" />
 <img src="http://hexoblog-1253306922.cosgz.myqcloud.com/photo2017/DatePickerDialog.jpg" width="150" height="150" />
 </center>


  [1]: http://hexoblog-1253306922.cosgz.myqcloud.com/photo2017/datatimepicker.png