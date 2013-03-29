#关于生成文档的事项


可以借助appledoc去生成文档，并且可以通过xcode自带的文档查看器去跳转查看。

##安装步骤：



1. 安装appledoc，简单的方法就是 

    1. ）下载源码 git clone git://github.com/tomaz/appledoc.git
    1. ）安装appledoc，并且安装到/usr/bin等目录下，以便直接appledoc直接运行。
    
        ```
        sudo sh install-appledoc.sh -b /usr/bin -t ~/Library/Application\ Support/appledoc
        ```
        
        
##生成文档
通过下面的命令去生成文档
```
appledoc --project-name WXMessengerDoc --project-company "alibaba-inc" --company-id com.alibaba-inc.WXMessenger [--output ./helloworld]  [includeFilePathOrFile] [--ignore excludeFilePathOrFile] .
```

这个命令只扫描当前目录下所有文件（另外包含includeFilePathOrFile和去除excludeFilePathOrFile) 并生成到某个目录下(默认是当前目录）

该命令结束后会添加到系统的文档目录下

If the path is directory, it's recursively parsed for all source files. If path is file, it's parsed as source file

#####更多关于命令的说明是这样的:
http://gentlebytes.com/appledoc-docs-examples-basic/


2：写代码的时候注意添加注释，我测试下生成的规则后对注释的理解。

```
头文件中注释使用
/*
 我随便写的，反正这里不会被抽取作为文档的一部分，这里随便写点帮助别人如何使用该API的注释就好了，反正安装了docset后，你都可以直接option+左键点出文档相关了，这里说太多参数，函数作用，返回值的东西也没什么用，而且还占地方
 */
- (id)initWithAvatar:(UIImage *)avatarImage;


.m或者.mm文件里添加注释如果下，该部分会被appledoc提取生成文档
/** 你可以从这开始，不过也没什么必要，反正这里和下面那行一样的功能,这行不写可能更加好看点
 这里很特别，这里是添加Discussion的地方，这样我们可以看到这个函数有什么问题啊，注意事项啊，反正他放到了文档里的Discussion里，你自己看着办
 @brief 麻烦告诉我这个函数干什么用的
 @param avatarImage 介绍下avatarImage是什么东西吧。
 @return 这里是返回结果的描述，什么情况返回什么样的结果啊？
 @exception name description
 */
- (id)initWithAvatar:(UIImage *)avatarImage{
	。。。
}
```
上面的方式是头文件和.m文件分开注释
但是对于protocol来说，没有实现文件了，所以还是需要直接添加注释的，这里测试的代码如下

```
/**
 这个协议的描述
 */
@protocol PHAvatarViewDelegate <NSObject>
@optional
/**
 @brief 这个didShowAvatar delegate的作用
 */
-(void)didShowAvatar:(NSString*)avatarName animated:(BOOL)animated;
@required
/**
 @brief willShowAvatar delegate的作用
 */
-(void)willShowAvatar:(NSString*)avatarName animated:(BOOL)animated;
@end
```
通过上面的方式就能生成如下的效果了(protocol类似，这里不贴了），详细的注释和文档效果图见最下面


##说明

这里有几点说明下：（下面说的文档注释指会被提取出生成文档的，普通注释不会被提取)


1:如果文档注释写到.m等文件里也是可行的，这样可以减少头文件的注释大小，头文件只添加一部分普通注释就好了。但是protocol之类的就不得不在声明处添加文档注释了。

2：如果在.m .mm等文件里添加文档注释，那么生成的文档可能有个比较怪异，比如

		Declared In
		PHAvatarView.m
所以这个是写在.m文件里的弊端。

3：要生成文档的添加注释的规则方法如下：

	1）多行注释是从下面这种样式/**注释*/注释里抓的，注意前面需要是/**（实际上/*!也支持），对于/*注释*/这样的注释是不会被提取的。单行注释通过///去抓取，//是不会抓取的
		/**
		  注释
		  */
	2）简介需要标签@brief，而discussion一定要写在最前面。
	3）对于同一个方法不能出现两个两个地方的文档注释，否则.h文件里的注释会覆盖了.m文件
	4）扫描生成注释的时候可以忽略某些文件夹或者某些类型文件。
	5）标签的格式一般为类似@param <name> <description>
	6）可以通过///@name spcename去添加一段需要在文档中标示一组方法的作用，具体看下面的文档效果。


集成安装到XCode中直接安装
-----------------------------------------------------------------
还可以通过在xcode工程里添加一个target来运行该target生成文档
创建一个target，添加一个run script
内容类似下面
```
/usr/bin/appledoc \
--project-name "Messenger" \
--project-company "Gentle Bytes" \
--company-id com.alibaba-inc.WXMessenger \
--ignore UnitTest	\
.
exit 0
```
说明：这里之前安装appledoc是安装到了/usr/bin下，运行该target后自动生成相关的文档并安装到系统文档目录（/Users/youUserName/Library/Developer/Shared/Documentation/DocSets）下。
####参见：
http://www.infinite-loop.dk/wp/wp-content/uploads/2011/06/Build-Documentation-Script.png

#附：
##参考资料：
###更多参考
http://gentlebytes.com/appledoc

github地址: https://github.com/tomaz/appledoc

http://www.infinite-loop.dk/blog/2011/06/providing-custom-documentation-in-xcode/

下面是官方的appledoc源码和生成的文档，可以参考比对。
https://github.com/tomaz/appledoc/blob/master/
http://gentlebytes.com/media/appledoc/examples/v2/latest/Classes/GBGenerator.html#tasks










###附头文件和实现文件的注释和最后的文档效果：

###-------------------------头文件------------------------

```
/** 
 这个类的作用
 */
@interface PHAvatarView : UIView
{
    
}

///-----------------------------------------------------------------
/// @name Initialization 
///-----------------------------------------------------------------


/*
 * 我随便写的，反正这里不会被抽取作为文档的一部分，这里随便写点帮助别人如何使用该API的注释就好了，反正安装了docset后，你都可以直接option+左键点出文档相关了，这里说太多参数，函数作用，返回值的东西也没什么用，而且还占地方
 *
 *
 */
//我随便写，这里不会被提取
- (id)initWithAvatar:(UIImage *)avatarImage;

@end

/**
 这个协议的描述
 */
@protocol PHAvatarViewDelegate <NSObject>
@optional
/**
 @brief 这个didShowAvatar delegate的作用
 */
-(void)didShowAvatar:(NSString*)avatarName animated:(BOOL)animated;
@required
/**
 @brief willShowAvatar delegate的作用
 */
-(void)willShowAvatar:(NSString*)avatarName animated:(BOOL)animated;
@end
```

###------------------------    实现文件    ---------------------

```
@implementation PHAvatarView

/** 你可以从这开始，不过也没什么必要，反正这里和下面那行一样的功能,这行不写可能更加好看点
 这里很特别，这里是添加Discussion的地方，这样我们可以看到这个函数有什么问题啊，注意事项啊，反正他放到了文档里的Discussion里，你自己看着办
 @brief 麻烦告诉我这个函数干什么用的
 @param avatarImage 介绍下avatarImage是什么东西吧
 @return 这里是返回结果的描述，什么情况返回什么样的结果啊？
 @exception aaa 异常提示
 */
- (id)initWithAvatar:(UIImage *)avatarImage{
    if (self = [super initWithFrame:CGRectMake(0, 0, 50, 50)]) {
        UIImageView *avatarImageView = [[[UIImageView alloc] initWithImage:avatarImage] autorelease];
        [avatarImageView sizeToFit];
        avatarImageView.layer.cornerRadius = avatarImage.size.width/2.0;
        avatarImageView.clipsToBounds = YES;
        avatarImageView.frame = CGRectMake(2, 0, avatarImageView.frame.size.width, avatarImageView.frame.size.height);
        [self addSubview:avatarImageView];

        UIImageView *maskView = [[[UIImageView alloc] initWithImage:[UIImage imageNamed:@"invites-user-avatar-edge.png"]] autorelease];
        [maskView sizeToFit];
        [self addSubview:maskView];
    }
    return self;
}

@end
```


###-----------------     文档效果     ---------------------------
<img src="http://yunpan.alibaba-inc.com/share/scan.do?info=8H2tK3INh&pInfo=8H2tK3INh&app_name=">