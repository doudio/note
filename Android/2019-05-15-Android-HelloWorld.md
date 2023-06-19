> ## Android-HelloWorld

> #### LinearLayout :: 线性布局

> 设置宽高

| 属性                                 | 描述   |
| ------------------------------------ | ------ |
| android:layout_width="200dp"         | 设置宽 |
| android:layout_height="match_parent" | 设置高 |
| match_parent                         | 自适应 |
| android:layout_weight="1"            | 设置权重( 在父空间中剩下来的空间设置权重 ) |


> 设置边距

| 属性                               | 描述       |
| ---------------------------------- | ---------- |
| android:padding="50dp"             | 设置内边距 |
| android:paddingTop="50dp"          | 内上       |
| android:paddingBottom="50dp"       | 内下       |
| android:paddingLeft="50dp"         | 内左       |
| android:paddingRight="50dp"        | 内右       |
| android:layout_margin="50dp"       | 设置外边距 |
| android:layout_marginTop="50dp"    | 外上       |
| android:layout_marginBottom="50dp" | 外下       |
| android:layout_marginLeft="50dp"   | 外左       |
| android:layout_marginRight="50dp"  | 外右       |

> 设置容器内部属性

| 属性                             | 描述                                             |
| -------------------------------- | ------------------------------------------------ |
| android:orientation="horizontal" | 指定布局内控件排列方式为 水平排列 ( 默认情况为 ) |
| android:orientation="vertical"   | 指定布局内控件排列方式为 垂直排列                |
| android:gravity="center"         | 容器内部对齐方式                                 |

> RelativeLayout :: 相对布局

| 属性                                    | 描述                 |
| --------------------------------------- | -------------------- |
| android:layout_alignParentBottom="true" | 与父空间底部对齐     |
| android:layout_alignParentRight="true"  | 与父空间右边对齐     |
| android:layout_toRightOf="@id/v1"       | 相对到某个Id元素右边 |
| android:layout_below="@id/v1"           | 相对到某个Id元素下边 |

> #### 文字, 字体相关

| 属性                                     | 描述                 |
| ---------------------------------------- | -------------------- |
| android:textSize="25sp"                  | 设置文字大小         |
| android:textColor="#000"                 | 设置文字颜色         |
| android:maxLines="1"                     | 设置最大显示行       |
| android:ellipsize="end"                  | 设置省略符位置       |
| android:drawableRight="@drawable/tomark" | 设置文字右边存放图片 |

