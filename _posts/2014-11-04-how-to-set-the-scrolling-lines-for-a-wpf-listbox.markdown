---
layout:     post
title:      "How to set the scrolling lines for a WPF ListBox?"
date:       2014-11-04 22:54:02 +0100
comments:   true
categories: [Develop,Tips&Tricks]
tags:       [WPF,Xaml,C#]
---

The default behavior for scrolling with the mouse wheel on a `ListBox` or `ListView` scrolls the entire view 3 lines up or down. That's for the most scenarios ok, but sometimes it could be necessary to scroll only 1 or n lines.
But there is no property to set this and the `ScrollViewer` can not be accessed.

So what now?

A simple trick to enable such behavior is to create an attached property to this.

```csharp
public static readonly DependencyProperty ScrollingLinesProperty =
  DependencyProperty.RegisterAttached(
    "ScrollingLines",
    typeof(int),
    typeof(SelectorHelper),
    new UIPropertyMetadata(3,
                           OnScrollingLinesPropertyChangedCallback, 
                           (o, value) => (int)value <= 0 ? 1 : value));
```

With this property it's now possible to set the scrolling lines on a `ListBox` or `ListView`.

```xml
<ListBox local:SelectorHelper.ScrollingLines="1"
         ScrollViewer.CanContentScroll="True"
         ItemsSource="..." />
```

> This works only with `ScrollViewer.CanContentScroll="True"`!

The scrolling *magic* happens at the mouse wheel event.

```csharp
var scrollingLines = GetScrollingLines(selector);
for (var i = 0; i < scrollingLines; i++)
{
  if (e.Delta < 0)
  {
    lbScrollViewer.LineDown();
  }
  else
  {
    lbScrollViewer.LineUp();
  }
}
```

The code uses an extension method to get the `ScrollViewer`, because the `ListBox` and the `ListView` doesn't have such a nice proeprty.

```csharp
var selector = (Selector)sender;
// get the scrollviewer
SetScrollViewer(selector,
                selector.GetDescendantByType(typeof(ScrollViewer)) as ScrollViewer);
```

```csharp
public static class VisualExtensions
{
  /// <summary>
  /// Gets a descendant by type at the given visual.
  /// </summary>
  /// <param name="visual">The visual that contains the descendant.</param>
  /// <param name="type">The type to search for the descendant.</param>
  /// <returns>Returns the searched descendant or null if nothing was found.</returns>
  public static Visual GetDescendantByType(this Visual visual, Type type)
  {
    if (visual == null)
    {
      return null;
    }

    if (visual.GetType() == type)
    {
      return visual;
    }

    // sometimes it's necessary to apply a template before getting the childrens
    var frameworkElement = visual as FrameworkElement;
    if (frameworkElement != null)
    {
      frameworkElement.ApplyTemplate();
    }

    Visual foundElement = null;
    for (var i = 0; i < VisualTreeHelper.GetChildrenCount(visual); i++)
    {
      var childVisual = VisualTreeHelper.GetChild(visual, i) as Visual;
      foundElement = GetDescendantByType(childVisual, type);
      if (foundElement != null)
      {
        break;
      }
    }
    return foundElement;
  }
}
```

Here is the complete `SelectorHelper` code.

```csharp
using System.Windows;
using System.Windows.Controls;
using System.Windows.Controls.Primitives;
using System.Windows.Input;

namespace SimpleMusicPlayer.Base
{
  public static class SelectorHelper
  {
    public static readonly DependencyProperty ScrollingLinesProperty =
      DependencyProperty.RegisterAttached("ScrollingLines",
                                          typeof(int),
                                          typeof(SelectorHelper),
                                          new UIPropertyMetadata(3,
                                                                 OnScrollingLinesPropertyChangedCallback,
                                                                 (o, value) => (int)value <= 0 ? 1 : value));

    /// <summary>
    /// Gets or Sets the value how much lines (items) should be scrolled.
    /// </summary>
    [AttachedPropertyBrowsableForType(typeof(ListBox))]
    public static int GetScrollingLines(Selector source)
    {
      return (int)source.GetValue(ScrollingLinesProperty);
    }

    public static void SetScrollingLines(Selector source, int value)
    {
      source.SetValue(ScrollingLinesProperty, value);
    }

    private static readonly DependencyProperty ScrollViewerProperty =
      DependencyProperty.RegisterAttached("ScrollViewer",
                                          typeof(ScrollViewer),
                                          typeof(SelectorHelper),
                                          new UIPropertyMetadata(null));

    private static ScrollViewer GetScrollViewer(DependencyObject source)
    {
      return (ScrollViewer)source.GetValue(ScrollViewerProperty);
    }

    private static void SetScrollViewer(DependencyObject source, ScrollViewer value)
    {
      source.SetValue(ScrollViewerProperty, value);
    }

    private static void OnScrollingLinesPropertyChangedCallback(DependencyObject dependencyObject, DependencyPropertyChangedEventArgs e)
    {
      var selector = (Selector)dependencyObject;
      if (e.NewValue != e.OldValue && e.NewValue != null)
      {
        selector.Loaded -= OnSelectorLoaded;
        selector.Loaded += OnSelectorLoaded;
      }
    }

    private static void OnSelectorLoaded(object sender, RoutedEventArgs routedEventArgs)
    {
      var selector = (Selector)sender;
      // get or store scrollviewer
      SetScrollViewer(selector, selector.GetDescendantByType(typeof(ScrollViewer)) as ScrollViewer);
      selector.PreviewMouseWheel -= OnSelectorPreviewMouseWheel;
      selector.PreviewMouseWheel += OnSelectorPreviewMouseWheel;
    }

    private static void OnSelectorPreviewMouseWheel(object sender, MouseWheelEventArgs e)
    {
      if (e.Delta == 0)
      {
        // nothing to do
        return;
      }

      var selector = (Selector)sender;
      // get or stored scrollviewer
      var lbScrollViewer = GetScrollViewer(selector);
      if (lbScrollViewer != null)
      {
        var scrollingLines = GetScrollingLines(selector);
        for (var i = 0; i < scrollingLines; i++)
        {
          if (e.Delta < 0)
          {
            lbScrollViewer.LineDown();
          }
          else
          {
            lbScrollViewer.LineUp();
          }
        }
        e.Handled = true;
      }
    }
  }
}
```
