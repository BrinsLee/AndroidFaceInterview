### 2.11 ListView & RecyclerView

- 1.ListView是什么？如何使用？

> [点击查看答案](https://www.cnblogs.com/s-y-j/p/6548032.html)

- 2.RecyclerView是什么？如何使用？如何返回不一样的Item。

> [RecyclerView是什么？如何使用？](https://www.cnblogs.com/shiwei-bai/p/4972039.html)  
> [如何返回不一样的Item](https://www.cnblogs.com/zhujiabin/p/7172001.html)

- 3.ListView和RecycyclerView的区别是什么？

> [点击查看答案](https://blog.csdn.net/u014497502/article/details/50917321)

- 4.分别讲讲你对ListView & RecyclerView的优化经验。

> [ListView的优化](https://www.cnblogs.com/mfmdaoyou/p/6938208.html)  
> [RecyclerView的优化](https://www.cnblogs.com/ldq2016/p/9039979.html)

- 5.给我说说RecyclerView的回收复用机制。
> RecyclerView 的回收复用机制的内部实现都是由 Recycler 内部类实现，
> ![avatar](/app/src/main/res/drawable/1.png)  

Q1:如果向下滑动，新一行的5个卡位的显示会去复用缓存的 ViewHolder，第一行的5个卡位会移出屏幕被回收，
    那么在这个过程中，是先进行复用再回收？还是先回收再复用？还是边回收边复用？
    也就是说，新一行的5个卡位复用的 ViewHolder 有可能是第一行被回收的5个卡位吗？
    
> 答：ViewHolder先复用再回收，新一行的5个卡位先去目前的 mCachedViews 和 ViewPool 的缓存中寻找复用，
      没有就重新创建，然后移出屏幕的那行的5个卡位再回收缓存到 mCachedViews 和 ViewPool 里面，
      所以新一行5个卡位和复用不可能会用到刚移出屏幕的5个卡位。

Q2: 在这个过程中，为什么当 RecyclerView 再次向上滑动重新显示第一行的5个卡位时，只有后面3个卡位触发了 onBindViewHolder() 方法，
    重新绑定数据呢？明明5个卡位都是复用的。
 
> 答：滑动场景下涉及到的回收和复用的结构体是 mCachedViews 和 ViewPool，前者默认大小为2，后者为5。
      所以，当第三行显示出来后，第一行的5个卡位被回收，回收时先缓存在 mCachedViews，满了再移出旧的到 ViewPool 里，
      所有5个卡位有2个缓存在 mCachedViews 里，3个缓存在 ViewPool，至于是哪2个缓存在 mCachedViews，这是由 LayoutManager 控制。
      
>    上面讲解的例子使用的是 GridLayoutManager，滑动时的回收逻辑则是在父类 LinearLayoutManager 里实现，
     回收第一行卡位时是从后往前回收，所以最新的两个卡位是0、1，会放在 mCachedViews 里，而2、3、4的卡位则放在 ViewPool 里。

>    所以，当再次向上滑动时，第一行5个卡位会去两个结构体里找复用，之前说过，mCachedViews 里存放的 ViewHolder 只有原本位置的卡位才能复用，
     所以0、1两个卡位都可以直接去 mCachedViews 里拿 ViewHolder 复用，而且这里的 ViewHolder 是不用重新绑定数据的，
     至于2、3、4卡位则去 ViewPool 里找，刚好 ViewPool 里缓存着3个 ViewHolder，所以第一行的5个卡位都是用的复用的，
     而从 ViewPool 里拿的复用需要重新绑定数据，才会这样只有三个卡位需要重新绑定数据。
     
Q3：接下去不管是向上滑动还是向下滑动，滑动几次，都不会再有 onCreateViewHolder() 的日志了，
    也就是说 RecyclerView 总共创建了17个 ViewHolder，但有时一行的5个卡位只有3个卡位需要重新绑定数据，
    有时却又5个卡位都需要重新绑定数据，这是为什么呢？
> 答：有时一行只有3个卡位需要重新绑定的原因跟Q2一样，因为 mCachedView 里正好缓存着当前位置的 ViewHolder，
    本来就是它的 ViewHolder 当然可以直接拿来用。而至于为什么会创建了17个 ViewHolder，那是因为再第四行的卡位要显示出来时，
    ViewPool 里只有3个缓存，而第四行的卡位又用不了 mCachedViews 里的2个缓存，因为这两个缓存的是6、7卡位的 ViewHolder，
    所以就需要再重新创建2个 ViewHodler 来给第四行最后的两个卡位使用。


> [点击查看答案](https://www.cnblogs.com/dasusu/p/7746946.html)

- 6.说说你是如何给ListView & RecyclerView加上拉刷新 & 下拉加载更多机制。

> 这题笔者不提供答案。SwipeRefreshLayout加第三方库都可以讲讲。