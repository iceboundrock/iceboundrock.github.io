---
layout: post
title:  "高性能的帧动画"
date:   2016-09-17 12:41:29 +0800
categories: android
---


在开发安卓app的过程中，有不少地方可能会用到帧动画。安卓本身也提供了AnimationDrawable来完成这个工作。然而如果帧动画的面积较大、帧数较多，AnimationDrawable对内存的使用就可能是个问题了。而且在这种情况下，预加载所有动画帧也需要比较长的时间，造成动画需要延迟比较久才能开始播放。为了解决这个问题，我们可以边播放边加载，并且通过使用bitmap重用技术来降低内存占用。



## Bitmap重用技术

在BitmapFactory.Options这个类中有很多有趣的参数，inBitmap就是其中一个。这个接口从API 11引入，我们可以通过这个参数传入一个已经创建好的Bitmap给BitmapFactory，这样BitmapFactory在进行解码的时候就会尽量的复用inBitmap的内存而不是创建一个新的bitmap。对于API 19和以上的系统，得益于新的Bitmap#reconfigure方法，所以只要inBitmap的AllocationByte大于等于待解码图片解码后的体积，BitmapFactory就会尝试复用inBitmap。但是对于API 11～18系统，重用的限制就比较多了，首先，inBitmap必须与要被解码的图片尺寸完全一样，配置也必须一样（例如都是ARGB_8888）。其次，inSampleSize必须为1，也就是不能在解码时进行缩小操作。


## 边播放边加载

有了Bitmap重用技术之后，我们就可以使用少量的几个Bitmap对象作为缓冲区，然后不断的把加载好的frame绘制到屏幕，然后再加载新的frame。在这里，我们使用一个ring buffer。如果图片加载速度特别快或者动画帧间隔比较长，那么这个ring buffer可以只有一个元素，也就是每次加载，然后绘制到canvas，然后再加载。。。但是如果来不及这样做，就可以通过调整适当的ring buffer尺寸，用空间换时间的方式来解决。例如我们需要绘制一个50帧的动画，每一帧的间隔为33ms，为了绘制这个动画，我们创建了一个有b0和b1两个元素的buffer。那么就可以预先加载第一和第二帧到buffer，然后开始播放动画。绘制完第一帧之后，立即开始重用b0加载第三帧，然后等到第二帧的绘制时间到达的时候就进行第二帧的绘制，完成后又立即开始重用b1加载第四帧。等到第三帧的绘制时间到达时距离开始加载第三帧已经有大约60ms的时间，如果此时第三帧已经加载完成，则绘制第三帧，如果第三帧尚未加载完成，则等待第三帧加载完成之后再绘制第三帧。绘制完成然后立即开始用b0加载第五帧。以此类推。


## 一些其它关于Bitmap加载的技巧

* Resampling

如果要加载的图片文件中的图片尺寸比需要的大很多大时候，可以使用inSampleSize来对图片进行等比例的缩小，但是缩小的比例是2的n次方（2、4、8、16...)，如果传入的参数不是2的n次方，底层会自动调整到与参数最接近的2的n次方值。

* scale without createScaledBitmap

除了可以使用inSampleSize来了对图片进行缩小之外，还可以使用inDensity和inTargetDensity配合来对图片进行等比例缩放，而无需先解码图片再用Bitmap#createScaledBitmap进行缩放。这两个参数可以与inSampleSize配合使用。