# ScreenAdaptation-Rapid

## 1. Installation 

###1.1   With [CocoaPods](http://cocoapods.org), add this line to your Podfile.

```
pod 'ScreenCompatible_LFL', '~> 1.0.0'

pod install 

```
###1.2  也可以下载源码后把FrameAutoScaleLFL文件夹直接拖入工程即可,建议 使用pod

###1.3 如果发现使用后代码适配依旧有问题，请检查你的工程中，启动图片是否完整，如果启动图片不完整，默认最大图片尺寸为屏幕尺寸.

##2.代码演示部分

###2.1 使用宏布局,框架定义了一系列宏,更方便设置,详见demo

```objc
// 2016年3.20号更新  
//    1.frame
//    RectMake_LFL(<#X_LFL#>, <#Y_LFL#>, <#WIDTH_LFL#>, <#HEIGHT_LFL#>)
RectMake_LFL(20, 20, 100, 100);
//    2. point
//    PointMake_LFL(<#X_LFL#>, <#Y_LFL#>)
PointMake_LFL(30, 30);
//    3. Size
//    SizeMake_LFL(<#WIDTH_LFL#>, <#HEIGHT_LFL#>)
SizeMake_LFL(60, 60);
//  4. edgeInsets
//    EdgeInsets_LFL(<#X_LFL#>, <#Y_LFL#>, <#WIDTH_LFL#>, <#HEIGHT_LFL#>)
EdgeInsets_LFL(10, 0, 10, 0);
```

### 2.2 使用frame布局

```objc
/**
#pragma mark  
1.1普通使用 以下测试。请切换模拟器查看打印数据对比 是否等比例缩放 iPhone4s -6sPlus 如果公司UI图是以iphone6为基准, 直接写UI图上的坐标即可,如果其他尺寸,进入FrameAutoScaleLFL.h文件头文件修改RealUISrceenHight 和RealUISrceenWidth 为其他尺寸即可.
/**
1.1 Eg: [FrameAutoScaleLFL CGLFLMakeX:30 Y:300 width:200 height:40]
setting a view Frame With the UIfigure  number  all value will be size to fit UIScreen
全部对应数值都将按照比例缩放返回一个进过处理缩放比例的frame.
*/
+ (CGRect)CGLFLMakeX:(CGFloat) x Y:(CGFloat) y width:(CGFloat) width height:(CGFloat) height;

/**
setting a view Frame With the UIfigure number special CGRectGetY
全部对应数值都将按照比例缩放而Y参数除外的frame.eg: 获取上个控件的Y,不可以再次缩放.
*/
+ (CGRect)CGLFLMakeX:(CGFloat) x CGRectGetY:(CGFloat) GetY width:(CGFloat) width height:(CGFloat) height;
/**
setting a view Frame With the UIfigure number special CGRectGetX
返回正常处理通过CGRectGetX方式的frame(其他均正常) special X eg: 20  always 20 Value
比如控件,左边距离 屏幕20 就可以使用这个设置
*/
+ (CGRect)CGLFLMakeGetX:(CGFloat)GetX Y:(CGFloat) Y width:(CGFloat) width height:(CGFloat) height;
/**
setting a view Frame With the UIfigure number special height eg: 64  always 64 Value
比如导航栏的高度,一直不变,或者设置固定的高度,可以使用
*/
+ (CGRect)CGLFLMakeX:(CGFloat) x Y:(CGFloat) y  width:(CGFloat) width heightAllSame:(CGFloat) heightAllSame;

/**
return a fullSrceen frame
*/
+ (CGRect)CGLFLfullScreen;

```
```objc
label_LFL.frame =[FrameAutoScaleLFL CGLFLMakeX:100 Y:0 width:100 height:100];
label_LFL.frame.size = [FrameAutoScaleLFL CGSizeLFLMakeMainScreenSize];
label_LFL.frame.origin= [FrameAutoScaleLFL CGLFLPointMakeX:200 Y:200];
```
##3  对比masonry测试耗时,创建view控件100个,同样位置大小,测试次数三次,相比较而言,对于简单布局,采取frame效率很好.(不同项目要求不同,可以搭配使用).

```objc
- (void)viewDidLoad {
[super viewDidLoad];
// 测试:创建100个view 颜色均为红色
#define TICK NSDate *startTime = [NSDate date];
#define TOCK NSLog(@"耗时统计:%f", -[startTime timeIntervalSinceNow]);

//    [self Masonry];  //  0.017872  0.016632    0.020712

[self ScreenCompatible];  //  0.001341   0.000702  0.000923 
}

/**
Masonry
*/
- (void)Masonry{
for (int i = 0; i < 100; i++) {
UIView *view = [[UIView alloc]init];
view.backgroundColor = [UIColor redColor];
[self.view addSubview:view];
__weak typeof(self) weakSelf=self;
[view mas_makeConstraints:^(MASConstraintMaker *make) {
make.size.mas_equalTo(100);
make.top.equalTo(weakSelf.view.mas_top);
make.left.equalTo(weakSelf.view.mas_left);
}];
}
}
/**
ScreenCompatible
*/
- (void)ScreenCompatible{
for (int i = 0; i < 100; i++) {
UIView *view = [[UIView alloc]init];
view.backgroundColor = [UIColor redColor];
[self.view addSubview:view];
view.frame = RectMake_LFL(0,0, 100, 100);
}
}
```

## 更新日志部分
### 2016年3.5号更新
###1. 修正部分接口
###2. 减少代码多余的框架,演示代码修改.

### 2016年2月19-21更新简介
###1. 增加如果布局中有涉及设置导航栏和使用CGRectGetMaxY() CGRectGetMaxX等导致再次缩放的问题,提供新API方法,eg:长文本无法获取具体Y点,需要通过CGRectGetMaxY()方式,如果使用之前方法,会导致y数值二次缩放,导致出错.代码调整,便于查看demo

### 2016年1月23更新简介

### 一.更新可视化的storyBoard和xib中得view自动适配方法,参考demo代码,一行搞定!,布局控件时可以(按照UI手机型号)尺寸布局,布局完成后,Size选择Freeform即可,可以自行测试.
### 二.修改屏幕宽高的宏定义名字,避免和项目中的重名


### 2016年1月22更新简介
### 一. 适配4s-6sp全机型(之前适配到5而已,网上缩放适配也都是到5)
### 二.虽然代码适配网上有masonry,但是有的确实也比较麻烦点,对于一般公司项目中UI图,可以使用本框架直接设置即可,简单.


### 2015年11月14日更新说明
### 控件设置坐标时 如下写即可 ,直接调用类方法设置,eg: CGRect CGPoint  CGSize

## 4. 参考资料和其他

### 1. https://www.zhihu.com/question/25421514

### 2. http://blog.csdn.net/phunxm/article/details/42174937

### 3. 有任何问题，请及时 issues me

Email:  dragonli_52171@163.com