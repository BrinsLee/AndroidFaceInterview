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
> 1) 采用低内存占用量的编码方式，推荐RGB_565
> 2) 图片压缩，BitmapFactory.Options，我们通过inSampleSize设置缩放倍数，比如写2，即长宽变为原来的1/2，
     图片就是原来的1/4，如果不进行缩放的话设置为1即可！但是不能一味的压缩，毕竟这个值太小 的话，图片会很模糊，
     而且要避免图片的拉伸变形，所以需要我们在程序中动态的计算，这个 inSampleSize的合适值，
     而Options中又有这样一个方法：inJustDecodeBounds，将该参数设置为 true后，decodeFiel并不会分配内存空间，
     但是可以计算出原始图片的长宽，调用 options.outWidth/outHeight获取出图片的宽高，然后通过一定的算法，即可得到适合的 inSampleSize
     
- 栗子：
    
public static int caculateInSampleSize(BitmapFactory.Options options, int reqWidth, int reqHeight) {
    int width = options.outWidth;
    int height = options.outHeight;
    int inSampleSize = 1;
    if (width > reqWidth || height > reqHeight) {
        int widthRadio = Math.round(width * 1.0f / reqWidth);
        int heightRadio = Math.round(height * 1.0f / reqHeight);
        inSampleSize = Math.max(widthRadio, heightRadio);
    }
    return inSampleSize;
}


BitmapFactory.Options options = new BitmapFactory.Options();
options.inJustDecodeBounds = true; // 设置了此属性一定要记得将值设置为false
Bitmap bitmap = null;
bitmap = BitmapFactory.decodeFile(url, options);
options.inSampleSize = computeSampleSize(options,128,128);
options.inPreferredConfig = Bitmap.Config.ARGB_4444;
/* 下面两个字段需要组合使用 */  
options.inPurgeable = true;
options.inInputShareable = true;
options.inJustDecodeBounds = false;
try {
    bitmap = BitmapFactory.decodeFile(url, options);
} catch (OutOfMemoryError e) {
        Log.e(TAG, "OutOfMemoryError");
}
> 3）及时回收Bitmap对象
> [点击查看答案](https://blog.csdn.net/u012758088/article/details/70145656)

- 3.给我谈谈图片压缩。
> 1)设置图片格式：png,无损压缩图片格式，支持Alpha通道，Android切图素材多采用此格式
                  jpg,有损压缩图片格式，不支持Alpha
> 2)质量压缩：质量压缩并不会改变图片在内存中的大小，仅仅会减小图片所占用的磁盘空间的大小，
              因为质量压缩不会改变图片的分辨率，而图片在内存中的大小是根据width*height*一个像素的所占用的字节数计算的，宽高没变，
              在内存中占用的大小自然不会变，质量压缩的原理是通过改变图片的位深和透明度来减小图片占用的磁盘空间大小，所以不适合作为缩略图
> 3)采样率压缩：采样率压缩是通过设置BitmapFactory.Options.inSampleSize，来减小图片的分辨率，进而减小图片所占用的磁盘空间和内存大小。
                设置的inSampleSize会导致压缩的图片的宽高都为1/inSampleSize，整体大小变为原始图片的inSampleSize平方分之一
> 4)缩放压缩：通过减少图片的像素来降低图片的磁盘空间大小和内存大小，可以用于缓存缩略图
                
public void compress(View v) {
    File sdFile = Environment.getExternalStorageDirectory();
    File originFile = new File(sdFile, "originImg.jpg");
    Bitmap bitmap = BitmapFactory.decodeFile(originFile.getAbsolutePath());
    //设置缩放比
    int radio = 8;
    Bitmap result = Bitmap.createBitmap(bitmap.getWidth() / radio, bitmap.getHeight() / radio, Bitmap.Config.ARGB_8888);
    Canvas canvas = new Canvas(result);
    RectF rectF = new RectF(0, 0, bitmap.getWidth() / radio, bitmap.getHeight() / radio);
    //将原图画在缩放之后的矩形上
    canvas.drawBitmap(bitmap, null, rectF, null);
    ByteArrayOutputStream bos = new ByteArrayOutputStream();
    result.compress(Bitmap.CompressFormat.JPEG, 100, bos);
    try {
        FileOutputStream fos = new FileOutputStream(new File(sdFile, "sizeCompress.jpg"));
        fos.write(bos.toByteArray());
        fos.flush();
        fos.close();
    } catch (FileNotFoundException e) {
        e.printStackTrace();
    } catch (IOException e) {
        e.printStackTrace();
    }


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