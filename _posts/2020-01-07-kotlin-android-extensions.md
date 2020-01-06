---
layout: post
title:  "Kotlin Android Extensions"
comments: true
date:   2020-01-07 07:13:00
---


여기서는 확장된 기능 중에 View binding 에 대해서 이야기합니다.

[Kotlin Android Extensions](https://kotlinlang.org/docs/tutorials/android-plugin.html)


## View binding

안드로이드 개발에서 가능 흔한 [boilerplate code](https://en.wikipedia.org/wiki/Boilerplate_code) 는 `findViewById`일 것이다.

그래서 그동안 많은 개발자는 [Butter Knife](https://jakewharton.github.io/butterknife/)류의 [di(dependency injection)](https://en.wikipedia.org/wiki/Dependency_injection) 라이브러리를 많이 사용해 왔다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<!-- activity_main.xml -->
<RelativeLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <TextView
        android:id="@+id/textView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        />
</RelativeLayout>
```
```kotlin
// 보통의 안드로이드 : 우리는 보통 이런 식으로 해 왔다.
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)
    val textView = findViewById<TextView>(R.id.textView)
}
```
```kotlin
// butter knife : 뭔가 더 편하다는 느낌적인 느낌이 들었지만 과연 그럴까...
@BindView(R.id.textView) lateinit var textView: TextView
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)
    ButterKnife.bind(this)
}
```

이제는 이런 게 된다.
```kotlin
import kotlinx.android.synthetic.main.activity_main.* // IDE 가 알아서 포함해 준다.
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    setContentView(R.layout.activity_main)
    textView.text = "this is kotlin android extensions"
}
```

## 내부 구현

android studio 에서 `Tools > Kotlin > Show Kotlin Bytecode` 를 통해 decompile 된 자바 코드를 보면,

다음과 같은 코드를 볼 수 있다.

```java
private HashMap _$_findViewCache; // 이녀석이 캐시

protected void onCreate(@Nullable Bundle savedInstanceState) {
  super.onCreate(savedInstanceState);
  this.setContentView(-1300078);
  TextView var10000 = (TextView)this._$_findCachedViewById(id.textView);
  var10000.setText((CharSequence)"this is kotlin android extensions");
}

public View _$_findCachedViewById(int var1) {
  if (this._$_findViewCache == null) {
     this._$_findViewCache = new HashMap();
  }

  View var2 = (View)this._$_findViewCache.get(var1);
  if (var2 == null) {
     var2 = this.findViewById(var1);
     this._$_findViewCache.put(var1, var2);
  }

  return var2;
}

public void _$_clearFindViewByIdCache() {
  if (this._$_findViewCache != null) {
     this._$_findViewCache.clear();
  }
}
```

결국 `findViewById`를 호출하는 코드로 구현되어 있다.

그리고 여러번 호출되는 경우 성능을 위해서 HashMap 에 캐싱하는 코드까지 포함되어 있다.

이녀석은 `Activity`뿐 아니라 `Fragment`에도 같은 방식으로 동작한다.

(참고로 `View`는 캐싱하는 기능은 들어있지 않다.)


코틀린을 사용하자 ㅡ
