---
layout: post
title:  LayoutManager
categories: Android
keywords: Android
mathjax: true
prism: [Android]
---

## 需求:
+ 屏幕上最多只能展示5个item(4个+首尾2个半个item)
+ 可以左右无限滑动

## 关健步骤：

### 创建自定义LayoutManager
首先，自定义 LooperLayoutManager 继承自 RecyclerView.LayoutManager，然后需要实现抽象方法 generateDefaultLayoutParams()，这个方法的作用是给 itemView 设置默认的LayoutParams，直接返回如下就行。
### 预定义每个item的宽度，item之间的间隔
getWidth()获取屏幕宽度，view.getLayoutParams()获取item的间隔、宽度属性
### 打开滚动开关
对滚动方向做处理，重写canScrollHorizontally()方法，打开横向滚动开关。注意我们是实现横向无限循环滚动，所以实现此方法，如果要对垂直滚动做处理，则要实现canScrollVertically()方法。
### 对RecyclerView进行初始化布局
重写 onLayoutChildren() 方法，开始对itemView初始化布局。
onLayoutChildren()对所有的 itemView 进行布局，一般会在初始化和调用 Adapter 的 notifyDataSetChanged() 方法时调用。
标注2：
detachAndScrapAttachedViews(recycler) 方法会将所有的 itemView 从View树中全部detach，然后放入scrap缓存中。RecyclerView是有一个二级缓存的，一级缓存是 scrap 缓存,二级缓存是 recycler 缓存，其中从View树上detach的View会放入scrap缓存里，调用removeView()删除的View会放入recycler缓存中。
标注3：
recycler.getViewForPosition(i) 方法会从缓存中拿到对应索引的 itemView，这个方法内部会先从 scrap 缓存中取 itemView，如果没有则从 recycler 缓存中取，如果还没有则调用 adapter 的 onCreateViewHolder() 去创建 itemView。
标注5：
layoutDecorated() 方法会对 itemView 进行布局排版，这里可以看出来，我们是根据宽依次往父容器的右边排下去，直到下一个 itemView的顶点位置超过了RecyclerView 的宽度。
### 对RecyclerView进行滚动和回收itemView处理
对RecyclerView的子item进行排版布局后，运行一下效果就会出现了，不过这时候我们滑动列表会发现滑动后变成空白了，所以就该对滑动操作进行处理了。
我们打开了横向滚动的开关，所以对应的，我们要重写 scrollHorizontallyBy()方法进行横向滑动操作。

#### 滑动逻辑:
横向滑动的时候，对左右两边按顺序填充itemView
滑动itemView
回收已经不可见的itemView



 **源码如下:**
```
/**
 * @author ChenYu
 * My personal blog  https://prettyant.github.io
 * <p>
 * Created on 4:01 PM  2020/8/07
 * PackageName : com.example.spdbsoappandroid.textonline.ui.widget
 * describle : recyclerveiw的左右滑动
 * 需求:
 * a、屏幕上最多只能展示5个item(4个+首位2个半个item)
 * b、可以左右无限滑动
 * 需要注意每个item之间的间隔、宽度等属性设置
 */
public class LooperLayoutManager extends LinearLayoutManager {
    private static final String  TAG          = "LooperLayoutManager";
    private              boolean looperEnable = true;
    private              int     totalWidth;
    private              int     singleWidth;
    private              int     singleHeight;
    private              int     margenDistance;
    private static final float   heightTimes  = 1.15f;//高度的倍数，即高度为宽度的几倍
    private static final int     itemCounts   = 5;//每一页最多展示的item的数目


    public LooperLayoutManager(Context context) {
        super(context);
    }


    public LooperLayoutManager(Context context, int orientation, boolean reverseLayout) {
        super(context, orientation, reverseLayout);
    }

    public LooperLayoutManager(Context context, AttributeSet attrs, int defStyleAttr, int defStyleRes) {
        super(context, attrs, defStyleAttr, defStyleRes);
    }

    /**
     * 预定义每个item的宽度，item之间的间隔
     */
    private void initParams() {
        totalWidth = getWidth();
        singleWidth = totalWidth / (itemCounts + 1);//设置每页显示5个item,还剩一个item的距离平分出去，用作每个item之间的间隔
        singleHeight = (int) (singleWidth * heightTimes);//设置每个item的高度,高度为宽度的heightTimes倍
        margenDistance = singleWidth / (itemCounts * 2);//每个item的左右间距
    }

    public void setLooperEnable(boolean looperEnable) {
        this.looperEnable = looperEnable;
    }

    @Override
    public RecyclerView.LayoutParams generateDefaultLayoutParams() {
        return new RecyclerView.LayoutParams(ViewGroup.LayoutParams.WRAP_CONTENT,
                ViewGroup.LayoutParams.WRAP_CONTENT);
    }

    @Override
    public boolean canScrollHorizontally() {
        if (getItemCount() <= itemCounts) {
            return false;
        }
        return true;
    }

    @Override
    public boolean canScrollVertically() {
        return false;
    }

    @Override
    public void onLayoutChildren(RecyclerView.Recycler recycler, RecyclerView.State state) {
        initParams();
        if (getItemCount() <= 0) {
            return;
        }
        //preLayout主要支持动画，直接跳过
        if (state.isPreLayout()) {
            return;
        }
        //将视图分离放入scrap缓存中，以准备重新对view进行排版
        detachAndScrapAttachedViews(recycler);

        int autualWidth = 0;
        for (int i = 0; i < getItemCount(); i++) {
            //初始化，将在屏幕内的view填充
            View itemView = recycler.getViewForPosition(i);
            itemView = getSettedView(itemView);

            addView(itemView);
            //测量itemView的宽高
            measureChildWithMargins(itemView, 0, 0);

            int width  = getDecoratedMeasuredWidth(itemView);
            int height = getDecoratedMeasuredHeight(itemView);
            //根据itemView的宽高进行布局
            if (getItemCount() <= itemCounts) {
                layoutDecorated(itemView, autualWidth + margenDistance, 0, autualWidth + width + margenDistance, height);//小于5个时，不偏移
            } else {
                layoutDecorated(itemView, autualWidth - width / 2, 0, autualWidth + width / 2, height);//设置item的属性，并且向左平移半个单位
            }
            autualWidth = autualWidth + singleWidth + margenDistance * 2;
            //如果当前布局过的itemView的宽度总和大于RecyclerView的宽，则不再进行布局
            if (autualWidth > totalWidth) {
                break;
            }
        }
    }

    /**
     * 设置item的参数，并返回item
     *
     * @param view
     * @return
     */
    private View getSettedView(View view) {
        ViewGroup.LayoutParams layoutParams = view.getLayoutParams();
        layoutParams.width = singleWidth;
        layoutParams.height = singleHeight;
        view.setLeft(margenDistance);//设置左边距
        view.setRight(margenDistance);//设置右边距
        view.setLayoutParams(layoutParams);//给子item设置参数
        return view;
    }

    @Override
    public int scrollHorizontallyBy(int dx, RecyclerView.Recycler recycler, RecyclerView.State state) {
        //1.左右滑动的时候，填充子view
        int travl = fill(dx, recycler, state);
        if (travl == 0) {
            return 0;
        }

        //2.滚动
        offsetChildrenHorizontal(travl * -1);

        //3.回收已经离开界面的
        recyclerHideView(dx, recycler, state);
        return travl;
    }

    /**
     * 左右滑动的时候，填充
     */
    private int fill(int dx, RecyclerView.Recycler recycler, RecyclerView.State state) {
        if (dx > 0) {
            //标注1.向左滚动
            View lastView = getChildAt(getChildCount() - 1);
            if (lastView == null) {
                return 0;
            }
            int lastPos = getPosition(lastView);
            //标注2.可见的最后一个itemView完全滑进来了，需要补充新的
            if (lastView.getRight() < getWidth()) {
                View scrap = null;
                //标注3.判断可见的最后一个itemView的索引，
                // 如果是最后一个，则将下一个itemView设置为第一个，否则设置为当前索引的下一个
                if (lastPos == getItemCount() - 1) {
                    if (looperEnable) {
                        scrap = recycler.getViewForPosition(0);
                    } else {
                        dx = 0;
                    }
                } else {
                    scrap = recycler.getViewForPosition(lastPos + 1);
                }
                if (scrap == null) {
                    return dx;
                }

                scrap = getSettedView(scrap);
                //标注4.将新的itemViewadd进来并对其测量和布局
                addView(scrap);
                measureChildWithMargins(scrap, 0, 0);
                int width  = getDecoratedMeasuredWidth(scrap);
                int height = getDecoratedMeasuredHeight(scrap);
                layoutDecorated(scrap, lastView.getRight() + 2 * margenDistance, 0, lastView.getRight() + width + 2 * margenDistance, height);
                return dx;
            }
        } else {
            //向右滚动
            View firstView = getChildAt(0);
            if (firstView == null) {
                return 0;
            }
            int firstPos = getPosition(firstView);

            if (firstView.getLeft() >= 0) {
                View scrap = null;
                if (firstPos == 0) {
                    if (looperEnable) {
                        scrap = recycler.getViewForPosition(getItemCount() - 1);
                    } else {
                        dx = 0;
                    }
                } else {
                    scrap = recycler.getViewForPosition(firstPos - 1);
                }
                if (scrap == null) {
                    return 0;
                }
                scrap = getSettedView(scrap);
                addView(scrap, 0);
                measureChildWithMargins(scrap, 0, 0);
                int width  = getDecoratedMeasuredWidth(scrap);
                int height = getDecoratedMeasuredHeight(scrap);
                layoutDecorated(scrap, firstView.getLeft() - width - 2 * margenDistance, 0, firstView.getLeft() - 2 * margenDistance, height);
            }
        }
        return dx;
    }

    /**
     * 回收界面不可见的view
     */
    private void recyclerHideView(int dx, RecyclerView.Recycler recycler, RecyclerView.State state) {
        for (int i = 0; i < getChildCount(); i++) {
            View view = getChildAt(i);
            if (view == null) {
                continue;
            }
            if (dx > 0) {
                //向左滚动，移除一个左边不在内容里的view
                if (view.getRight() < 0) {
                    removeAndRecycleView(view, recycler);
                    Log.d(TAG, "循环: 移除 一个view  childCount=" + getChildCount());
                }
            } else {
                //向右滚动，移除一个右边不在内容里的view
                if (view.getLeft() > getWidth()) {
                    removeAndRecycleView(view, recycler);
                    Log.d(TAG, "循环: 移除 一个view  childCount=" + getChildCount());
                }
            }
        }

    }
}

```

感谢 [传送门](https://www.jb51.net/article/162717.htm)
