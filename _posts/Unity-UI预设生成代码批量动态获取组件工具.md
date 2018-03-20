---
title: UI预设生成代码批量获取组件工具
author: liuaf
categories: Unity编辑器
tags:
     - Unity
     - Unity编辑器扩展
     - 工具
---

> 最近在写UI面板功能时发现每一个UI预设对应UI逻辑脚本在获取组件时需要很繁琐的代码__(通常为了解耦，UI预设上是不会挂载脚本的，只有通用的组件会挂载上去)__。而我们大部分逻辑是用lua来处理，虽然使用SublimeText,但补全功能始终不如VS中编辑C#那么友好。


<!--more-->


> 先看一下我们的需求

    self.input_account = self:GetChildCompByObj("input_account","UnityEngine.UI.InputField");
    self.input_password = self:GetChildCompByObj("input_password","UnityEngine.UI.InputField");
    self.btn_login = self:GetChildCompByObj("btn_login","UnityEngine.UI.Button");
    self.btn_register = self:GetChildCompByObj("btn_register","UnityEngine.UI.Button");


> 这是我的UI逻辑中在获取组件的一段代码，如果一个UI面板中存在大量很复杂的组件时想想就够恐怖的，维护起来也很麻烦，好在这种情况是有规律性的，对于程序猿来说能偷懒就偷懒。我要去做的一个工具就是去生成这一段代码。

> 再来看一下最后做出来的东西是什么样的
![预览图](/img/UIElementGenerate/Preview.jpg)

> 这个工具最重要的是左边那个树组件

```java

    private void ShowObject(UIElementTreeItem item)
    {
        GUILayout.BeginVertical();
        {
            GUILayout.BeginHorizontal();
            {
                GUILayout.Space(item.Depth * 20 + 5);
                item.IsOpen = Foldout(item, item.IsOpen);
            }
            GUILayout.EndHorizontal();
            if (item.IsOpen)
            {
                if (item.childs != null && item.childs.Count > 0)
                {
                    for (int i = 0; i < item.childs.Count; i++)
                    {
                        if (trees.ContainsKey(item.childs[i]))
                        {
                            UIElementTreeItem child = trees[item.childs[i]];
                            if (child != null)
                            {
                                ShowObject(child);
                            }
                        }
                    }
                }
            }
        }
        GUILayout.EndVertical();
    }

    /// <summary>
    /// 自定义折叠页
    /// 如果当前操作的组件没有子对象，则折叠功能取消，点击后相应选中或未选中状态
    /// </summary>
    /// <param name="item"> 操作的对象 </param>
    /// <param name="display"> 是否折叠 </param>
    /// <returns></returns>
    private bool Foldout(UIElementTreeItem item, bool display)
    {
        bool isFoldout = item.childs != null && item.childs.Count > 0;

        ///Box样式
        GUIStyle style = new GUIStyle("ShurikenModuleTitle");
        style.font = new GUIStyle(EditorStyles.label).font;
        style.border = new RectOffset(15, 7, 4, 4);
        style.fixedHeight = 22;
        style.contentOffset = new Vector2(isFoldout ? 35f : 20f, -2f);
        ///绘制box
        Rect rect = GUILayoutUtility.GetRect(16f, 22f, style);
        GUI.Box(rect, item.Name, style);

        if (isFoldout)
        {
            ///每一项是否能显示的Toggle
            Rect selectedRect = new Rect(rect.x + 18f, rect.y, 13f, 13f);
            item.IsGenerate = GUI.Toggle(selectedRect, item.IsGenerate, "");

            Event e = Event.current;
            Rect toggleRect = new Rect(rect.x + 4f, rect.y + 2f, 13f, 13f);
            if (e.type == EventType.Repaint)
            {
                EditorStyles.foldout.Draw(toggleRect, false, false, display, false);
            }

            if (e.type == EventType.MouseDown && rect.Contains(e.mousePosition))
            {
                display = !display;
                e.Use();
            }
        }
        else
        {
            Event e = Event.current;
            Rect toggleRect = new Rect(rect.x + 4f, rect.y, 13f, 13f);
            if (e.type == EventType.Repaint)
            {
                EditorStyles.toggle.Draw(toggleRect, false, false, item.IsGenerate, false);
            }

            if (e.type == EventType.MouseDown && rect.Contains(e.mousePosition))
            {
                item.IsGenerate = !item.IsGenerate;
                e.Use();
            }
        }
        return display;
    }
```

> 具体代码我可以到我的[Github](https://github.com/Aifee/UIElementGenerate.git)下载