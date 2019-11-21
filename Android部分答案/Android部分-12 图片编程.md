### 2.12 图片编程

- 1.你对Bitmap了解吗？它在内存中如何存在？

> Bitmap位图包括像素以及长、宽、颜色等描述信息。
  长宽和像素位数是用来描述图片的，可以通过这些信息计算出图片的像素占用内存的大小。
> 1. Config：表示图片像素类型，包括ALPHA_8、RGB_565、ARGB_4444、ARGB_8888 A：透明度；RGB分别是Red、Green、Blue，三种原色

  ARGB_8888：四个通道都是8位，每个像素占用4个字节，图片质量是最高的，但是占用的内存也是最大的；
  
  ARGB_4444：四个通道都是4位，每个像素占用2个字节，图片的失真比较严重；
  
  RGB_565：没有A通道，每个像素占用2个字节，图片失真小，但是没有透明度；
  
  ALPHA_8：只有A通道，每个像素占用1个字节大大小，只有透明度，没有颜色值。
  
  使用场景总结：ARGB_4444失真严重，基本不用；ALPHA_8使用场景特殊，比如设置遮盖效果等；不需要设置透明度，RGB_565是个不错的选择；既要设置透明度，对图片质量要求又高，就用ARGB_8888。
> [点击查看答案](https://www.cnblogs.com/winter-is-coming/p/9112192.html)

- 2.有关Bitmap导致OOM的原因知道吗？如何优化？

> [点击查看答案](https://blog.csdn.net/u012758088/article/details/70145656)

- 3.给我谈谈图片压缩。

> [点击查看答案](https://blog.csdn.net/u013928412/article/details/80358597)

- 4.LruCache & DiskLruCache原理。

> [LruCache原理](https://www.cnblogs.com/huhx/p/useLruCache.html)  
> [DiskLruCache原理](https://www.cnblogs.com/huhx/p/useDiskLruCache.html)

- 5.说说你平常会使用的一些第三方图片加载库,最好给我谈谈它的原理。

> [点击查看答案](http://www.cnblogs.com/dingxiansen/p/8182479.html)  
> 注意：至于库的原理请看常用库部分。

- 6.如果让你设计一个图片加载库，你会如何设计？

> 这个实际上一方面在考察你对第三方图片加载库原理的理解，一方面在考察你是否具备设计库的逻辑头脑。
> 基本上按照某个图片库的设计思路讲给面试官听就可以过了，但是答的出彩的话，可以细化思考优化Glide,Picasso等。

- 7.有一张非常大的图片,你如何去加载这张大图片？

> 这题回答角度是从两个方面着手：  
> **一是图片的大指的是图片尺寸比较大-->**那么加载方式可以是：【1】切割并使用RecyclerView进行加载。 【2】
> 采用局部加载的方式，Android里面是利用BitmapRegionDecoder来局部展示图片的，展示的是一块矩形区域。  
> 二是图片的容量比较大，例如：它可能是一个10M以上的图片，那么加载方式可以是：[点击查看方式加载](https://www.jianshu.com/p/da754f9fad51)

- 8.你知道Android中处理图片的一些库吗(OpenCv & GPUImage ...)？

> 这道题目笔者就不提供答案了，读者可自行查找资料了解。

- 9.如何计算一张图片在内存中占用的大小？

> [点击查看答案](https://www.cnblogs.com/dasusu/p/9789389.html)