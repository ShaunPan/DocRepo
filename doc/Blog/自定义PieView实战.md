# 自定义PieView实战

最近在复习自定义View所涉及的相关知识,说实话,自定义View中涉及了大量的类和方法,比如说Paint,canvas,path等一系列方法;还有很多概念和数学知识需要理解和复习,比如说贝塞尔曲线,三角函数公式等;复杂的View还会涉及事件分发等等一些列问题,这里就不一一列举了.难怪初学者抱怨自定义View困难,其实,新手从基础一点点入手,常常练习,总有一天会攻克这一方面的知识.这里推荐GcsSloop的自定义View系列博客[传送门](https://www.gcssloop.com/customview/CustomViewIndex "传送门"),相信一定会对你有所帮助.  
其他不多说,进入正题

### 最终效果图

[](https://github.com/ShaunPan/DocRepo/blob/master/img/PieView.PNG)

### 分析

PieView的具体实现主要是在onDraw方法中,分为以下三个步骤:

1. 绘制饼状图
2. 绘制延长线
3. 绘制文字和文字的下划线


以上三点点,饼状图的绘制比较简单,我们重点关注2,3.通过效果图我们发现,延长线的起始点坐标位于每一个扇形的中间弧度,延长了一定的距离,在终点处绘制文字和下划线.

### 绘制饼状图

这里使用Canvas的方法drawArc绘制圆弧,需要注意的是,创建变量累加起始角度,循环绘制弧形.

	for (int j = 0; j < mPieModels.size(); j++) {

	    //获取数据
	    PieModel pieModel = mPieModels.get(j);
	    float percentage = pieModel.getPercentage();
	    String name = pieModel.getName();
	
	    //绘制弧度
	    int i = j % colorArr.length;
	    anglePaint.setColor(colorArr[i]);
	    RectF rectF = new RectF(-r,-r,r,r);
	    canvas.drawArc(rectF,startAngle,percentage,true,anglePaint);
	    startAngle += percentage;//累加角度,作为下一次绘制的起始角度
	}

### 绘制饼状图的延伸线

由于是在每面扇形的中心位置,那么获取当前扇形的角度的一半,根据三角形函数公式算出该点的坐标,如图:

[](https://github.com/ShaunPan/DocRepo/blob/master/img/%E5%9D%90%E6%A0%87%E5%88%86%E6%9E%90.PNG)
图中线AB就是延长线部分,确定A点和B点的坐标就可以画出延长线,其实就是半径OA方向上的延长线,点A所在的位置是扇形弧度的中点,那么对应的角a即为当前扇形角度的一半,那么根据三角函数公式得:sin a = AC / OA ,cos a = OC / OA ==> AC = sin a * OA , OC = cos a * OA,OA即圆半径,那么A点的坐标就出来了.(梦回初中...)  

B的坐标同样的道理,已知角a,OB(r + AB),求BD,OD

	//获取新增角度的一半位置
    float halfAngle = startAngle - percentage / 2;
    Log.i(TAG, "halfAngle: "+halfAngle);

    //计算文字的起始坐标点
    float x = (float) (Math.cos(Math.toRadians(halfAngle)) * r);// Math.cos(double d)中的参数是弧度
    float y = (float) (Math.sin(Math.toRadians(halfAngle)) * r);

    //计算延长线的终点坐标
    float extendX = (float) (Math.cos(Math.toRadians(halfAngle)) * (r+extendLine));
    float extendY = (float) (Math.sin(Math.toRadians(halfAngle)) * (r+extendLine));

    //绘制半径的延长线
    canvas.drawLine(x,y,extendX,extendY,anglePaint);

> 这里有个坑还是提一下比较好,**`Math.cos(double d)`中的参数是弧度**,千万别传角度进去,角度通过`Math.toRadians(double angle)`转换为弧度

### 绘制文字和文字的下划线

延长线搞定,接下来就是文字和下划线的绘制了,这里需要注意的问题有两点:  

1. 屏幕中的坐标系和数学中的坐标系不同(手机中的坐标系:X轴右方为正,Y轴**下方**为正,数学中的坐标系:X轴右方为正,Y轴**上方**为正)  
2. 由于坐标系不同,象限也不同(如上图所示屏幕中的象限)

[此文章能助你很好的理解](https://www.gcssloop.com/customview/CoordinateSystem)  


	//测量文字的宽度
    float dis = anglePaint.measureText(name);

    //绘制文字和下划线
    if (halfAngle >=0 && halfAngle < 90){//第一象限
        canvas.drawLine(extendX,extendY,extendX + dis,extendY,anglePaint);
        canvas.drawText(name,extendX,extendY -lineDis,anglePaint);
    }else if (halfAngle >=90 && halfAngle <180){//第二象限
        canvas.drawLine(extendX,extendY,extendX - dis,extendY,anglePaint);
        canvas.drawText(name,extendX-dis,extendY -lineDis,anglePaint);
    }else if (halfAngle >=180 && halfAngle <270){//第三象限
        canvas.drawLine(extendX,extendY,extendX - dis,extendY,anglePaint);
        canvas.drawText(name,extendX-dis,extendY-lineDis,anglePaint);
    }else if (halfAngle >=270 && halfAngle < 360) {//第四象限
        canvas.drawLine(extendX,extendY,extendX + dis,extendY,anglePaint);
        canvas.drawText(name,extendX,extendY-lineDis,anglePaint);
    }

文字下划线是一条水平线,所以主要是X轴方向上的变化,Y轴不做改变,那么我们只要测量出文字的宽度即可画出与文字匹配的下划线了.所在象限不同,X轴Y轴的正负不同,数据处理方式也不同.上面的问题如果处理不当会影响文字和下划线的绘制方向,需要多多练习,转换数学和屏幕中的坐标系的概念.


### 提供接口

这没什么说的,直接上代码

	/**
     * 填充数据
     * @param dataList 数据集合
     */
    public void fillData(List<PieModel> dataList){
        this.mPieModels = dataList;
        invalidate();
    }

    /**
     * 设置颜色
     * @param colorArr 颜色数组
     */
    public void setColorArr(int[] colorArr){
        this.colorArr = colorArr;
    }

    /**
     * 设置延长线长度
     * @param lineDis 延长线长度
     */
    public void setExtendLine(float lineDis){
        this.extendLine = lineDis;
    }

    /**
     * 设置下划线与文字间的间距
     * @param lineDis 下划线与文字间的间距
     */
    public void setLineDis(float lineDis){
        this.lineDis = lineDis;
    }

    public void setTextSize(float textSize){
        anglePaint.setTextSize(textSize);
    }

### 总结

初级的自定义View,重拾起来,还是涉及很多问题的.  
1. canvas绘制方法的使用
2. 屏幕中坐标系与数学中坐标系的区别
3. 象限问题
4. padding问题(这个根据需要做处理,源码中处理了padding的问题)


### [源码链接](https://github.com/ShaunPan/ViewList/blob/master/app/src/main/java/com/pan/viewlist/view/PieView.java) 