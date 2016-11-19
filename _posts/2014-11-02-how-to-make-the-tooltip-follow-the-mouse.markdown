---
layout:     post
title:      "How to make the ToolTip follow the mouse?"
date:       2014-11-02 14:22:31 +0100
comments:   true
categories: [Develop,Tips&Tricks]
tags:       [WPF,Xaml,C#]
---

In a project of mine I needed a `ToolTip`. That's easy, I know, but I want a `ToolTip` that follows the mouse and not the default behavior. Normally a `ToolTip` opens and stays at this position on the target element. After leaving the element the `ToolTip` closes.

So here is my solution after some searching at the www.

### The simplest way

The simplest way is to use the `ToolTip` itself and not using a `Popup` (found this as answer at SO).

```xml
<Button MouseMove="Button_MouseMove"
        Content="Test Button 1"
        Padding="5"
        Margin="5">
    <Button.ToolTip>
        <ToolTip x:Name="tt"
                 Content="Solution for a ToolTip..." />
    </Button.ToolTip>
</Button>
```

Now do the moving *magic* at the mouse move event.

```csharp
private void Button_MouseMove(object sender, MouseEventArgs e)
{
    tt.Placement = System.Windows.Controls.Primitives.PlacementMode.Relative;
    tt.HorizontalOffset = e.GetPosition((IInputElement)sender).X + 16;
    tt.VerticalOffset = e.GetPosition((IInputElement)sender).Y + 16;
}
```

That's it.

![button with tooltip]({{ site.url }}/images/button_tooltip.png)

### The 'behavior' way

The simple way is for a short effort ok, but doing this for any element that uses a `ToolTip` is not the way I want.

So I decided to create an attached property to enable/disable the movement.

```csharp
public static readonly DependencyProperty AutoMoveProperty =
  DependencyProperty.RegisterAttached("AutoMove",
                                      typeof(bool),
                                      typeof(ToolTipHelper),
                                      new FrameworkPropertyMetadata(false, AutoMovePropertyChangedCallback));
```

Now you can use this property on your `ToolTip` in a simple way.

```xml
<Button Content="Test Button 2"
        Padding="5"
        Margin="5">
    <Button.ToolTip>
        <ToolTip local:ToolTipHelper.AutoMove="True"
                 Content="Awesome solution for a ToolTip..." />
    </Button.ToolTip>
</Button>
```

A complete example (incl. the `ToolTipHelper` class) can be found on [GitHub](https://github.com/punker76/code-samples/tree/master/ToolTipAutoMoveSample).

Here is the code for the `ToolTipHelper`.

```csharp
public static class ToolTipHelper
{
    public static readonly DependencyProperty AutoMoveProperty =
      DependencyProperty.RegisterAttached("AutoMove",
                                          typeof(bool),
                                          typeof(ToolTipHelper),
                                          new FrameworkPropertyMetadata(false, AutoMovePropertyChangedCallback));

    /// <summary>
    /// Enables a ToolTip to follow the mouse cursor.
    /// When set to <c>true</c>, the tool tip follows the mouse cursor.
    /// </summary>
    [AttachedPropertyBrowsableForType(typeof(ToolTip))]
    public static bool GetAutoMove(ToolTip element)
    {
        return (bool)element.GetValue(AutoMoveProperty);
    }

    public static void SetAutoMove(ToolTip element, bool value)
    {
        element.SetValue(AutoMoveProperty, value);
    }

    public static readonly DependencyProperty AutoMoveHorizontalOffsetProperty =
      DependencyProperty.RegisterAttached("AutoMoveHorizontalOffset",
                                          typeof(double),
                                          typeof(ToolTipHelper),
                                          new FrameworkPropertyMetadata(16d));

    [AttachedPropertyBrowsableForType(typeof(ToolTip))]
    public static double GetAutoMoveHorizontalOffset(ToolTip element)
    {
        return (double)element.GetValue(AutoMoveHorizontalOffsetProperty);
    }

    public static void SetAutoMoveHorizontalOffset(ToolTip element, double value)
    {
        element.SetValue(AutoMoveHorizontalOffsetProperty, value);
    }

    public static readonly DependencyProperty AutoMoveVerticalOffsetProperty =
      DependencyProperty.RegisterAttached("AutoMoveVerticalOffset",
                                          typeof(double),
                                          typeof(ToolTipHelper),
                                          new FrameworkPropertyMetadata(16d));

    [AttachedPropertyBrowsableForType(typeof(ToolTip))]
    public static double GetAutoMoveVerticalOffset(ToolTip element)
    {
        return (double)element.GetValue(AutoMoveVerticalOffsetProperty);
    }

    public static void SetAutoMoveVerticalOffset(ToolTip element, double value)
    {
        element.SetValue(AutoMoveVerticalOffsetProperty, value);
    }

    private static void AutoMovePropertyChangedCallback(DependencyObject dependencyObject, DependencyPropertyChangedEventArgs eventArgs)
    {
        var toolTip = (ToolTip)dependencyObject;
        if (eventArgs.OldValue != eventArgs.NewValue && eventArgs.NewValue != null)
        {
            var autoMove = (bool)eventArgs.NewValue;
            if (autoMove)
            {
                toolTip.Opened += ToolTip_Opened;
                toolTip.Closed += ToolTip_Closed;
            }
            else
            {
                toolTip.Opened -= ToolTip_Opened;
                toolTip.Closed -= ToolTip_Closed;
            }
        }
    }

    private static void ToolTip_Opened(object sender, RoutedEventArgs e)
    {
        var toolTip = (ToolTip)sender;
        var target = toolTip.PlacementTarget as FrameworkElement;
        if (target != null)
        {
            // move the tooltip on openeing to the correct position
            MoveToolTip(target, toolTip);
            target.MouseMove += ToolTipTargetPreviewMouseMove;
            Debug.WriteLine(">>tool tip opened");
        }
    }

    private static void ToolTip_Closed(object sender, RoutedEventArgs e)
    {
        var toolTip = (ToolTip)sender;
        var target = toolTip.PlacementTarget as FrameworkElement;
        if (target != null)
        {
            target.MouseMove -= ToolTipTargetPreviewMouseMove;
            Debug.WriteLine(">>tool tip closed");
        }
    }

    private static void ToolTipTargetPreviewMouseMove(object sender, MouseEventArgs e)
    {
        var target = sender as FrameworkElement;
        var toolTip = (target != null ? target.ToolTip : null) as ToolTip;
        MoveToolTip(sender as IInputElement, toolTip);
    }

    private static void MoveToolTip(IInputElement target, ToolTip toolTip)
    {
        if (toolTip == null || target == null)
        {
            return;
        }
        toolTip.Placement = System.Windows.Controls.Primitives.PlacementMode.Relative;
        var hOffset = GetAutoMoveHorizontalOffset(toolTip);
        var vOffset = GetAutoMoveVerticalOffset(toolTip);
        toolTip.HorizontalOffset = Mouse.GetPosition(target).X + hOffset;
        toolTip.VerticalOffset = Mouse.GetPosition(target).Y + vOffset;
        Debug.WriteLine(">>ho {0:.2f} >> vo {1:.2f}", toolTip.HorizontalOffset, toolTip.VerticalOffset);
    }
}
```
