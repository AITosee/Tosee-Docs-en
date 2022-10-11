.. _chapter_vision_index:

Vision
================

Brief
----------------
The Sentry2 vision sensor integrates a variety of offline vision algorithms to recognize objects without network, and the on-board ESP8285-WiFi chip can realize the cloud-based image recognition function.

Visual Basic
----------------

* Image Detection
    判断画面中是否有某一类目标物体，而不区分这个物体具体内容是什么。比如检测人脸，只需要判断画面中有人脸即可，而无需知道这个人是谁。
    Determine whether there is a certain type of object in the picture, without distinguishing what the object is. Face detection, for example, only needs to determine that there is a face in the picture, without knowing who the person is.

* 图像识别
    不仅要判断画面中是否有某一类目标物体，还要对这个物体具体的内容进行识别。比如在检测到人脸之后，还需要判断出这个人脸是谁。

* 分类标签
    对于具有物体识别功能的算法，需要用一个数字来标记不同的物体，这个数字即为分类标签：Label。
    
    对于不同的算法，即便具有相同的数字，其所代表的含义也是不同的，比如在颜色识别算法中用数字1代表*黑色*，而在20分类算法中用数字1代表*飞机*。

* 像素
    每一幅画面都是由各种颜色的”点“按一定的规则排列而成的，这些点称之为像素。颜色可以通过三原色来表示，对于图像而言，其三原色为红色Red，绿色Green，蓝色Blue。

    每个像素的颜色就是由不同深浅的R、G、B三种颜色组成的。每个通道颜色的深浅可以称之为该通道的亮度，可以由一个字节的数据来表示，其范围为0～255,其中0为最暗，255为最亮。
    
    当RGB三个通道都为0时最暗，表现出来的就是黑色，反之都为255时最亮，表现出来就是白色。

* 图像坐标系
    图像默认的分辨率为320×240像素，规定图像的左上角为坐标原点（0,0），水平方向为X轴，自左向右数值从0递增至319，总像素为320个，垂直方向为Y轴，自上向下数值从0递增至239，总像素为240个。
    图像中的每个像素位置可以由一组x-y坐标表示，比如（160,120）为图像中心点位置。如果一个物体的中心点坐标为（50,120），表明这个物体位于图像画面中偏左的方位，
    再比如，一个物体的坐标为（200,50），那么这个物体位于右上方的位置。

    Sentry2还提供了百分比坐标系，将水平方向和垂直方向量化至0～100的范围内，即100等分，返回坐标是相对满量程的值。百分比坐标系的图像中点为（50,50），即水平方向50%的位置和垂直方向50%的位置。
    
    **注意**：百分比坐标系下想表示一个正方形，其宽w和高h是不相等的，而是符合3：4的关系。比如，如果正方形的宽w为12%,那么其对应的高度h应该为12/3×4=16%

* 目标物体标记方法
    当检测到物体后，需要按照某种方式来标记目标物体，通常采用矩形框标记法和顶点标记法。
    
    **矩形框标记法**:
        该方法会提供四个参数，分别为目标物体的中心点水平坐标x，中心点垂直坐标y，物体的水平宽度w，物体的垂直高度h。
        通过这4个参数就可以将物体在画面中的方位和大小标记出来。大部分算法都采用此类标记法。

    **顶点标记法**：
        该方法会提供1个或多个端点的x-y坐标，用于标记一个或多个端点。
        比如在线条检测算法中，每组处理结果的2个端点可以确定一条直线，可以推算出其方向和大小。

* KPU类算法与CPU类算法
    Sentry2中的图像算法分为两种类型，一种是基于神经网络硬件加速器KPU的，另一种是只通过CPU执行运算的。
    
    **KPU算法**：
        该类算法在运行前需要先从Flash中加载算法模型文件，然后经KPU进行加速运算处理后，再由CPU做后续处理。
        由于芯片KPU单元硬件设计架构的原因，同一时间内KPU加速器只能运行一个模型文件，又由于内存资源有限，一般情况下无法同时加载多个模型文件。
        所以视觉传感器对于KPU类算法无法同时开启，但可以与CPU类算法同时开启，如果当前已经在运行KPU算法，那么新指定的KPU算法不会被开启，除非旧的KPU算法被关闭掉。

    **CPU算法**：
        该类算法由CPU指令运算即可实现，无需KPU加速器，也无需加载模型文件，此类算法可以与KPU共同执行，可以同时开启与运算。

* 数码变焦
    Sentry2具有数码变焦功能，可以实现对图像视野的缩放，用于看清远处的物体。数码变焦用Zoom来表示，提供了1～5档的变焦选项（不同型号的摄像头变焦范围可能会有差异）。
    Zoom1为原始视野，视野内的前后距离比较近，但视野宽度广，对于快速移动的物体可以进行很好的跟踪。
    随着Zoom值的增大，可以看清更远处的物体，但视野范围会缩窄，如果物体移动比较快，则容易跑到视野外面。


Vision Introduction
----------------

Vision List
************************

================    ================================================    ====================
Vision ID            Name                                                Brief                                                                                                                           
================    ================================================    ====================
1                    :ref:`Color<chapter_vision_color_index>`            Return the R(red),G(green),B(blue) value and its label of each region. Up to 25 regions
2                    :ref:`Blob<chapter_vision_blob_index>`              Detect a specified color block. It supports black, white, red, green, blue and yellow color blocks setection at the same time
3                    :ref:`Apriltag<chapter_vision_apriltag_index>`      Support 16H5, 25H9, 36H11 Apriltag family. Up to 25 tags
4                    :ref:`Line<chapter_vision_line_index>`              Find lines and return its endpoints and degrees, support 1-5 lines
5                    :ref:`Learning<chapter_vision_learning_index>`      Training objects and categorize them. Up to 25 model data
6                    :ref:`Card<chapter_vision_card_index>`              Identify special card patterns, including 10 traffic cards, 9 shape cards, and 10 number cards
7                    :ref:`Face<chapter_vision_face_index>`              Face detection and recognition, support mask detection, can store 25 model data
8                    :ref:`20Class<chapter_vision_20class_index>`        Classify 20 common objects, such as cat, car, human etc
9                    :ref:`QrCode<chapter_vision_qrcode_index>`          Recognition a simple QR code
10                   :ref:`Custom<chapter_vision_custom_index>`          Running custom algorithms which is running in the ESP8285-WiFi chip on board
11                   :ref:`Motion<chapter_vision_motion_index>`          Determine if there are moving areas in the image
================    ================================================    ====================

*Note: Multiple visions without asterisks can be enabled at the same time. But the visions with asterisks can not running with other asterisks vision.  
When multiple algorithms are enabled, the speed will be slowed down*

Detailed Introduction
************************

.. _chapter_vision_color_index:

ID:1 Color
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Brief
    .. image:: images/sentry2_vision_color_selecting.png

    User can set one or up to 25 regions for color recognition and return the R(red),G(green),B(blue) value and its label of each region. 
    The coordinate and size of each region can be configured.

* Color Label
    A color label is a number use to represent a color:

    .. image:: images/sentry2_vision_label.png

    ================    ================    ================    ================ 
    Label                Name                Label              Name 
    ================    ================    ================    ================
    1                    Black               2                   White 
    3                    Red                 4                   Green               
    5                    Blue                6                   Yellow
    0                    Unknown            
    ================    ================    ================    ================

* Parameters

    User can set regions for recognition:

    ================    ================================
    Param               Brief
    ================    ================================
    1                   X-coordinate of the region center
    2                   Y-coordinate of the region center
    3                   Width of the region
    4                   Height of the region
    5                   None
    ================    ================================

    .. image:: images/sentry2_vision_color_setting.png

    We provide several preset parameters in the UI setting page:

    Grid(X x Y): 1x1、2x2、3x3、4x4、5x5、1x10、2x10、6x1、6x2

    Size(W x H): 2x2、4x4、8x8、16x16、32x32

    **NOTE**：To represent a square in the percentage coordinate system, the width and height are not equal, but conform to the 3:4 relationship. 
    For example, if the width of a square is 12%, then its height h should be 12/3×4=16%.
    In the absolute coordinate system, the preset recognition area size are : 1x1, 2x3, 3x4, 6x8, 9x12

* Results

    .. image:: images/sentry2_vision_color_running.png

    There will be a rectangular box on the screen that identifies the color, and a 4-corner box identifies the unknown color

    ================    ================================
    Result              Brief
    ================    ================================
    1                   R, red channel value, range 0～255
    2                   G, green channel value, range 0～255
    3                   B, blue channel value, range 0～255
    4                   None
    5                   Color label
    ================    ================================

* Tips

    1. The process speed will be slow down if the size of region is too large
    2. A smaller region (such as 2x2), faster but be easily disturbed 
    3. A larger region (such as 32x32), slower but higher credibility 
    4. Suggest to lock the white balance if color is abnormal

.. _chapter_vision_blob_index:

ID:2 Blob
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Brief

    .. image:: images/sentry2_vision_blob_selecting.png

    Find a certain color block from an image and return its coordinate and size. It support mulit-color and multi-result detection. 
    The color label has the same definition of Color vision 

* Parameters

    User need to decide which color to be detected firstly. 
    The width and height of the minimum color piece can be configured to reduce the false results:

    ================    ================================
    Param               Brief
    ================    ================================
    1                   None
    2                   None
    3                   Minimum width
    4                   Minimum height
    5                   The label of Color to be detected
    ================    ================================

    .. image:: images/sentry2_vision_blob_setting.png

    We provide several preset parameters in the UI setting page:
        Algorithm Performance Level:
            To select the performance of the vision according to different application requirements:
            "Sensitive", "Balance", and "Accurate".
            
        Maximum Number of Blocks:
            Support 1~5 blocks for each color

        Minimum Size of Block:
            Absolute Coordinate System: 2x2, 4x4, 8x8, 16x16, 32x32, 64x64, 128x128 pixel

            Percentage Coordinate System:1x1, 2x3, 3x4, 6x8, 9x12, 21x28, 42x56 %

        Color to be Detected：
            An open eye icon is displayed if the color label is actived

* Results

    .. image:: images/sentry2_vision_blob_running.png

    Get the results :
    
    ================    ================================
    Result              Brief
    ================    ================================
    1                   X-coordinate of the block center
    2                   Y-coordinate of the block center
    3                   Width of the block
    4                   Height of the block
    5                   Color label
    ================    ================================

* Tips

    1. 当确定需要跟踪一个物体时，比如检测白色的道路或是跟踪小球，可以将色块数量设置为1，可以提高速度，减少误报
    2. 采用较小的识别区域并使用准确性能模式，可以看到更远处的物体
    3. 识别大面积的色块时，运行帧率会明显下降，此时可以用灵敏模式
    4. 当画面存在偏色时，需要锁定白平衡功能

.. _chapter_vision_apriltag_index:

ID:3 Apriltag
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Brief

    .. image:: images/sentry2_vision_apriltag_selecting.png

    Find apriltags from an image, support 16H5，25H9，36H11 encoding family and up to 25 results. 
    You need to decide which encoding family to use before this vision enabled, and only one family can be process

    **NOTE**: This vision cannot run at the same time as other vision marked with asterisks

    **Label**

    .. image:: images/sentry2_vision_apriltag_family.png

    Apriltag is a set of defined black and white squares. 
    Different codes use different numbers of squares. 
    Each pattern has a predefined label.

    `Apriltag image download <https://github.com/AprilRobotics/apriltag-imgs/tree/master>`

* Parameters

    .. image:: images/sentry2_vision_apriltag_setting.png

    We provide several preset parameters in the UI setting page:
        Algorithm Performance Level:
            To select the performance of the vision according to different application requirements:
            "Sensitive", "Balance", and "Accurate".

        Encoding Family:
            Support “16H5”，“25H9”，“36H11”


* Results
    .. image:: images/sentry2_vision_apriltag_running.png

    Get the results :

    ================    ================================
    Result              Brief
    ================    ================================
    1                   X-coordinate of the tag center
    2                   Y-coordinate of the tag center
    3                   Width of the tag
    4                   Height of the tag
    5                   Label
    ================    ================================

* Tips

    1. 所识别到的标签宽度和高度具有较稳定的输出，可以利用这一点进行距离判断，标签旋转后不会改变其大小，但倾斜时可能会有影响
    2. 当需要识别多个标签时，可以关闭坐标线的显示，看起来比较简洁
    3. 标签越大，识别的距离就越远

.. _chapter_vision_line_index:

ID:4 Line
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Brief

    .. image:: images/sentry2_vision_line_selecting.png
    
    Find one or up to 5 lines from an image and return its 2 endpoints coordinate and degrees. If it is a curve, an approximate line segment is returned
 
* Parameters

    .. image:: images/sentry2_vision_line_setting.png

    Several parameters can be set in UI setting page:
        Algorithm Performance Level:
            To select the performance of the vision according to different application requirements:
            "Sensitive", "Balance", and "Accurate".

        Maximum Lines Number:
            Range from 1 to 5

* Results

    .. image:: images/sentry2_vision_line_running_01.png

    **NOTE:** The horizontal to the right is 0 degrees, the value is increased by counterclockwise. 
    Upward is 90 degrees, and the horizontal to the left is 180 degrees.

    .. image:: images/sentry2_vision_line_running_02.png

    We use 5 different colors - red, yellow, green, blue, and purple - to distinguish the multi-lines

    ================    ================================
    Result              Brief
    ================    ================================
    1                   X-coordinate of the end point of the line (upper)
    2                   Y-coordinate of the end point of the line (upper)
    3                   X-coordinate of the start point of the line (lower)
    4                   Y-coordinate of the start point of the line (lower)
    5                   Degree of the line
    ================    ================================

* Tips

    1. 背景与线条应清晰分明，比如白底黑线，如果背景杂乱，则可能会检测出背景中的线条
    2. 线条粗细应适中，不可过细，也不可太宽
    3. 一般来说，巡线时，第一条线段始终为屏幕下方先发现的线段，然后是分支线段

.. _chapter_vision_learning_index:

ID:5 Learning
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Brief

    .. image:: images/sentry2_vision_learn_selecting.png

    Objects can be trained and recognized by this vision, up to 25 model data can be saved

* Parameters

    Training New Object:
        New object can be trained in the running page：

        .. image:: images/sentry2_vision_learn_training.png

        A label will be automatically assigned to the new object. 
        The principle is: select the smallest number from the available ID
        
    Delete All Objects:
        Vertically long press the joystick more than 2 seconds in the running page.

        .. image:: images/sentry2_vision_learn_delete_all.png

    ================    ================================
    Param               Brief
    ================    ================================
    1                   None
    2                   None
    3                   None
    4                   None
    5                   Write 0 to delete this object, or write 100 to trained
    ================    ================================

    .. image:: images/sentry2_vision_learn_setting.png

    You can rename or delete the trained model in the UI setting page

    Rename:

        .. image:: images/sentry2_vision_learn_rename.png

        *NOTE*：No more than 32 characters 

    Delete:

        .. image:: images/sentry2_vision_learn_delete.png

* Results

    .. image:: images/sentry2_vision_learn_running.png

    The vision can only judge the existence of the trained object, but not its coordinates and size, so the recognition box is a fixed output value

    ================    ================================
    Result              Brief
    ================    ================================
    1                   Fixed, 160
    2                   Fixed, 120
    3                   Fixed, 224
    4                   Fixed, 224
    5                   Label
    ================    ================================

* Tips

    1. 该算法支持对物体旋转后的识别，但是需要位于“0度，90度，180度，270度”四个方向上识别，左右有15度的容差。如果需要支持任意角度的物体识别，可以拍摄多个角度的图片，比如拍摄了0度，30度，60度时物体的照片，分别对应标签ID1,ID2,ID3，则可以把这3个ID当作同一个物体来处理
    

    2. 训练物体时，如果背景经常变化，那么将不利于物体识别，尽量让被训练物体处于一个相对固定的背景环境中，否则如果更换了背景环境，那么可能需要重新训练

.. _chapter_vision_card_index:

ID:6 Card
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Brief

    .. image:: images/sentry2_vision_card_selecting.png

    recognize a specified card in the image and return its coordinates, size, label and other information. 
    It includes traffic cards, shape cards and numbers cards. The labels are shown in the following table

    **Traffic**

    ================    ================    ================    ================    
    Label               Name                Label               Name              
    ================    ================    ================    ================    
    1                    Forward             2                   Left                
    3                    Right               4                   Turn Around         
    5                    Park                6                   Green               
    7                    Red                 8                   Speed 40            
    9                    Speed 60            10                  Speed 80            
    ================    ================    ================    ================    

    **Shape**

    ================    ================    ================    ================    
    Label               Name                Label               Name            
    ================    ================    ================    ================   
    11                   Check               12                  Cross              
    13                   Circle              14                  Square            
    15                   Triangle            16                  Plus               
    17                   Minus               18                  Divide             
    19                   Equal               
    ================    ================    ================    ================    

    **Number**

    ================    ================    ================    ================    
    Label               Name                Label               Name             
    ================    ================    ================    ================    
    20                   Num 0               21                   Num 1              
    22                   Num 2               23                   Num 3              
    24                   Num 4               25                   Num 5              
    26                   Num 6               27                   Num 7              
    28                   Num 8               29                   Num 9              
    ================    ================    ================    ================    

* Parameters

    None

* Results

    .. image:: images/sentry2_vision_card_running.png

    This vision can recognize multiple cards at same time, and the rotation of cards within 30 degrees can still be recognized but don't rotate the angle too much.

    ================    ================================
    Result              Brief
    ================    ================================
    1                   X-coordinate of the card center
    2                   Y-coordinate of the card center
    3                   Width of the card
    4                   Height of the card
    5                   Label of the card
    ================    ================================

* Tips

    1. 该算法可以检测到远距离的卡片，但此时并不是用户所期望的检测位置，此时可以通过判断“卡片宽度”来排除那些远距离的卡片，比如只有当卡片宽度>50%时，才会触发接下来的动作行为
    
    2. 图像中有多个卡片时，比如拍成一排的卡片，其检测输出顺序以卡片中心点为基准，从左上角(0,0)点逐行扫描，自上而下，从左到右，的顺序输出


.. _chapter_vision_face_index:

ID:7 Face
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Brief

    .. image:: images/sentry2_vision_face_selecting.png

    Faces can be trained and recognized by this vision, up to 25 model data can be saved, its also support mask detection

* Parameters

    Training New Face:
        New object can be trained in the running page：

        .. image:: images/sentry2_vision_face_training.png

    A label will be automatically assigned to the new face. 
        The principle is: select the smallest number from the available ID
        
    Delete All Faces:
        Vertically long press the joystick more than 2 seconds in the running page.

    ================    ================================
    Param               Brief
    ================    ================================
    1                   None
    2                   None
    3                   None
    4                   None
    5                   Write 0 to delete this object, or write 100 to trained
    ================    ================================

    .. image:: images/sentry2_vision_face_setting.png

    You can rename or delete the trained model in the UI setting page, refer to :ref:`Learning<chapter_vision_learning_index>` 

* Results

    .. image:: images/sentry2_vision_face_running.png

    This vision support face detection (new face) and face recognition (trained face) running at the same time. 
    New face will be assigned label 0. 
    Specially, if a new face wearing a mask is detected, "New face (mask)" will be displayed, and the label is fixed at 200

    .. image:: images/sentry2_vision_face_mask.png

    ================    ================================
    Result              Brief
    ================    ================================
    1                   X-coordinate of the face center
    2                   Y-coordinate of the face center
    3                   Width of the face
    4                   Height of the face
    5                   Label, 0:new face, 200:new face with mask
    ================    ================================

.. _chapter_vision_20class_index:

ID:8 20Class
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Brief

    .. image:: images/sentry2_vision_20class_selecting.png

    Identify 20 common types of objects and return their coordinate, size and labels, as shown in the table below.

    **Label**

    ================    ================    ================    ================    
    Label               Name                Label               Name  
    ================    ================    ================    ================    
    1                    Airplane            2                   Bicycle
    3                    Bird                4                   Boat 
    5                    Bottle              6                   Bus 
    7                    Car                 8                   Cat 
    9                    Chair               10                  Cow 
    11                   DiningTable         12                  Dog 
    13                   Horse               14                  Motorbike 
    15                   Person              16                  PottedPlant 
    17                   Sheep               18                  Sofa 
    19                   Train               20                  Tvmonitor 
    ================    ================    ================    ================ 

* Parameters

    .. image:: images/sentry2_vision_20class_setting.png
    
    Algorithm Performance Level:
            To select the performance of the vision according to different application requirements:
            "Sensitive", "Balance", and "Accurate".

* Results
    
    .. image:: images/sentry2_vision_20class_running.png

    ================    ================================
    Result              Brief
    ================    ================================
    1                   X-coordinate of the object center
    2                   Y-coordinate of the object center
    3                   Width of the object
    4                   Height of the object
    5                   Label
    ================    ================================

* Tips

    1. 图像清晰度会较为明显的影响到识别效果，如果图案太小，摄像头无法清晰对焦到图案上，屏幕中图像看起来比较模糊，那么识别效果会变差，可以使用较大的图片

    2. 如果直接对电脑屏幕上的图案进行识别，可以适当调低电脑屏幕的亮度，避免过曝


.. _chapter_vision_qrcode_index:

ID:9 QrCode
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Brief

    .. image:: images/sentry2_vision_qrcode_selecting.png

    A standard QR code (less than 25 ASCII characters) can be recognized

    **ASCII Table**

    ================    ================    ================    ================    ================    ================
    Label               ASCII               Label               ASCII               Label               ASCII
    ================    ================    ================    ================    ================    ================
    32                   空格                 33                  !                   34                  "
    35                   #                   36                  $                   37                  %
    38                   &                   39                  '                   40                  (
    41                   )                   42                  \*                  43                  \+
    44                   ,                   45                  \-                  46                  .
    47                   /                   48                  0                   49                  1
    50                   2                   51                  3                   52                  4
    53                   5                   54                  6                   55                  7
    56                   8                   57                  9                   58                  :
    59                   ;                   60                  <                   61                  =
    62                   >                   63                  ?                   64                  @
    65                   A                   66                  B                   67                  C
    68                   D                   69                  E                   70                  F
    71                   G                   72                  H                   73                  I
    74                   J                   75                  K                   76                  L
    77                   M                   78                  N                   79                  O
    80                   P                   81                  Q                   82                  R
    83                   S                   84                  T                   85                  U
    86                   V                   87                  W                   88                  X
    89                   Y                   90                  Z                   91                  [
    92                   \\                  93                  ]                   94                  ^
    95                   _                   96                  \`                  97                  a
    98                   b                   99                  c                   100                 d
    101                  e                   102                 f                   103                 g
    104                  h                   105                 i                   106                 j
    107                  k                   108                 l                   109                 m
    110                  n                   111                 o                   112                 p
    113                  q                   114                 r                   115                 s
    116                  t                   117                 u                   118                 v
    119                  w                   120                 x                   121                 y
    122                  z                   123                 {                   124                 |
    125                  }                   126                 ~
    ================    ================    ================    ================    ================    ================



* Parameters

    None
    
* Results

    .. image:: images/sentry2_vision_qrcode_running.png

    该算法返回结果包含两种信息，第一组结果为属性信息，后续结果为字符数据，每组结果包含5个字符

    Different than other visions, this vision returns two kinds of information, attribute packet and character data

    **Attribute Packet**

    ================    ================================
    Result              Brief
    ================    ================================
    1                   X-coordinate of the QR Code center
    2                   Y-coordinate of the QR Code center
    3                   Width of the QR Code
    4                   Height of the QR Code
    5                   Number of characters
    ================    ================================

    **Character Data**

    ================    ================================
    Result              Brief
    ================    ================================
    1                   character data
    2                   character data
    3                   character data
    4                   character data
    5                   character data
    ================    ================================

.. _chapter_vision_custom_index:

ID:10 Custom
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Brief

    .. image:: images/sentry2_vision_custom_selecting.png

    If this mode is enabled, the wifi chip will run, details:
    :download:`Sentry2 WiFi Firmware Developing User Guide_V1.1.pdf <../Download/docs/Sentry2 WiFi Firmware Developing User Guide_V1.1.pdf>`

* Parameters

    Custom 

* Results

    Custom

* Tips

    1. 图像清晰度会较为明显的影响到识别效果，如果图案太小，摄像头无法清晰对焦到图案上，屏幕中图像看起来比较模糊，那么识别效果会变差，可以使用较大的图片

    2. 如果直接对电脑屏幕上的图案进行识别，可以适当调低电脑屏幕的亮度，避免过曝


.. _chapter_vision_motion_index:

ID:11 Motion
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Brief

    .. image:: images/sentry2_vision_motion_selecting.png

    Compared the pixel difference of adjacent frames to determine whether there is a motion region in the image, return its coordinate and size. 

* Parameters

    None

* Results

    .. image:: images/sentry2_vision_motion_running.png

    ================    ================================
    Result              Brief
    ================    ================================
    1                   X-coordinate of the region center
    2                   Y-coordinate of the region center
    3                   Width of the region
    4                   Height of the region
    5                   None
    ================    ================================

//end