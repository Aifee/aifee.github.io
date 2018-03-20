---
title: Unity中常用的Attribute标签
author: liuaf
toc: true
categories: Unity编辑器
tags:
     - Unity
     - Unity编辑器扩展
     - Attribute
---


* [CustomEditor](#CustomEditor)
<!--more-->
* [ExecuteInEditMode](#ExecuteInEditMode)
* [MenuItem](#MenuItem)
* [AddComponentMenu](#AddComponentMenu)
* [ContextMenu](#ContextMenu)
* [ContextMenuItem](#ContextMenuItem)
* [Multiline & TextArea](#Multiline & TextArea)
* [CreateAssetMenu & PreferBinarySerialization](#CreateAssetMenu & PreferBinarySerialization)
* [DisallowMultipleComponent](#DisallowMultipleComponent)
* [Header](#Header)
* [HelpURL](#HelpURL)
* [HideInInspector](#HideInInspector)
* [Range](#Range)
* [RequireComponent](#RequireComponent)
* [RuntimeInitializeOnLoadMethod](#RuntimeInitializeOnLoadMethod)
* [SelectionBase](#SelectionBase)
* [SerializeField](#SerializeField)
* [Space](#Space)
* [Tooltip](#Tooltip)
* [PreferenceItem](#PreferenceItem)

<h4 id="CustomEditor">CustomEditor</h4>
```java
/// <summary>
/// 要自定义编辑的脚本
/// </summary>
[CustomEditor(typeof(PlugExtend))]
public class PlugExtendEditor : Editor
{
    public override void OnInspectorGUI()
    {
        PlugExtend extend = target as PlugExtend;
        Vector2 vec = new Vector2(extend.min, extend.max);
        vec = EditorGUILayout.Vector2Field("Min And Max", vec);
    }
}

public class PlugExtend : MonoBehaviour
{
    public float min;
    public float max;
}
```
![效果图](/img/UnityEditorOptions/CustomEditor.png)

<h4 id="ExecuteInEditMode">ExecuteInEditMode</h4>
```java
/// <summary>
/// 编辑器模式行执行
/// </summary>
[ExecuteInEditMode]
public class PlugExtend : MonoBehaviour
{
    /// <summary>
    /// 挂载这个脚本后编辑器状态下也能够执行这个方法
    /// </summary>
    private void Awake()
    {
        Debug.Log("PlugExtend Awake function");
    }
}
```

<h4 id="MenuItem">MenuItem</h4>
```java
public class PlugExtendEditor : EditorWindow
{
    /// <summary>
    /// 在顶部菜单Tools/OpenWindow菜单
    /// %#&u 快捷键 Ctrl + Shift + Alt + U
    /// false 验证函数为false的情况下不激活
    /// 11 菜单中的显示顺序权重
    /// </summary>
    [MenuItem("Tools/OpenWindow  %#&u", false, 11)]
    public static void Open()
    {
        PlugExtendEditor window = EditorWindow.CreateInstance<PlugExtendEditor>();
        window.Show();
    }
    /// <summary>
    /// 验证函数
    /// false 的情况下Tools/OpenWindow菜单无效
    /// true 的情况下Tools/OpenWindow菜单激活
    /// </summary>
    /// <returns></returns>
    [MenuItem("Tools/OpenWindow %#&u", true, 11)]
    public static bool ValidateOpen()
    {
        bool validate = false;
        //验证是否打开窗口条件
        return validate;
    }
}

public class PlugExtend : MonoBehaviour
{
    /// <summary>
    /// 在齿轮菜单中添加一个菜单项
    /// CONTEXT 固定写法
    /// Transform 脚本所挂载的对象上的组件
    /// setting 菜单名
    /// </summary>
    [MenuItem("CONTEXT/Transform/setting")]
    private static void Setting()
    {
        Debug.Log("this is setting");
    }
}

```

<h4 id="AddComponentMenu">AddComponentMenu</h4>
```java
/// <summary>
/// 在Inspector面板中最下面有个 [Add Component] 按钮，点击后的菜单。
/// </summary>
[AddComponentMenu("Test/PlugExtend")]
public class PlugExtend : MonoBehaviour
{
    /// <summary>
    /// 挂载这个脚本后编辑器状态下也能够执行这个方法
    /// </summary>
    private void Awake()
    {
        Debug.Log("PlugExtend Awake function");
    }
}
```
![效果图](/img/UnityEditorOptions/AddComponentMenu.png)

<h4 id="ContextMenu">ContextMenu</h4>
> 直接上代码上图
```java
[ContextMenu("DoSomething")]
public void DoSomething()
{
    //响应时做某种事情
}
```
![效果图](/img/UnityEditorOptions/ContextMenu.png)


<h4 id="ContextMenuItem">ContextMenuItem</h4>
```java
/// <summary>
/// 属性上右键菜单响应所调用的函数
/// </summary>
[ContextMenuItem("Reset", "ResetOption")]
public string value = "";
void ResetOption()
{
    value = "liuaf";
    Debug.Log(value);
}
```
![效果图](/img/UnityEditorOptions/ContextMenuItem.png)

<h4 id="Multiline & TextArea">Multiline & TextArea</h4>
```java
[Multiline(8)]
public string value;
[TextArea(1, 5)]
public string value1;
```
![效果图](/img/UnityEditorOptions/Multiline_TextArea.png)

<h4 id="ColorUsage">ColorUsage</h4>
``` java
public Color color1;
/// <summary>
/// 没有alpha
/// </summary>
[ColorUsage(false)]
public Color color2;
/// <summary>
/// showAlpha           如果为false，则alpha通道信息。
/// HDR                 true视为HDR颜色（默认值：false）。
/// minBrightness       使用HDR拾色器时的最小允许HDR色彩分量值（默认值：0）。
/// maxBrightness       使用HDR拾色器时的最大允许HDR色彩分量值（默认值：8）。
/// minExposureValue    HDR颜色选择器允许的最小曝光值（默认值：1/8 = 0.125）。
/// maxExposureValue    HDR颜色选择器允许的最大曝光值（默认值：3）。
/// </summary>
[ColorUsage(true, true, 0, 8, 0.125f, 3)]
public Color color3;
````

<h4 id="CreateAssetMenu & PreferBinarySerialization">CreateAssetMenu & PreferBinarySerialization</h4>
``` java
/// <summary>
/// fileName 创建的资源名
/// menuName create菜单中显示的菜单名
/// order 顺序
/// PreferBinarySerialization 使用二进制序列化
/// </summary>
[CreateAssetMenu( fileName = "customData.asset", menuName ="CustomData", order =10)]
[PreferBinarySerialization]
public class CustomData : ScriptableObject
{
}
```
![效果图](/img/UnityEditorOptions/CreateAssetMenu.png)

<h4 id="DisallowMultipleComponent">DisallowMultipleComponent</h4>
``` java
/// <summary>
/// 不允许同一个GameObject 挂载多个此脚本
/// 继承此脚本的对象也不可以存在多个
/// </summary>
[DisallowMultipleComponent]
public class NewBehaviourScript : MonoBehaviour
{    
}
```

<h4 id="Header">Header</h4>
#### Header
```java
/// <summary>
/// 在属性面板中的字段添加标题
/// </summary>
[Header("Health Settings")]
public int health = 0;
public int maxHealth = 100;
[Header("Shield Settings")]
public int shield = 0;
public int maxShield = 0;
```
![效果图](/img/UnityEditorOptions/Header.png)

<h4 id="HelpURL">HelpURL</h4>
``` java
/// <summary>
/// 帮助文档url
/// </summary>
[HelpURL("https://aifee.github.io")]
public class NewBehaviourScript : MonoBehaviour
{
}
```

<h4 id="HideInInspector">HideInInspector</h4>
``` java
/// <summary>
/// 属性面板中隐藏此属性的编辑
/// </summary>
[HideInInspector]
public int p = 5;
```

<h4 id="Range">Range</h4>
``` java
public class NewBehaviourScript : MonoBehaviour
{
    /// <summary>
    /// 限制valueInt值在 0 - 10 范围内
    /// </summary>
    [Range(0,10)]
    public int valueInt;
    /// <summary>
    /// 限制valueFloat值在 0 - 1 范围内
    /// </summary>
    [Range(0,1)]
    public float valueFloat;
}
```

<h4 id="RequireComponent">RequireComponent</h4>
``` java
/// <summary>
/// 自动关联依赖的组件，如果没有则为这个GameObject 添加这个依赖组件
/// </summary>
[RequireComponent(typeof(Image))]
public class NewBehaviourScript : MonoBehaviour
{
}
```

<h4 id="RuntimeInitializeOnLoadMethod">RuntimeInitializeOnLoadMethod</h4>
``` java
/// <summary>
/// 游戏加载运行后不需要继承MonoBehaviour也可以响应函数
/// 注意：能够确定的是在Awake函数后调用
/// 但多个这个标记时无法保证执行顺序
/// </summary>
public class NewBehaviourScript
{
    [RuntimeInitializeOnLoadMethod]
    static void OnRuntimeMethodLoad()
    {
        Debug.Log("After scene is loaded and game is running");
    }

    [RuntimeInitializeOnLoadMethod]
    static void OnSecondRuntimeMethodLoad()
    {
        Debug.Log("SecondMethod After scene is loaded and game is running.");
    }
}
```

<h4 id="SelectionBase">SelectionBase</h4>
``` java
/// <summary>
/// 挂载这个脚本的对象在Scene界面选择对象时
/// 如果点中它的子对象时，会默认选择这个对象
/// 是个不错的功能，尤其场景中对象很多很乱的时候
/// </summary>
[SelectionBase]
public class NewBehaviourScript : MonoBehaviour
{
}
```

<h4 id="SerializeField">SerializeField</h4>
``` java
public class NewBehaviourScript : MonoBehaviour
{
    /// <summary>
    /// private 修饰的属性，在属性面板中依然可以编辑
    /// </summary>
    [SerializeField]
    private bool hasHealthPotion = true;
}
```

<h4 id="Space">Space</h4>
``` java
public class NewBehaviourScript : MonoBehaviour
{
    public int health = 0;
    public int maxHealth = 100;
    /// <summary>
    /// 间距像素，同 GUILayout.Space(50) 用法相似
    /// </summary>
    [Space(50)]
    public int shield = 0;
    public int maxShield = 0;
}
```
![效果图](/img/UnityEditorOptions/Space.png)

<h4 id="Tooltip">Tooltip</h4>
``` java
public class NewBehaviourScript : MonoBehaviour
{
    /// <summary>
    /// 为字段添加提示，鼠标获取焦点后显示
    /// </summary>
    [Tooltip("Health value between 0 and 100.")]
    public int health = 0;
}
```
![效果图](/img/UnityEditorOptions/tooltip.png)

<h4 id="PreferenceItem">PreferenceItem</h4>
``` java
/// <summary>
/// 官方的案例
/// 可以定制 Edit / Preferences 面板
/// </summary>
public class NewBehaviourScript : MonoBehaviour
{
    // Have we loaded the prefs yet
    private static bool prefsLoaded = false;
    // The Preferences
    public static bool boolPreference = false;
    // Add preferences section named "My Preferences" to the Preferences Window
    [PreferenceItem("My Preferences")]
    public static void PreferencesGUI()
    {
        // Load the preferences
        if (!prefsLoaded)
        {
            boolPreference = EditorPrefs.GetBool("BoolPreferenceKey", false);
            prefsLoaded = true;
        }
        // Preferences GUI
        boolPreference = EditorGUILayout.Toggle("Bool Preference", boolPreference);
        // Save the preferences
        if (GUI.changed)
            EditorPrefs.SetBool("BoolPreferenceKey", boolPreference);
    }
}
```
![效果图](/img/UnityEditorOptions/Preferences.png)