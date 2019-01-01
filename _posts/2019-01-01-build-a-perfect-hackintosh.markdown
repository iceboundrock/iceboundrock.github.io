# 装一台完美的“黑” 苹果
## 起因

传说中会有“[significant display performance upgrade](https://www.macrumors.com/roundup/imac/)”的iMac并没有在10月的发布会上如约而至。新mac mini虽然可以升级内存，但是它不能选配独立显卡而且一旦拆机就会丧失保修。经过一番研究，我选择了自己攒一台“黑”苹果。

## 目标

1. 不修改系统文件
1. 尽量使用系统自带的驱动
1. 可以启用SIP
1. macOS升级时尽量少维护

## 硬件选择

### CPU
众所周知，Apple并没有任何使用AMD CPU的产品。虽然有爱好者为AMD的CPU创建了相应的内核，甚至开设了[AMD OS X](https://amd-osx.com/)网站，但是AMD CPU的支持依然不甚理想。虽然Intel第9代i7已经上市，但是还没有任何苹果自己的产品采用。为了稳妥起见我选了Intel的i7 8700K。

### 主板
macOS对Intel的主板芯片组已经支持的很好，需要考虑的是板载声卡和网卡。我这次选择了Gigabyte的[Z370-HD3P](https://www.gigabyte.com/Motherboard/Z370-HD3P-rev-10#sp)。

### 声卡
板载声卡芯片的选择可以参考这个[项目](https://github.com/acidanthera/AppleALC)。由于这个项目设计的非常优雅，不需要编程即可新增codecs，所以大多数板载声卡都已经得到支持了。

### 网卡
网卡就相对复杂一些，目前支持的比较好的就只有Intel的网卡芯片，具体可以参考这个[项目](https://github.com/Mieze/IntelMausiEthernet)。IntelMausiEthernet只开源了代码而没有发布编译好的二进制包，你可以自己编译或者从这个[镜像项目](https://bitbucket.org/RehabMan/os-x-intel-network/overview)下载编译好的kext。

### 显卡
* Intel
苹果还在支持周期的电脑产品基本都用了。Intel显卡驱动直接在内核里。除了走HDMI输出的时候有些小问题，其他都很完美。HDMI问题的解决方法后面会谈到。

* AMD
苹果自己还在支持周期的电脑产品大量使用了AMD显卡，所以驱动支持方面还是很稳的，由于未来会有一些编(wan)辑(er)视(you)频(xi)的需求，所以这次我买了一块RX 570。

* NVIDIA
由于NVIDIA至今没有发布针对macOS mojave的web driver。如果想用macOS mojave那么就只能用intel集成显卡或者AMD显卡。如果确实想用N显卡，可以装macOS High Sierra。需要注意的是，NVIDIA web driver是跟macOS版本紧密关联的。哪怕同为10.3.6不同的build也要安装不同的web driver。具体对应关系可以参考这个[网页](https://www.tonymacx86.com/nvidia-drivers/)。

## 软件策略

Hackintosh技术从出现到现在已经走过了十多年的时光，从最早期需要在命令行里各种折腾到现在基本可以无脑安装。Hackintosh配套软件的实现策略也更新了几代。最新的策略是所谓的[Vanilla Install](https://hackintosh.gitbook.io/-r-hackintosh-vanilla-desktop-guide/)。这次安装我就是根据这个指南完成的。

## 一些小坑

### Intel显卡HDMI输出偏色
由于着急安装，所以我没等显卡送到就开始装系统了，所以体验了一次粉红色的安装界面。安装完之后参考[这段](https://hackintosh.gitbook.io/-r-hackintosh-vanilla-desktop-guide/config.plist-per-hardware/coffee-lake#pink-purple-tint)说明设置framebuffer相关参数即可解决偏色问题。

### Preview无法打开jpeg文件

其实Preview是直接被hang住了，必须`Force Quit`。堆栈是这样的

```

Heaviest stack for the main thread of the target process:
  14  start + 1 (libdyld.dylib + 93913) [0x7fff74f77ed9]
  14  NSApplicationMain + 780 (AppKit + 14499) [0x7fff4526f8a3]
  14  -[NSApplication run] + 699 (AppKit + 82277) [0x7fff45280165]
  14  -[NSApplication(NSEvent) _nextEventMatchingEventMask:untilDate:inMode:dequeue:] + 1362 (AppKit + 106754) [0x7fff45286102]
  14  _DPSNextEvent + 997 (AppKit + 111459) [0x7fff45287363]
  14  _BlockUntilNextEventMatchingListInModeWithFilter + 64 (HIToolbox + 42344) [0x7fff46fcc568]
  14  ReceiveNextEventCommon + 371 (HIToolbox + 42740) [0x7fff46fcc6f4]
  14  RunCurrentEventLoopInMode + 293 (HIToolbox + 43701) [0x7fff46fccab5]
  14  CFRunLoopRunSpecific + 463 (CoreFoundation + 240968) [0x7fff47d35d48]
  14  __CFRunLoopRun + 2335 (CoreFoundation + 243898) [0x7fff47d368ba]
  14  __CFRUNLOOP_IS_SERVICING_THE_MAIN_DISPATCH_QUEUE__ + 9 (CoreFoundation + 246187) [0x7fff47d371ab]
  14  _dispatch_main_queue_callback_4CF + 1125 (libdispatch.dylib + 61283) [0x7fff74f34f63]
  14  _dispatch_client_callout + 8 (libdispatch.dylib + 15823) [0x7fff74f29dcf]
  14  _dispatch_call_block_and_release + 12 (libdispatch.dylib + 11603) [0x7fff74f28d53]
  14  ??? (Preview + 1031260) [0x106d38c5c]
  14  ??? (Preview + 47787) [0x106c48aab]
  14  ??? (Preview + 52028) [0x106c49b3c]
  14  -[NSObject(NSThreadPerformAdditions) performSelectorOnMainThread:withObject:waitUntilDone:] + 131 (Foundation + 736362) [0x7fff4a133c6a]
  14  -[NSObject(NSThreadPerformAdditions) performSelector:onThread:withObject:waitUntilDone:modes:] + 1123 (Foundation + 704709) [0x7fff4a12c0c5]
  14  ??? (Preview + 52263) [0x106c49c27]
  14  -[NSWindowController showWindow:] + 36 (AppKit + 2736764) [0x7fff4550827c]
  14  ??? (Preview + 46372) [0x106c48524]
  14  -[NSWindowController window] + 110 (AppKit + 1041970) [0x7fff4536a632]
  14  -[NSWindowController _windowDidLoad] + 542 (AppKit + 1708488) [0x7fff4540d1c8]
  14  ??? (Preview + 53991) [0x106c4a2e7]
  14  ??? (Preview + 55414) [0x106c4a876]
  14  ??? (Preview + 101183) [0x106c55b3f]
  14  ??? (Preview + 126475) [0x106c5be0b]
  14  ??? (Preview + 127972) [0x106c5c3e4]
  14  -[IKImageContentView setImageURL:imageAtIndex:withDisplayProperties:] + 160 (ImageKit + 13437) [0x7fff5235c47d]
  14  -[IKImageContentView _setImageFromImageSource:imageAtIndex:withDisplayProperties:] + 716 (ImageKit + 962743) [0x7fff524440b7]
  14  __82-[IKImageContentView _setImageFromImageSource:imageAtIndex:withDisplayProperties:]_block_invoke + 64 (ImageKit + 962979) [0x7fff524441a3]
  14  -[IKImageContentView _newScaledImageFromSource:index:imageScale:canUseExistingThumbnail:displayProperties:] + 483 (ImageKit + 960502) [0x7fff524437f6]
  14  CGImageSourceCreateThumbnailAtIndex + 357 (ImageIO + 424688) [0x7fff4a8d1af0]
  14  IIOImageSource::createThumbnailAtIndex(unsigned long, IIODictionary*) + 3207 (ImageIO + 468821) [0x7fff4a8dc755]
  14  CGImageCreateCopyWithParametersNew(CGImage*, CGColor*, CGAffineTransform, unsigned long, unsigned long, unsigned long, unsigned long, unsigned long, CGColorSpace*, unsigned int, bool, int, int, bool) + 2197 (ImageIO + 414604) [0x7fff4a8cf38c]
  14  CGContextDrawImage + 51 (CoreGraphics + 210463) [0x7fff4817d61f]
  14  CGContextDrawImageWithOptions + 432 (CoreGraphics + 210916) [0x7fff4817d7e4]
  14  CGContextDelegateDrawImage + 41 (CoreGraphics + 212998) [0x7fff4817e006]
  14  ripc_DrawImage + 707 (CoreGraphics + 213721) [0x7fff4817e2d9]
  14  ripc_AcquireRIPImageData + 293 (CoreGraphics + 216932) [0x7fff4817ef64]
  14  RIPImageCacheGetRetained + 661 (CoreGraphics + 218174) [0x7fff4817f43e]
  14  RIPImageDataInitializeShared + 207 (CoreGraphics + 219310) [0x7fff4817f8ae]
  14  CGSImageDataLock + 1256 (CoreGraphics + 220691) [0x7fff4817fe13]
  14  img_data_lock + 7610 (CoreGraphics + 244166) [0x7fff481859c6]
  14  img_interpolate_read + 724 (CoreGraphics + 548174) [0x7fff481cfd4e]
  14  img_raw_read + 1290 (CoreGraphics + 251836) [0x7fff481877bc]
  14  get_access_session + 44 (CoreGraphics + 254231) [0x7fff48188117]
  14  CGAccessSessionCreate + 98 (CoreGraphics + 246854) [0x7fff48186446]
  14  CGDataProviderRetainData + 69 (CoreGraphics + 247180) [0x7fff4818658c]
  14  provider_for_destination_retain_data + 17 (CoreGraphics + 247215) [0x7fff481865af]
  14  CGDataProviderRetainData + 69 (CoreGraphics + 247180) [0x7fff4818658c]
  14  imageProvider_retain_data + 77 (CoreGraphics + 247307) [0x7fff4818660b]
  14  CGImageProviderCopyImageBlockSetWithOptions + 137 (CoreGraphics + 247520) [0x7fff481866e0]
  14  IIOImageProviderInfo::CopyImageBlockSetWithOptions(void*, CGImageProvider*, CGRect, CGSize, __CFDictionary const*) + 650 (ImageIO + 99140) [0x7fff4a882344]
  14  IIOImageProviderInfo::copyImageBlockSetWithOptions(CGImageProvider*, CGRect, CGSize, __CFDictionary const*) + 509 (ImageIO + 99761) [0x7fff4a8825b1]
  14  IIO_Reader::CopyImageBlockSetProc(void*, CGImageProvider*, CGRect, CGSize, __CFDictionary const*) + 101 (ImageIO + 100125) [0x7fff4a88271d]
  14  AppleJPEGReadPlugin::copyImageBlockSet(InfoRec*, CGImageProvider*, CGRect, CGSize, __CFDictionary const*) + 1708 (ImageIO + 104576) [0x7fff4a883880]
  14  AppleJPEGReadPlugin::createImageBlockSetWithHardwareDecode(InfoRec*, CGImageProvider*, CGSize, bool*) + 85 (ImageIO + 926615) [0x7fff4a94c397]
  14  AppleJPEGReadPlugin::createImageBlockSetWithHardware_intel(InfoRec*, CGImageProvider*, __CFData const*, CGSize, bool*) + 343 (ImageIO + 531227) [0x7fff4a8ebb1b]
  14  AppleJPEGReadPlugin::createIOSurfaceWithHardware_intel(CGImageProvider*, __CFData const*, unsigned int, VPA_HWJPEGDecodeSession*, __IOSurface**) + 74 (ImageIO + 527770) [0x7fff4a8ead9a]
  14  AppleJPEGReadPlugin::acquireSession() + 24 (ImageIO + 526358) [0x7fff4a8ea816]
  14  _pthread_mutex_firstfit_lock_slow + 226 (libsystem_pthread.dylib + 5303) [0x7fff751694b7]
  14  __psynch_mutexwait + 10 (libsystem_kernel.dylib + 15990) [0x7fff750b3e76]
 *14  psynch_mtxcontinue + 0 (pthread + 10095) [0xffffff7f8238976f]

```
从堆栈的这句`AppleJPEGReadPlugin::createImageBlockSetWithHardware_intel`大概可以猜出是调用Intel GPU做解码出了问题。搜了一圈之后发现原因是我根据 [文档](https://hackintosh.gitbook.io/-r-hackintosh-vanilla-desktop-guide/config.plist-per-hardware/coffee-lake#explanation-5)在SMBIOS中把`Product Model`设置为`18,1`了。实际上我是用RX 570来接显示器的。解决办法也很简单，把`Product Model`设置为`18,3`就好了，记得要用[macserial](https://github.com/acidanthera/macserial)来生成对应的序列号。这部分都很简单但是故事并没有这样结束...

### iMovie打开就crash

在SMBIOS中把`Product Model`设置为`18,3`之后我的iMovie就打不开了，在dock里跳两下就会崩溃掉。崩溃堆栈大概是这样：
```
Application Specific Information:
/Applications/iMovie.app/Contents/MacOS/../Frameworks/ProGraphics.framework/Versions/A/ProGraphics
abort() called

Thread 0 Crashed:: Dispatch queue: com.apple.main-thread
0   libsystem_kernel.dylib        	0x00007fff78e6523e __pthread_kill + 10
1   libsystem_pthread.dylib       	0x00007fff78f1bc1c pthread_kill + 285
2   libsystem_c.dylib             	0x00007fff78dce1c9 abort + 127
3   com.apple.procore.framework   	0x000000010af61511 pcAbortImpl + 9
4   com.apple.ProGraphics         	0x0000000117de615a (anonymous namespace)::PGInfoImpl::instance() + 730
5   dyld                          	0x00000001140e4cc8 ImageLoaderMachO::doModInitFunctions(ImageLoader::LinkContext const&) + 518
6   dyld                          	0x00000001140e4ec6 ImageLoaderMachO::doInitialization(ImageLoader::LinkContext const&) + 40
7   dyld                          	0x00000001140e00da ImageLoader::recursiveInitialization(ImageLoader::LinkContext const&, unsigned int, char const*, ImageLoader::InitializerTimingList&, ImageLoader::UninitedUpwards&) + 358
8   dyld                          	0x00000001140e006d ImageLoader::recursiveInitialization(ImageLoader::LinkContext const&, unsigned int, char const*, ImageLoader::InitializerTimingList&, ImageLoader::UninitedUpwards&) + 249
9   dyld                          	0x00000001140e006d ImageLoader::recursiveInitialization(ImageLoader::LinkContext const&, unsigned int, char const*, ImageLoader::InitializerTimingList&, ImageLoader::UninitedUpwards&) + 249
10  dyld                          	0x00000001140df254 ImageLoader::processInitializers(ImageLoader::LinkContext const&, unsigned int, ImageLoader::InitializerTimingList&, ImageLoader::UninitedUpwards&) + 134
11  dyld                          	0x00000001140df2e8 ImageLoader::runInitializers(ImageLoader::LinkContext const&, ImageLoader::InitializerTimingList&) + 74
12  dyld                          	0x00000001140d1d20 dyld::runInitializers(ImageLoader*) + 82
13  dyld                          	0x00000001140db75f dlopen_internal + 609
14  libdyld.dylib                 	0x00007fff78d115f3 dlopen + 200
15  com.apple.CoreFoundation      	0x00007fff4bb0af8e _CFBundleDlfcnLoadBundle + 148
16  com.apple.CoreFoundation      	0x00007fff4bbaad83 _CFBundleLoadExecutableAndReturnError + 519
17  com.apple.Foundation          	0x00007fff4ded70a0 -[NSBundle loadAndReturnError:] + 481
18  com.apple.Flexo               	0x000000010b3f1122 +[FFPluginDirectoryScanner _scanDirectory:withExtension:scanned:delegate:didLoadSelector:] + 930
19  com.apple.Flexo               	0x000000010b3f15fb +[FFPluginDirectoryScanner scanForPluginsInDirectory:withExtension:delegate:didLoadSelector:] + 1067
20  libobjc.A.dylib               	0x00007fff77c5a248 CALLING_SOME_+initialize_METHOD + 19
21  libobjc.A.dylib               	0x00007fff77c4a00c _class_initialize + 282
22  libobjc.A.dylib               	0x00007fff77c49f42 _class_initialize + 80
23  libobjc.A.dylib               	0x00007fff77c49a19 lookUpImpOrForward + 238
24  libobjc.A.dylib               	0x00007fff77c49494 _objc_msgSend_uncached + 68
25  com.apple.Foundation          	0x00007fff4ded74a9 -[NSBundle loadAndReturnError:] + 1514
26  com.apple.Flexo               	0x000000010b3f1122 +[FFPluginDirectoryScanner _scanDirectory:withExtension:scanned:delegate:didLoadSelector:] + 930
27  com.apple.Flexo               	0x000000010b3f15fb +[FFPluginDirectoryScanner scanForPluginsInDirectory:withExtension:delegate:didLoadSelector:] + 1067
28  com.apple.Flexo               	0x000000010b14924a +[FFEffect(RuntimeBundleLoading) initEffectRegistry] + 218
29  com.apple.Flexo               	0x000000010b17374f FFInitializePart2 + 111
30  com.apple.Flexo               	0x000000010b1158d6 +[Flexo finishInit] + 22
31  com.apple.iMovieApp           	0x000000010acbcf19 main + 1033
32  libdyld.dylib                 	0x00007fff78d25ed9 start + 1

```

于是我又搜了一圈，发现这个问题是也是因为iGPU导致的。根本原因是我的`ig-platform-id`没有写对。其实[文档](https://hackintosh.gitbook.io/-r-hackintosh-vanilla-desktop-guide/config.plist-per-hardware/coffee-lake#properties)里已经说了正确的配置。在CPU用8700K但是用独立显卡接显示器的情况下，这个`ig-platform-id`的值应该设为`0x3E920003`。如果想了解更多的话可以参考这篇[文章](https://www.insanelymac.com/forum/topic/334899-intel-framebuffer-patching-using-whatevergreen/)，里面详细讨论了多种CPU在多个macOS版本下配置`ig-platform-id`和`framebuffer`的细节。

### 旋转显示器问题
我外接了两个显示器，一个旋转了270度。每次重启都之后这个旋转的显示器上的画面都非常诡异。屏幕上的图像实际上是旋转了270度之后的竖条形，但是被横着显示在竖起来的屏幕上。上下都是黑边，而且鼠标和屏幕像素也对不上。在macOS的System Preferences里重新设置旋转角度后又可以正常使用。暂时没有根治的办法，一个workaround是用这个[项目](https://github.com/CdLbB/fb-rotate)在用户登录时自动旋转两次屏幕。用`Automator`制作一个application来调用下面这段脚本，然后把这个application加入`Login Item`即可。

```bash
#!/bin/bash

fbr="$HOME/code/fb-rotate/fb-rotate"
display_id=$($fbr -l | grep 2560 | awk '{print $1}')
$fbr -d $display_id -r 90
$fbr -d $display_id -r 270
```
