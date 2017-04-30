---
layout: post
title:  "一步一步教你如何实现阿里芝麻信用分仪表盘效果"
subtitle:   ""
author: "MakerChen"
# header-img: ""
date:   2016-05-29 12:50:11
tags:
   - android
---

阿里芝麻信用分仪表盘效果

***

废话不多说，先看下效果：
![参考效果图](/media/ali/1.jpg)

该效果一眼看上去比较简单，但其涉及的知识点还是挺多的。尤其是需要读者对android.graphics包下的API有一定的了解。
先对涉及到的知识点罗列如下，还不是很了解的读者可以先自行百度做个简单的涉猎，对后续文章的理解会有很大帮助。

* Paint、Canvas这两个基础的类必须熟悉。
* 用作渲染的Shader类及其子类，以及后文中使用的是SweepGradient*梯度渲染*，用作渐变圆环，需要了解。
* canvas.save() & canvas.restore() 的作用与关系。
* 由Paint引申的PathEffect、PorterDuffXfermode，已经Matrix等类要有个基本的概念。
* 图层绘制的一些概念。
* 脏矩形技术。

* * *
如果你已经基本了解了上面涉及到的知识点。
OK。那接下来我们就一步一步实现这个效果。

### 1.环形渐变
或许大家都有印象，在ApiDemos中提供过一个例子*仿照PS做的取色器效果*。有兴趣的读者可以具体查看ApiDemos下的ColorPickerDialog的实现。这里我们参考他的写法，就可以做出一个简单的环形渐变了。
当然ColorPickerDialog中的核心代码也正是使用了刚才所提及的SweepGradient类用作渲染。该类属于Shader的子类，当然其兄弟类还有BitmapShader*位图渲染*、LinearGradient*线性渲染*、RadialGradient*环形渲染*、SweepGradient*梯度渲染*以及ComposeShader*组合渲染*。网上有一大堆关于他们的介绍,可以做出很多很棒的效果。此处不展开。

**核心代码如下：**

```
private Paint mPaint = new Paint(Paint.ANTI_ALIAS_FLAG);// 渐变色环画笔，抗锯齿
private final int[] mColors = new int[] { 0xffff0000, 0xffffff00, 0xff00ff00,
0xff00ffff,0xff0000ff,0xffff00ff };// 渐变色环颜色

Shader s = new SweepGradient(0, 0, mColors, null);
mPaint.setShader(s);
mPaint.setStyle(Paint.Style.STROKE);
mPaint.setStrokeWidth(40);

float r = CENTER_X - mPaint.getStrokeWidth() * 0.5f;
canvas.save() ;
canvas.translate(CENTER_X, CENTER_X);// 移动中心
canvas.rotate(150);
canvas.drawOval(new RectF(-r, -r, r, r), mPaint);// 画出色环和中心园
canvas.restore();

```

 **效果如图1所示：**
![图1](/media/ali/2.jpg)

**代码讲解：**
从**参考效果图**上看，颜色是有红色**渐变(并非线性渐变，这里我们先按照简单的实现)**为绿色，而且效果并非为一个整圆。为了计算方便，我们假设该圆环的角度为240度。
如图2所示
![图2](/media/ali/3.png)
我们已知SweepGradient是一个360度均匀分布的渐变,我们一共设置了6个渐变色：从红色（ff0000）到紫色（ff00ff）,使其均匀分布在圆环上。
而绘制圆的时候，我们先将canvas的原点(*在android2D图形系统中其坐标系原点在视图左上角*)通过<strong>canvas.translate()</strong>平移至了圆环的中心点。在此我们使用<strong>canvas.rotate()</strong>旋转操作，旋转150度，使其红色渐变的开始位置处于图片左下方（*此处正确的理解应该是这样：由于我们对画布旋转了150度，所以我们在绘制完圆环之后，通过restore()方法又使得画布回归到原来位置，从而达到了将红色渐变位于左下方的目的*）。调整完canvas之后，我们通过<strong>canvas.drawOval()</strong>将圆绘制上去。最后将画布回归到原来的位置。
此处还使用了<strong>canvas.save()</strong>与<strong>canvas.restore()</strong>组合操作。简单介绍一下：由于此处我们对画布有平移、旋转操作。为了不造成对后续绘制的影响，使其复杂度增加。我们使用save()和restore()的组合来使得画布回归到它原来的位置。此举有时候会对性能产生一定的影响，本文只是step by step的实现教程，而且此效果并不会强依赖于性能，所以性能在此处先放一边。文末我会注明可以优化的点，供大家思考、讨论。
在这里调用完restore()的表象就是canvas的原点又回到了视图的左上角。关于具体对<strong>canvas.save()</strong>和<strong>canvas.restore()</strong>的解释，网上有一大堆。这里不详细展开。大致可以理解为save()为保存当前canvas状态，restore则为恢复上一次save()的状态。

### 2.绘制内圆
**核心代码如下**

```
paintMiddleCircle.setColor(Color.GRAY);
paintInnerCircle.setColor(Color.GRAY);
paintMiddleCircle.setStrokeWidth(4);
paintInnerCircle.setStrokeWidth(4);
paintMiddleCircle.setStyle(Paint.Style.STROKE);
paintInnerCircle.setStyle(Paint.Style.STROKE);
PathEffect effects = new DashPathEffect(new float[]{5,5,5,5},1);
paintInnerCircle.setPathEffect(effects);

canvas.save() ;
canvas.translate(CENTER_X, CENTER_X);
canvas.drawCircle(0, 0, CENTER_X * 5 / 8, paintInnerCircle);
canvas.drawCircle(0, 0, CENTER_X * 3 / 4, paintMiddleCircle);
canvas.restore();
```

**效果如图3所示**

![图3](/media/ali/4.jpg)

**代码讲解：**
该功能比较简单。
在此处需要了解PathEffect及其子类的作用，这里我们使用DashPathEffect绘制虚线。
细心的读者还可以发现，我们使用的绘制圆形的方法不一样。前面使用的是drawOval绘制椭圆，而在此处使用的是drawCircle直接画圆，效果都一样。具体区别可以自己体会，一个是框死了画内切椭圆，另一个是直接画圆。

### 3.绘制辅助线
**核心代码如下**

```
paintGap1.setColor(Color.WHITE);
paintGap2.setColor(Color.WHITE);
paintGap1.setStrokeWidth(2);
paintGap2.setStrokeWidth(4);

int a = (int) (2 * CENTER_X - mPaint.getStrokeWidth());
for ( int i=0;i<=60; i++) {    
    canvas.save() ;    
    canvas.rotate(-(-30 + 4 * i), CENTER_X, CENTER_X);    
    if ( i % 10 == 0 ) {        
        canvas.drawLine( a ,CENTER_X, 2 * CENTER_X, CENTER_X, paintGap2);    
    } else {       
        canvas.drawLine( a ,CENTER_X, 2 * CENTER_X, CENTER_X, paintGap1);    
    }    
    canvas.restore();
}
```

**效果如图4所示**

![图4](/media/ali/5.jpg)

**代码讲解**
在上面，我们曾假设了圆弧的角度为240度。便于计算，我们将该圆弧划分为6个区，每个区占40度,每个区有10个小间隔，每个小间隔的角度就是4度。由于圆弧有30度是在水平线以下的，所以我们的循环规则是上述代码。<strong> canvas.rotate(-(-30 + 4 * i), CENTER_X, CENTER_X);   </strong>此处由于<strong>CENTER_X==CENTER_Y==r</strong>,将上述代码修改为<strong>canvas.rotate(-(-30 + 4 * i), CENTER_X, CENTER_Y); </strong>或许更容易理解。rotate中参数>0为顺时针旋转，<0为逆时针旋转。

 ### 4.圆环变圆弧
到目前为止，我们画的还只是个渐变圆环，与效果圆弧还有些不同。下面我们将圆环处理为圆弧。
** 核心代码如下 **

```
width = MeasureSpec.getSize(widthMeasureSpec);
height =  (int) ( ( Math.tan(Math.PI / 6) + 1 ) * width / 2 ) ;

Path path = new Path();
path.moveTo(CENTER_X, CENTER_X);
path.lineTo(0, height);
path.lineTo(width, height);
path.lineTo(CENTER_X, CENTER_X);
path.close();
canvas.drawPath(path, paintBg);

```

**效果图5如下**
![图5](/media/ali/6.jpg)

** 代码讲解：**
首先我们需要调整视图的高度。在这之前我们都是令<strong>width==height</strong>，保证绘制出一个整圆。现在根据我们的假设圆弧度数240度，其在水平线以下为30度，即PI/6。由数学公式计算得知，其视图高度为 <strong>height = r * tan(PI/6) + r</strong>。
这还不够，调整完视图的高度，我们需要将一些杂线，从视图中除去，让其看上去更像是个圆弧。
如图6所示未去杂线的时候

![图6](/media/ali/7.jpg)

我们利用图层互相遮罩的原理。以圆心和视图的两个顶点，连接成一个三角形，可以达到掩盖其与杂线的目的。也就是后面代码的作用。
记住在onDraw时候的一个原则:先画的在画布下方，后画的在画布上方，后画的会覆盖先画的。从而达到图5的效果。

### 5.文字的绘制
** 核心代码如下**

```
private static final String[] text = {"950","极好","700","优秀","650","良好","600","中等","550","较差","350","很差","150"};

for ( int i=0;i<=12;i++) {   
     canvas.save();    
     canvas.rotate(-(-120 + 20 * i ), CENTER_X, CENTER_X);    
     canvas.drawText(text[i],CENTER_X - 20 ,CENTER_X * 3 / 16,paintText);   
     canvas.restore();
}
```

**效果图7如下**
![图7](/media/ali/8.jpg)

** 代码讲解 **
我们已知每个区为40度。从参考效果图上可以看出每隔20度就会有一段文字。我们知道在绘制文字的时候，都是从左往右写的。所以我们在旋转画布的时候，起始点需要在原来的基础上再加90度，即逆时针旋转120度，然后绘入文字。当然这段绘制的过程需要在绘制三角形之后，否则部分文字会被三角形的遮罩遮盖起来。

### 6.最后的动效

```
if ( isSetReferValue ) {    
    float r1 = CENTER_X * 6 / 8 ;    
    canvas.save();    
    canvas.translate(CENTER_X, CENTER_X);    
    canvas.drawArc(new RectF(-r1, -r1, r1, r1), -210, currentRotateAngle, false, paintMiddleArc);    
    canvas.rotate( - 30 + currentRotateAngle );    
    Matrix matrix = new Matrix();    
    matrix.preTranslate(-r1 - bitmapWidth * 3/ 8,-bitmapHeight/2);    
    canvas.drawBitmap(bitmapLocation,matrix,paintBitmap);    
    canvas.restore();
}

public void setReferValue ( int referValue ,final RotateListener rotateListener) {    
    isSetReferValue = !isSetReferValue ;    
    if ( referValue <= 150 ) {        
        totalRotateAngle = 0f ;    
    } else if ( referValue <= 550 ) {        
        totalRotateAngle = ( referValue - 150 ) * 80 / 400f ;    
    } else if ( referValue <= 700 ) {        
        totalRotateAngle = ( referValue - 550 ) * 120 / 150f + 80 ;    
    } else if ( referValue <= 950 ) {        
        totalRotateAngle = ( referValue - 700 ) * 40 / 250f + 200;      
    } else {        
        totalRotateAngle = 210f ;    
    }    
    rotateAngle = totalRotateAngle / 60 ;    
    new Thread(new Runnable() {        
        @Override        
        public void run() {            
             boolean rotating = true ;            
             float value = 350;           
             while (rotating) {                
                  try {                    
                        Thread.sleep(16);                
                  } catch (InterruptedException e) {                    
                        e.printStackTrace();                
                  }                
                  currentRotateAngle += rotateAngle;                
                  if ( currentRotateAngle >= totalRotateAngle ) {
                      currentRotateAngle = totalRotateAngle;                          
                      rotating = false;                
                  }                
                  if ( null != rotateListener) {                    
                      if ( currentRotateAngle <= 80 ) {                        
                            value = 350 + ( currentRotateAngle / 80 ) * 400 ;                    
                      } else if ( currentRotateAngle <= 200 ) {                        
                            value = 550 + ( ( currentRotateAngle  - 80 )/ 120 ) * 150 ;                   
                      } else {                        
                            value = 700 + ( ( currentRotateAngle - 200 ) / 40 ) * 250 ;                    
                      }                    
                      rotateListener.rotate(currentRotateAngle,value);                                    
                  }                
                  postInvalidate();            
              }        
          }    
      }).start();
 }
```

**效果图8如下**

![图8](/media/ali/9.jpg)

**代码讲解**
绘制的代码中。首先我们要了解到绘制圆弧的方法为<strong>canvas.drawArc()</strong>,此处我们要从左下角开始绘制圆弧，所以我们的起始旋转角度为-210度。
由于我们此处的原点在圆心。图片要跟随着已知的旋转角度进行旋转。我们知道针对<strong>canvas.rotate()</strong>方法，当旋转角度>0的时候，是顺时针旋转；<0为逆时针旋转。由于此处我们图片的箭头朝向向右，为了保证图片的朝向指向圆心。我们旋转的规则为<strong>- 30 + currentRotateAngle</strong>,保证每一次在绘制图形的时候，都是在（x,y）为（-r1 - bitmapWidth * 3/ 8,-bitmapHeight/2）这个位置的时候绘制。最后恢复canvas。
关于在计算<strong>totalRotateAngle 、currentRotateAngle以及 value </strong>的时候，都是些简单的算法。夹杂着很多硬编码，耐心点应该可以读懂，不做过多解释。

* * *
实现的七七八八，大致思路应该是这样。

### 一些问题
1. 在上文也提到了，参考的效果图，并非是一个平滑的渐变。仔细观察的话，在600处有处瞬断的迹象。
解决思路：利用上面讲到过的PorterDuffXfermode，将两段不同的环形渐变，拼接而成。到达此效果。
2. 关于优化
  * onDraw()方法中，canvas.save()与canvas.restore()方法多次使用，造成不必要的性能浪费。
  * 在执行箭头转动效果的时候，不需要在canvas上每次全部都重新绘制。只需要绘制需要绘制的部分区域即可，即脏矩形。在这里也就是箭头所滚动范围内的部分区域圆环。读者可以自行实现。
3. 关于多线程
细心的人可以发现方法<strong>setReferValue()</strong>,并没有考虑多线程的情况。此处只是demo，场景也有限。没做特殊处理。有兴趣的读者可以自行实现。

### 后记
之前一直没有记录博客的习惯。现在写完两篇，发现将代码翻译成文不是一件容易的事。代码在周三就基本完成了，文章也是一直拖着到现在才整理出来发布。要将每一个知识点，能够简单的表述出来，是比较难的一件事情。落笔成文同面对面与人讲述，会不太一样。以后要多加强这方面的练习。也希望读者们能够一起来尝试记录。遗留的问题，都不是很难，读者可以自行尝试的去实现。今天脑子有点疼，就写到此了。

源代码在此下载：http://pan.baidu.com/s/1kTKUowJ

enjoy it！
