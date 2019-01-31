---
title: 使用Android Canvas绘制折线图
date: 2017-12-08 09:17:58
tags: [安卓,漏磁检测,Andriod]
categories: [安卓学习]
---


## 优化画图方法

​	原先磁感应力检测仪项目的绘图方法一直是采用的Android AChartEngine方法，简单介绍一下AChartEngine，它是 Android 平台的图表开发库, 能绘制 折线图, 饼图, 气泡图, 柱状图, 散点图, 面积图等统计图表。网上有很多例子，推荐一个[Android 图表绘制 achartengine 示例解析][1]。该方法优点：使用简单，只需要将数据传入到数据集，并设置相应渲染器的绘制颜色，线的粗细等等。缺点：当数据量较大时，在移动与缩放时会显得比较卡顿。
 <!--more-->
	接下来就介绍一种新的绘制方法：**Android Canvas方法**
	当我们在自定义 View的时候，我们经常需要绘制一些自己想要的效果。通过Canvas对象我们就可以绘制出我们自己想要的效果。比如折线图、平面图等等。
对于一些简单的原理操作网上有很多Demo，推荐一个我在简书上面看到的一篇[Android Canvas 方法总结][2]，里面介绍了关于Canvas的平移、缩放、旋转，以及后面使用Canvas对象绘制直线、各种形状以及文字。通过图形的展示，我相信很快就可以看懂啦！

​	正式开始我做的内容： 

 - 首先贴一张我们所需要最终达到的效果图
<img src="http://hexoblog-1253306922.cosgz.myqcloud.com/photo2017/%E6%8A%98%E7%BA%BF%E5%9B%BE%E5%8F%8A%E6%9B%B2%E7%BA%BF%E5%9B%BE%E5%AE%8C%E6%95%B4%E6%98%BE%E7%A4%BA%E6%95%88%E6%9E%9C.png" width="50%" height="50%">

1. **坐标线的绘制：**

   此时需要注意的是Canvas画布的坐标是以 *左上角为坐标原点的，水平向右为X轴的正方向，垂直向下为Y轴的正方向*，而为了美观以及需求，我们需要将**坐标原点设置在左下角，同时Y轴的正方向为垂直向上**。而达到最终效果。坐标原点设置在左下角可以通过简单的平移画布而实现坐标（0，0）位于View的左下角，但是方向岂不是还要通过翻转？ 这里其实并没有翻转这个方法，我指的其实就是通过坐标数据计算它对应的位置。画布的坐标原点并非你所显示的坐标原点。这样会使得画图麻烦很多。此时通过一个巧妙地方法，坐标数据的Y值我给它一个负号，那么如果是（2，5）和（3，7），而我实际将它们绘制在画布上的（2，-5）和（3，-7）；而-7比-5小，正好-7就在-5的上方，从而模拟出正方形为垂直向上。

``` java
    //绘制坐标线
    @Override
    public void drawAxis(Canvas canvas) {
        canvasWidth -= 140f;        //留有边距以显示坐标对应的值
        canvasHeight -= 140f;
        canvas.translate(110f, canvasHeight + 40f);     // 使画布向 x 轴正向移动 110f，向 y 轴移动 40f
        canvas.drawLine(0, 0, canvasWidth, 0, paint);   // 绘制 x 轴
        canvas.drawLine(0, 0, 0,-canvasHeight, paint);  // 绘制 y 轴
    }
```

2. **坐标刻度的绘制**

   坐标主要需要完成动态的适配，当平移和缩放的时候坐标动态显示，并且完成它们之间的间距控制，所显示的画布上坐标轴上只显示2-6个坐标，避免坐标刻度太密而使得刻度值挤在一块无法显示。实现思路是：
   a.通过`float interval = measureInterval(xDistance/xScale);        //每组值得间隔`计算当前显示区域的相邻坐标的间距。
   b.通过`int n = (int) Math.ceil((xDistance/xScale)/interval);      //一组有几个值`计算当前显示区域的坐标数量也就对于上述的（2-6）
   c.通过

``` java
int first = (int) Math.floor((xStart-((1-xScale)/2+xTranslate/canvasWidth)*xDistance/xScale)/interval);
```
​	计算出所显示的首坐标的位置。
	d.循环n次，依次显示n个从first+i开始的坐标刻度值。

``` java
// 所需要的坐标属性：
protected float xDistance;              // x 方向总距离
protected float yDistance;              // y 方向总距离
protected float xStart=0, yStart=0;     // 起始点坐标
protected float xTranslate = 0, yTranslate = 0;   // 分别控制 x 和 y 方向的平移距离，单位为屏幕像素，达到手指移动多少，坐标平移多少
protected float xScale = 1, yScale = 1;           // 分别控制 x 和 y 方向的缩放程度
```
``` java
//绘制坐标值及刻度
@Override
public void drawCoordinates(Canvas canvas) {
    String number;
    DecimalFormat df1 = new DecimalFormat("0.0");
    DecimalFormat df2 = new DecimalFormat("0.00");
    DecimalFormat df3 = new DecimalFormat("0.000");
    // 绘制 x 轴坐标
    canvas.save();  // 保存画布状态
    canvas.clipRect(0, 0, canvasWidth, 60f);                   // 切割画布，使坐标显示在一定范围内
    canvas.translate((1 - xScale) / 2 * canvasWidth, 0);       // 缩放时，平移使得与折线图一致
    canvas.translate(xTranslate, 0);                           // 使坐标跟着图形一起平移

    float interval = measureInterval(xDistance/xScale);        //每组值得间隔
    int n = (int) Math.ceil((xDistance/xScale)/interval);      //一组有几个值
    int first = (int) Math.floor((xStart-((1-xScale)/2+xTranslate/canvasWidth)*xDistance/xScale)/interval);
    for (int i = 0; i <= n; i++){
        if (interval < 0.01){
            number = df3.format((first+i)*interval);
        }else if (interval <= 0.1){
            number = df2.format((first+i)*interval);
        }else if (interval < 1){
            number = df1.format((first+i)*interval);
        }else {
            number = String.valueOf((first+i)*(int)interval);
        }
        canvas.drawLine((canvasWidth * (first+i) * interval / xDistance * xScale), 0f,
                (canvasWidth * (first+i) * interval / xDistance * xScale), 10f,paint);
        canvas.drawText(
                number,
                (canvasWidth * (first+i) * interval / xDistance * xScale),
                40f,
                paint);
    }
    canvas.restore();   // 使画布返回上一个状态

    // 绘制 y 轴坐标
    canvas.save();  // 保存画布状态
    canvas.clipRect(-110f, -canvasHeight, 0, 0);                        // 切割画布，使坐标显示在一定范围内
    canvas.translate(0, -(1 - yScale) / 2 * canvasHeight);              // 缩放时平移使得与折线图一致
    canvas.translate(0, (yTranslate/yScale-yStart/yDistance*canvasHeight)*yScale);      // 使坐标跟着图形一起平移
    interval = measureInterval(yDistance/yScale);                       //每组值得间隔
//        Log.d("Chart","interval="+interval);
    n = (int) Math.ceil((yDistance/yScale)/interval);                   //一组有几个值
//        Log.d("Chart","n="+n);
    first = -(int) Math.floor((yStart+yDistance/yScale/2*(1-yScale)-yTranslate/yScale/canvasHeight*yDistance)/interval);
//        Log.d("Chart","first="+first);
    for (int i = 0; i <= n; i++){
        if (interval < 0.01){
            number = df3.format((first+i)*interval);
        }else if (interval <= 0.1){
            number = df2.format((first+i)*interval);
        }else if (interval < 1){
            number = df1.format((first+i)*interval);
        }else {
            number = String.valueOf((first+i)*(int)interval);
        }
        canvas.drawLine(-10f, -(canvasHeight * (first+i) * interval / yDistance * yScale),
                0f, -(canvasHeight * (first+i) * interval / yDistance * yScale),paint);
        canvas.drawText(
                number,
                -5f,
                -(canvasHeight * (first+i) * interval / yDistance * yScale)-4,
                paint);
    }
    canvas.restore();   // 使画布返回上一个状态
}
```
​	以下为坐标完成动态适配的效果图：
<img src="http://hexoblog-1253306922.cosgz.myqcloud.com/photo2017/%E5%9D%90%E6%A0%87%E5%8A%A8%E6%80%81%E9%80%82%E9%85%8D%E6%95%88%E6%9E%9C.png" width="50%" height="50%">

3. **折线与图形的绘制**

   由于需要绘制两种不同的图形，去噪图（折线图）和扫描图（平面图），此处将画图方法抽象出来：子类通过重写drawAction方法来完成各自所需绘制的图形。同时后期如果需要绘制其他类型的图，继承此抽象类然后重写自己的drawAction方法即可实现不同类型的图形绘制。

   ![AbstractChartService画图抽象类][5]

   以下为折线图的drawAction方法：

   根据平移与缩放值，将画布进行相应的平移与缩放，同时设置图形的缩放中心，以所显示图形的中心点为缩放中心。然后设置画笔样式及颜色，通过所获取的xList与yList值连接相邻点达到绘制折线的效果。

``` java
//绘制曲线
@Override
public void drawAction(Canvas canvas) {
    // 裁切矩形，把画面控制在坐标平面内
    canvas.clipRect(0, 0, canvasWidth, -canvasHeight);
    // 手势缩放移动
    canvas.translate(xTranslate/xScale, yTranslate/yScale-yStart/yDistance*canvasHeight);
    float px = (canvasWidth / 2 - xTranslate/xScale);
    float py = (canvasHeight / 2 + yTranslate/yScale-yStart/yDistance*canvasHeight);
    canvas.scale(xScale, yScale, px, -py);               //以图的中心点缩放

    paint.setDither(true);                           //设定是否使用图像抖动处理，会使绘制出来的图片颜色更加平滑和饱满，图像更加清晰
    paint.setFilterBitmap(true);                     //如果该项设置为true，则图像在动画进行中会滤掉对Bitmap图像的优化操作，加快显示速度，本设置项依赖于dither和xfermode的设置
    paint.setStyle(Paint.Style.FILL_AND_STROKE);     //设置画笔的样式，Style.FILL: 实心   STROKE:空心   FILL_OR_STROKE:同时实心与空心
    paint.setStrokeJoin(Paint.Join.ROUND);           //设置绘制时各图形的结合方式，如平滑效果等  BEVEL斜角
    //paint.setStrokeWidth(4/xScale);                  //当画笔样式为STROKE或FILL_OR_STROKE时，设置笔刷的粗细度
    int[] colors = GlobalParameter.getInstance().getColors();
    // 绘制图形
    for (int i=0; i<showIDs.size(); i++) {           //通道数
        for (int j=0; j< yList.get(showIDs.get(i)).size()-1; j++) {
            paint.setColor(colors[showIDs.get(i)]);

            if ((yList.get(showIDs.get(i)).get(j+1)-yList.get(showIDs.get(i)).get(j))<15&&(yList.get(showIDs.get(i)).get(j+1)-yList.get(showIDs.get(i)).get(j))>-15){
                paint.setStrokeWidth(3/yScale);
            }else {
                paint.setStrokeWidth(3/xScale);
            }
            canvas.drawLine(        //xDistance x方向的总距离
                    xList.get(j) / xDistance * canvasWidth,
                    -yList.get(showIDs.get(i)).get(j) / yDistance * canvasHeight,
                    xList.get(j+1) / xDistance * canvasWidth,
                    -yList.get(showIDs.get(i)).get(j+1) / yDistance * canvasHeight,
                    paint
            );
        }
    }
    if (points!=null){
        paint.setColor(Color.RED);
        for (int i=0;i<points.size()-1;i++){
            canvas.drawLine(        //xDistance x方向的总距离
                    points.get(i).getX() / xDistance * canvasWidth,
                    -points.get(i).getY() / yDistance * canvasHeight,
                    points.get(i+1).getX() / xDistance * canvasWidth,
                    -points.get(i+1).getY() / yDistance * canvasHeight,
                    paint
            );
        }
    }
    initPaints();   //画笔reset
}
```
​	以下为PopWindow弹出框中横屏显示折线图的效果：
<img src="http://hexoblog-1253306922.cosgz.myqcloud.com/photo2017/%E5%A4%A7%E5%9B%BE%E6%98%BE%E7%A4%BA%E6%95%88%E6%9E%9C.png" width="50%" height="50%">

4. **Touch手势事件的监听**

   Android Canvas 没有提供有关手势缩放的功能，但我们可以利用onTouchListener 来监测手势，并根据手势的不同对扫描图作不同处理，比如移动和缩放。采用接口回调机制，在所需用到AbstractChartService对象处，进行手势监控，减少抽象类间的耦合性。

   通过设置OnTouchListener监听，只要有手指触碰到绘制的图形，就会触发 onTouch 方法，同时通过判断event.getAction() 获取到手势的不同动作，来完成你所重写的响应事件。重写onTouch方法，通过 event.getAction() 获取到的值，自动判断执行哪一个 case 中的代码，即通过监测不同的动作来对图形作出相应处理。我们的处理主要就是移动和缩放，通过event.getX、event.getY方法所获取的手势点击的屏幕坐标，计算图形的平移及缩放值。

``` java
// 设置折线图的触摸事件，使扫描图保持同步平移和缩放
chartView.setOnTouchListener(new View.OnTouchListener() {
    @Override
    public boolean onTouch(View view, MotionEvent event) {
        switch (event.getAction()) {
            // 一根手指按下时
            case MotionEvent.ACTION_DOWN:
                if (!mPopupWindow.isShowing()) {      //大图未显示时
                    // 双击扫描图跳转到大图
                    if (event.getEventTime() - chartView.getUpTime() < 200) {
                        addToBigchartLayout(chartView);
                    }
                }
                chartView.setxDown(event.getX());
                chartView.setyDown(event.getY());
                break;
            // 手指抬起时
            case MotionEvent.ACTION_UP:
                chartView.setUpTime(event.getEventTime());
                break;
            // 手指移动时
            case MotionEvent.ACTION_MOVE:
                // 只有一根手指移动时
                if (event.getPointerCount() == 1 && event.getAction() != 261
                        && chartView.getxDown() != 0 && chartView.getyDown() != 0 ) {
                    // 实现图形平移
                    chartView.setxTranslate(chartView.getxTranslate()+(event.getX() - chartView.getxDown()) );
                    chartView.setyTranslate(chartView.getyTranslate()+(event.getY() - chartView.getyDown()) );
                    chartView.setxDown(event.getX());
                    chartView.setyDown(event.getY());
                }
                // 有两根手指移动时
                else if (event.getPointerCount() == 2) {
                    // 实现扫描图缩放
                    double xLenMove = Math.abs(event.getX(0) - event.getX(1));
                    double yLenMove = Math.abs(event.getY(0) - event.getY(1));
                    double lenMove = Math.sqrt(xLenMove * xLenMove + yLenMove * yLenMove);
                    // 动态更新
                    // 设置最小缩放比例为 0.4
                    if (chartView.getxScale() + (lenMove / chartView.getLenDown() - 1) > 0.4) {
                        chartView.setxScale((float) (chartView.getxScale() + (lenMove / chartView.getLenDown()  - 1)));
                        chartView.setyScale((float) (chartView.getyScale() + (lenMove / chartView.getLenDown()  - 1)));
                        chartView.setLenDown(lenMove);
                    }
                    chartView.setxDown(0);
                    chartView.setyDown(0);
                }
                break;
            // 有两根手指按下时
            case 261:
                double xLenDown = Math.abs(event.getX(0) - event.getX(1));
                double yLenDown = Math.abs(event.getY(0) - event.getY(1));
                chartView.setLenDown(Math.sqrt(xLenDown * xLenDown + yLenDown * yLenDown));
                break;
            // 两根手指中的一根抬起时
            case MotionEvent.ACTION_POINTER_UP:
                chartView.setxDown(0);
                chartView.setyDown(0);
                break;
            default:
                break;
        }
        chartView.postInvalidate();   //手势完成时重绘
        scanView.setxTranslate(chartView.getxTranslate());
        scanView.setxScale(chartView.getxScale());
        scanView.postInvalidate();
        // 只有当返回 false 时才会开启手势检测效果，否则折线图将无法移动和缩放
        return false;
    }
});
```
​	最后通过计算的平移值以及缩放值，重绘图形。

 - 完成以上步骤就可以在其它Activity中使用自己封装的对象来完成各种不同图形的绘制。如下是适配设备校准的绘图方法：
  <img src="http://hexoblog-1253306922.cosgz.myqcloud.com/photo2017/%E9%80%82%E9%85%8D%E8%AE%BE%E5%A4%87%E6%A0%A1%E5%87%86%E7%9A%84%E7%BB%98%E5%88%B6%E6%96%B9%E6%B3%95.png" width="50%" height="50%">

  项目源码可见我的Github仓库[漏磁检测系统][8]。


[1]: http://www.cnblogs.com/wangfeng520/p/5610540.html
[2]: http://www.jianshu.com/p/f69873371763
[3]: http://hexoblog-1253306922.cosgz.myqcloud.com/photo2017/%E6%8A%98%E7%BA%BF%E5%9B%BE%E5%8F%8A%E6%9B%B2%E7%BA%BF%E5%9B%BE%E5%AE%8C%E6%95%B4%E6%98%BE%E7%A4%BA%E6%95%88%E6%9E%9C.png
[4]: http://hexoblog-1253306922.cosgz.myqcloud.com/photo2017/%E5%9D%90%E6%A0%87%E5%8A%A8%E6%80%81%E9%80%82%E9%85%8D%E6%95%88%E6%9E%9C.png
[5]: http://hexoblog-1253306922.cosgz.myqcloud.com/photo2017/ChartService%E6%8A%BD%E8%B1%A1%E7%B1%BB.jpg
[6]: http://hexoblog-1253306922.cosgz.myqcloud.com/photo2017/%E5%A4%A7%E5%9B%BE%E6%98%BE%E7%A4%BA%E6%95%88%E6%9E%9C.png
[7]: http://hexoblog-1253306922.cosgz.myqcloud.com/photo2017/%E9%80%82%E9%85%8D%E8%AE%BE%E5%A4%87%E6%A0%A1%E5%87%86%E7%9A%84%E7%BB%98%E5%88%B6%E6%96%B9%E6%B3%95.png
[8]: https://github.com/Mindyu/LCJCSystem