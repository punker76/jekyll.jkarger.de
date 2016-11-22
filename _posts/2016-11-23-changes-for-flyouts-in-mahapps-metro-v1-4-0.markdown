---
layout:     post
title:      "Changes for Flyouts in MahApps.Metro v1.4.0"
date:       2016-11-23 00:02:00 +0100
comments:   true
categories: [Develop,MahApps.Metro]
tags:       [WPF,Xaml,C#,UI,MahApps.Metro]
---

First, this upcoming changes affects only users who used the sample from the main demo source which shows how to use the `FlyoutsControl` actual width and a `Margin` for the `Flyout` to make it sizable with the window.

So now it's possible to work without the actual width, now we need only set the Margin or HorizontalContentAlignemnt for Left/Right and VerticalContentAnlignment for Top/Bottom `Flyout`.

Here is a sample how it works before.

```xml
<Controls:Flyout x:Name="settingsFlyout"
                 Controls:ControlsHelper.ContentCharacterCasing="Upper"
                 Width="{Binding RelativeSource={RelativeSource FindAncestor, AncestorType=Controls:FlyoutsControl}, Path=ActualWidth}"
                 Margin="200 0 0 0"
                 Header="Settings"
                 Position="Right" />
```

You can see it's not possible to set a top or bottom `Margin`, cause it doesn't works.

Now it's not necessary to set the width via binding to the actual width of the `FlyoutsControl`. And it's also possible to set all values of the `Margin`.

### Flyout with left and top Margin

```xml
<Controls:Flyout x:Name="settingsFlyout"
                 Controls:ControlsHelper.ContentCharacterCasing="Upper"
                 Margin="200 30 0 0"
                 Header="Settings"
                 Position="Right" />
```

![]({{ site.url }}/images/flyout01.png)

![]({{ site.url }}/images/flyout02.png)

### Flyout which covers the whole window

```xml
<Controls:Flyout CloseButtonIsCancel="True"
                 Header="New Goodness"
                 HorizontalContentAlignment="Stretch"
                 Position="Right" />
```

![]({{ site.url }}/images/flyout03.png)
