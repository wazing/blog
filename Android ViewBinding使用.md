## 使用

添加支持： `app` ->`build.gradle`（需要Android Studio 3.6版本及以上，22年了不会还有人没升吧,.,.,.）

```xml
android {
    <!---省略其他配置--->
    buildFeatures {
        viewBinding true
    }
}
```

### Activity中使用

```kotlin
class MainActivity : AppCompatActivity() {

    // 布局文件名称为 activity_main.xml
    private lateinit var binding: ActivityMainBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        // 初始化viewBinding
        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        binding.textView.text = "hello viewBinding~"
    }
}
```



### include 标签写法

`activity_main.xml `中` </inlude> `标签不带 `id`

```xml
    <include
        layout="@layout/include_status" />
```

`include_status.xml`布局，此时根布局用`merge`是没有问题的，也可以嵌套个其他`ViewGroup`

```xml
<?xml version="1.0" encoding="utf-8"?>
<merge xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@android:color/white">

    <androidx.constraintlayout.widget.Group
        android:id="@+id/status_loading"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="@color/white"
        android:visibility="visible"
        app:constraint_referenced_ids="status_loading_msg,status_loading_progressbar" />

    <androidx.constraintlayout.widget.Group
        android:id="@+id/status_error"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="@color/white"
        android:visibility="gone"
        app:constraint_referenced_ids="status_error_msg" />

    <ProgressBar
        android:id="@+id/status_loading_progressbar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintHorizontal_chainStyle="packed"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toLeftOf="@id/status_loading_msg"
        app:layout_constraintTop_toTopOf="parent"
        tools:viewBindingIgnore="true" />

    <TextView
        android:id="@+id/status_loading_msg"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginStart="16dp"
        android:text="加载中，请稍后..."
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintHorizontal_chainStyle="packed"
        app:layout_constraintLeft_toRightOf="@id/status_loading_progressbar"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        tools:viewBindingIgnore="true" />

    <TextView
        android:id="@+id/status_error_msg"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="加载异常，请点击重新加载~"
        android:visibility="gone"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent"
        tools:viewBindingIgnore="true" />
</merge>
```

`MainActivity.kt` 中调用

```kotlin
    // 布局文件名称为 activity_main.xml
    private lateinit var binding: ActivityMainBinding
    // 布局文件名称为 include_status.xml
    private lateinit var includeStatusBinding: IncludeStatusBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        binding = ActivityMainBinding.inflate(layoutInflater)
        // activity_main.xml 中带有<include/>标签，include_status
        includeStatusBinding = IncludeStatusBinding.bind(binding.root)
        setContentView(binding.root)
        // activity_main.xml 布局中的控件用 binding 对象
        binding.textView.text = "hello viewBinding!"
        // include_status.xml 布局中的控件用 includeStatusBinding 对象
        includeStatusBinding.statusLoading.isVisible = true
        includeStatusBinding.statusError.isVisible = false
    }
```

另外说明下，如果 `activity_main.xml `中` </inlude> `标签带 `id` 会抛异常:

```xml
    <include
        android:id="@+id/include_status"
        layout="@layout/include_status" />

Caused by: java.lang.NullPointerException: Missing required view with ID: com.wazing.viewbinding.simple:id/include_status
	......
```

此时就需要用另一种写法了，布局同上

```xml
    <include
        android:id="@+id/include_status"
        layout="@layout/include_status" />
```

不同的是在`MainActivity.kt`中直接调用就行

```kotlin
    // 布局文件名称为 activity_main.xml
    private lateinit var binding: ActivityMainBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)
        // activity_main.xml 中的控件用 binding 对象
        binding.textView.text = "hello viewBinding!"
        // 但此时 include_status.xml 布局最外层不能使用merge
        binding.includeStatus.statusLoading.isVisible = true
        binding.includeStatus.statusError.isVisible = false
    }
```

需要注意的是，`include_status.xml`布局不能再使用 `merge` 了，嵌套一层 `ConstraintLayout`/ `LinearLayout` 等即可，`include_status.xml`布局就不贴了。

### Fragment中使用

基本同Activity中使用一样，需要注意的下面代码中说明了

```kotlin
class HomeFragment : Fragment() {

    private var _binding: FragmentHomeBinding? = null
    private val binding: FragmentHomeBinding get() = _binding!!

    override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?
    ): View? {
        // 初始化 viewBinding
        _binding = FragmentHomeBinding.inflate(inflater, container, false)
        return binding.root
    }

    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        super.onViewCreated(view, savedInstanceState)
        binding.textView.text = "home fragment\nhello viewBinding~"
    }

    override fun onDestroyView() {
        super.onDestroyView()
        // 防止内存泄漏
        _binding = null
    }
}
```

### RecyclerView.ViewHolder中使用

使用方式大同小异，代码中都有注释，直接看代码就好了

`MainAdapter.kt`

```kotlin
class MainAdapter : RecyclerView.Adapter<MainAdapter.MainViewHolder>() {

    private val dataSource: ArrayList<String> = arrayListOf()

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): MainViewHolder {
        // 初始化ItemMainBinding，对应 item_main.xml
        val binding = ItemMainBinding.inflate(LayoutInflater.from(parent.context), parent, false)
        return MainViewHolder(binding)
    }

    override fun onBindViewHolder(holder: MainViewHolder, position: Int) {
        val item = dataSource[position]
        holder.bindData(item)
    }

    override fun getItemCount(): Int = dataSource.size

    /**
     * 添加数据
     */
    fun addData(data: List<String>) {
        dataSource.addAll(data)
        notifyItemRangeInserted(itemCount, data.size)
    }

    inner class MainViewHolder(
        private val binding: ItemMainBinding
    ) : RecyclerView.ViewHolder(binding.root) {
        fun bindData(str: String) {
            binding.itemText.text = str
        }
    }
}
```

`MainActivity.kt`

```kotlin
    // 布局文件名称为 activity_main.xml
    private lateinit var binding: ActivityMainBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        binding = ActivityMainBinding.inflate(layoutInflater)
        setContentView(binding.root)

        binding.recyclerview.adapter = MainAdapter().apply {
            val data = arrayListOf<String>()
            val itemCount = this.itemCount
            for (i in itemCount until itemCount + 30) {
                data.add("data::$i")
            }
            addData(data)
        }
    }
```

布局文件

```xml
# activity_main.xml
    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/recyclerview"
        android:layout_width="match_parent"
        android:layout_height="300dp"
        app:layoutManager="androidx.recyclerview.widget.LinearLayoutManager"
        app:layout_constraintTop_toBottomOf="@id/textView" />

# item_main.xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="40dp"
    android:gravity="center"
    android:orientation="vertical">

    <TextView
        android:id="@+id/item_text"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        tools:text="@string/app_name" />

</LinearLayout>
```

### 忽略ViewBinding生成绑定类

在不需要生成绑定类的根布局加上`tools:viewBindingIgnore="true"`，控件没有`android:id`同样也不会生成绑定。

## 封装

### Activity 中封装

#### 依托于基类

方案一：

```kotlin
abstract class BaseActivity<VB : ViewBinding> : AppCompatActivity() {

    lateinit var binding: VB

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = getViewBinding()
        setContentView(binding.root)
    }

    abstract fun getViewBinding(): VB
}
```

方案二：

```kotlin
abstract class BaseActivity<VB : ViewBinding> : AppCompatActivity() {

    fun AppCompatActivity.viewBinding(inflater: (LayoutInflater) -> VB) = lazy {
        inflater(layoutInflater).apply { setContentView(root) }
    }
}

class TwoActivity : BaseActivity<ActivityMainBinding>() {

    /*private val binding by viewBinding {
        ActivityMainBinding.inflate(it)
    }*/
    private val binding by viewBinding(ActivityMainBinding::inflate)

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        binding.textView.text = "two\nhello viewBinding~"
    }
}
```

两种方案都差不多，方案一通过`getViewBinding()`抽象方法让基类去初始化`viewBinding`，方案二是利用`kotlin`可以传递函数特性，把`ViewBinding.inflate`的函数传递过去，让函数进行实例化，并利用`apply`高阶函数执行`Activity.setContentView()`加载布局，最终返回`ViewBinding`实例。

#### 反射方式

```kotlin
abstract class BaseActivity<VB : ViewBinding> : AppCompatActivity() {

    open val binding: VB by lazy {
        val parameterizedType = javaClass.genericSuperclass as ParameterizedType
        val clz = parameterizedType.actualTypeArguments[0] as Class<*>
        val method = clz.getDeclaredMethod("inflate", LayoutInflater::class.java)
        return@lazy method.invoke(null, layoutInflater) as VB
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(binding.root)
    }
}
```

viewBinding的初始化并不太麻烦，不建议用反射方式~

### Fragment 中封装

方案一：

```kotlin
abstract class BaseFragment<VB : ViewBinding> : Fragment() {

    private var _binding: VB? = null
    val binding: VB get() = _binding!!

    override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?
    ): View? {
        _binding = getViewBinding()
        return binding.root
    }

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }

    abstract fun getViewBinding(): VB
}
```

方案二：

```kotlin
abstract class BaseFragment<VB : ViewBinding> : Fragment() {

    private var _binding: VB? = null
    open val binding: VB get() = _binding!!

    override fun onCreateView(
        inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?
    ): View? {
        val parameterizedType = javaClass.genericSuperclass as ParameterizedType
        val clz = parameterizedType.actualTypeArguments[0] as Class<*>
        val method = clz.getDeclaredMethod(
            "inflate",
            LayoutInflater::class.java,
            ViewGroup::class.java,
            Boolean::class.java
        )
        _binding = method.invoke(null, layoutInflater, container, false) as VB
        return binding.root
    }

    override fun onDestroyView() {
        super.onDestroyView()
        _binding = null
    }
}
```

viewBinding的初始化并不太麻烦，不建议用反射方式~

### ViewHolder 中封装

后续补充...
