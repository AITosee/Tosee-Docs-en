.. _chapter_vs1_vision_index:

Vision
================

简介
----------------
Sentry1视觉传感器集成有多种离线视觉算法，无需网络即可识别物体。

视觉基础
----------------

* 图像检测
    判断画面中是否有某一类目标物体，而不区分这个物体具体内容是什么。

* 图像识别
    不仅要判断画面中是否有某一类目标物体，还要对这个物体具体的内容进行识别。

* 分类标签
    对于具有物体识别功能的算法，需要用一个数字来标记不同的物体，这个数字即为分类标签：Label。
    
    对于不同的算法，即便具有相同的数字，其所代表的含义也是不同的，比如在颜色识别算法中用数字1代表*黑色*，而在卡片算法中用数字1代表*前进*。

* 像素
    每一幅画面都是由各种颜色的”点“按一定的规则排列而成的，这些点称之为像素。颜色可以通过三原色来表示，对于图像而言，其三原色为红色Red，绿色Green，蓝色Blue。

    每个像素的颜色就是由不同深浅的R、G、B三种颜色组成的。每个通道颜色的深浅可以称之为该通道的亮度，可以由一个字节的数据来表示，其范围为0～255,其中0为最暗，255为最亮。
    
    当RGB三个通道都为0时最暗，表现出来的就是黑色，反之都为255时最亮，表现出来就是白色。

* 图像坐标系
    Sentry1采用百分比坐标系，将水平方向和垂直方向量化至0～100的范围内，即100等分，返回坐标是相对满量程的值。百分比坐标系的图像中点为（50,50），即水平方向50%的位置和垂直方向50%的位置。
    
    **注意**：百分比坐标系下想表示一个正方形，其宽w和高h是不相等的，而是符合3：4的关系。比如，如果正方形的宽w为12%,那么其对应的高度h应该为12/3×4=16%

* 目标物体标记方法
    当检测到物体后，需要按照某种方式来标记目标物体，通常采用矩形框标记法和顶点标记法。
    
    **矩形框标记法**:
        该方法会提供四个参数，分别为目标物体的中心点水平坐标x，中心点垂直坐标y，物体的水平宽度w，物体的垂直高度h。
        通过这4个参数就可以将物体在画面中的方位和大小标记出来。大部分算法都采用此类标记法。

    **顶点标记法**：
        该方法会提供1个或多个端点的x-y坐标，用于标记一个或多个端点。
        比如在线条检测算法中，每组处理结果的2个端点可以确定一条直线，可以推算出其方向和大小。


算法介绍
----------------

算法列表
************************

================    ================================================    ================    ================
算法ID               名称                                                 英文名称             简介
================    ================================================    ================    ================
1                    :ref:`颜色识别<chapter_vs1_vision_color_index>`          Color                  可设置识别区域，返回区域中的颜色信息，如R，G，B值及分类标签
2                    :ref:`色块检测<chapter_vs1_vision_blob_index>`           Blob                   检测图像中是否有指定的色块，支持黑、白、红、绿、蓝、黄6种色块检测
3                    :ref:`球体识别<chapter_vs1_vision_ball_index>`           Ball                   可以检测识别橙色乒乓球或者绿色的网球
4                    :ref:`线条检测<chapter_vs1_vision_line_index>`           Line                   检测图像中的线条，返回两个端点坐标及倾斜角度
6                    :ref:`卡片识别<chapter_vs1_vision_card_index>`           Card                   识别特制的5张交通卡片图案，包含前进，左转、右转、调头和停车
7                    :ref:`人体检测<chapter_vs1_vision_body_index>`           Body                   可以检测到人体
================    ================================================    ================    ================

算法详解
************************

.. _chapter_vs1_vision_color_index:

ID:1 颜色识别-Color
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* 算法简介

    用户指定一个识别区域，其位置和大小由用户进行设置，返回该区域的颜色标签信息和实际的红R、绿G、蓝B数值。

* 颜色分类标签

    定义了7种颜色分类标签：

    ================    ================    ================    ================    ================    ================
    分类标签              英文标识             中文含义              分类标签             英文标识             中文含义
    ================    ================    ================    ================    ================    ================
    1                    Black               黑色                2                    White              白色
    3                    Red                 红色                4                    Green              绿色                
    5                    Blue                蓝色                6                    Yellow             黄色
    0                    Unknown             未知
    ================    ================    ================    ================    ================    ================

    **注意**：由于紫色、青色（蓝绿色）、橙色、灰色等，相对来说容易造成误报，因此这几个颜色部分区间被划分为临近颜色的标签，部分被划分为未知颜色，如果用户确实有这几种颜色的使用需求，可以通过返回参数的R、G、B实际值自行计算与判断

* 配置参数

    用户需要指定识别区域的坐标和大小，如果没有指定，则默认为图像中心点

    当通过主控设置寄存器参数时，每个识别区域都需要设置以下参数：

    ================    ================================
    参数                 含义
    ================    ================================
    1                   识别区域中心x坐标
    2                   识别区域中心y坐标
    3                   识别区域宽度w
    4                   识别区域高度h
    5                   无
    ================    ================================

* 返回结果

    当通过主控读取寄存器时，将会返回以下的数据：

    ================    ================================
    结果                 含义
    ================    ================================
    1                   R，红色值，范围 0～255
    2                   G，绿色值，范围 0～255
    3                   B，蓝色值，范围 0～255
    4                   无
    5                   颜色分类标签
    ================    ================================

* 使用技巧

    1. 由于是对像素进行统计处理，当识别区域较多且较大时，处理速度会相应的变慢，反之则会比较快速。
    2. 当识别区域窗口较小时（比如2x2），可以识别较小的色块，处理速度快，但统计样本太少，容易被干扰，可信度较低，适合于背景单一可控的环境。
    3. 当识别区域窗口较大时（比如32x32），统计样本多，即便出现若干的杂色也会被滤除，具有较高的可信度，但处理速度会变慢，当识别区域处于2种颜色的边界时，颜色可能会经常跳变。
    4. 当画面存在偏色时，需要锁定白平衡功能


.. _chapter_vs1_vision_blob_index:

ID:2 色块检测-Blob
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* 算法简介

    用户指定检测某个颜色，判断图像中是否有该颜色的色块，返回其坐标和大小，颜色分类标签与颜色识别中的定义相同。

* 配置参数

    用户需要指定待检测的颜色标签，用户还可以通过设置色块的最小宽度w和高度h来过滤那些小于该值的色块，以减少误报。

    当通过主控设置寄存器时，有以下参数需要设置：

    ================    ================================
    参数                 含义
    ================    ================================
    1                   无
    2                   无
    3                   有效色块最小宽度w
    4                   有效色块最小高度h
    5                   待检测的颜色分类标签（注意：该值是与设置参数一致的）
    ================    ================================


* 返回结果

    当通过主控读取寄存器时，将会返回以下的数据：
    
    ================    ================================
    结果                 含义
    ================    ================================
    1                   色块中心x坐标
    2                   色块中心y坐标
    3                   色块宽度w
    4                   色块高度h
    5                   颜色分类标签
    ================    ================================

* 使用技巧

    1. 当确定需要跟踪一个物体时，比如检测白色的道路或是跟踪小球，可以将色块数量设置为1，可以提高速度，减少误报
    2. 采用较小的识别区域并使用准确性能模式，可以看到更远处的物体
    3. 识别大面积的色块时，运行帧率会明显下降，此时可以用灵敏模式
    4. 当画面存在偏色时，需要锁定白平衡功能

.. _chapter_vs1_vision_ball_index:

ID:3 球体识别-Ball
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* 算法简介

    判断图像中是否有球体，只支持橙色小球（比如乒乓球）或绿色小球（比如网球），当然，近似颜色的球体都可以被检测到

* 配置参数

    无

* 返回结果

    识别到球体后会返回其坐标、大小和标签编号

    ================    ================    ================    ================    ================    ================
    分类标签              英文标识              中文含义             分类标签             英文标识              中文含义
    ================    ================    ================    ================    ================    ================
    1                    Pingpong            乒乓球（橙色）         2                   Tennis              网球（绿色）
    ================    ================    ================    ================    ================    ================

    当通过主控读取寄存器时，将会返回以下的数据：

    ================    ================================
    结果                 含义
    ================    ================================
    1                   球体中心x坐标
    2                   球体中心y坐标
    3                   球体宽度w
    4                   球体高度h
    5                   球体分类标签
    ================    ================================


.. _chapter_vs1_vision_line_index:

ID:4 线条检测-Line
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* 算法简介

    检测图像中是否有线条，如果有则会返回线条的两个端点和倾斜角度，如果为曲线，则会返回近似的直线段
    
* 配置参数

    无

* 返回结果

    检测到线条后会返回其两个端点和倾斜角度

    **注意**：水平向右为0度，逆时针增大，垂直向上为90度，水平向左为180度，一般不会向下检测输出角度

    当通过主控读取寄存器时，将会返回以下的数据：

    ================    ================================
    结果                 含义
    ================    ================================
    1                   线段终点x坐标（高处）
    2                   线段终点y坐标（高处）
    3                   线段起点x坐标（低处）
    4                   线段起点y坐标（低处）
    5                   线段的倾斜角度
    ================    ================================

* 使用技巧

    1. 背景与线条应清晰分明，比如白底黑线，如果背景杂乱，则可能会检测出背景中的线条
    2. 线条粗细应适中，不可过细，也不可太宽
    3. 一般来说，巡线时，第一条线段始终为屏幕下方先发现的线段，然后是分支线段


.. _chapter_vs1_vision_card_index:

ID:6 卡片识别-Card
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* 算法简介

    识别图像中是否有指定的交通卡片图案，返回其卡片坐标、大小、分类标签等信息。分类标签见下表

    **交通标志**

    ================    ================    ================    ================    ================    ================
    分类标签              英文标识              中文含义             分类标签             英文标识              中文含义
    ================    ================    ================    ================    ================    ================
    1                    Forward             前进                2                   Left                左转
    3                    Right               右转                4                   Turn Around         掉头
    5                    Park                停车                
    ================    ================    ================    ================    ================    ================

* 配置参数

    无

* 返回结果

    该算法只支持单张卡片识别，卡片在20度以内的旋转仍然可以识别，角度旋转过大则无法识别

    当通过主控读取寄存器时，将会返回以下的数据：

    ================    ================================
    结果                 含义
    ================    ================================
    1                   卡片中心x坐标
    2                   卡片中心y坐标
    3                   卡片宽度w
    4                   卡片高度h
    5                   卡片分类标签
    ================    ================================


.. _chapter_vs1_vision_body_index:

ID:7 人体检测-Body
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* 算法简介

    检测图像中是否含有人体上半身特征，上半身只包含头部、肩膀所在的人体区域。正面效果最佳，侧面及背面识别率会降低。

* 配置参数

    无

* 返回结果

    当通过主控读取寄存器时，将会返回以下的数据：

    ================    ================================
    结果                 含义
    ================    ================================
    1                   人体中心x坐标
    2                   人体中心y坐标
    3                   人体宽度w
    4                   人体高度h
    5                   无
    ================    ================================


//end