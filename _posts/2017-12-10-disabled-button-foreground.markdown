---
layout:     post
title:      "Disabled Button Foreground"
date:       2017-12-10 19:20:08 +0100
comments:   true
categories: [Develop,MahApps.Metro]
tags:       [WPF,Xaml,C#,UI,MahApps.Metro]
---

## The Problem

If you use a `Button` with a different `Foreground` brush then the original and a custom content, the `Foreground` wont be change on disabled state. In the following sample I will use directly the `IsEnabled` property, but it happens also if you use a `Command` which handles the state too.

```xml
<Button Width="60" Height="60" Margin="2" IsEnabled="False" Foreground="Blue">
  <iconPacks:PackIconModern Width="20" Height="20" Kind="CitySeattle"/>
</Button>
```

<p align="left">
  <img src="{{ site.url }}/images/disabledbutton01.png">
</p>

You can see that the blue `Foreground` is still shown. But, there are now two ways to solve this.

## Solution 1

The first solution is to use a `ContentTemplate` for the custom content, cause the `Foreground` for the `Button` will be applied for this template and not for the `Content` itself.

```xml
<Button Width="60" Height="60" Margin="2" IsEnabled="False" Foreground="Blue">
  <Button.ContentTemplate>
    <DataTemplate>
      <iconPacks:PackIconModern Width="20" Height="20" Kind="CitySeattle"/>
    </DataTemplate>
  </Button.ContentTemplate>
</Button>
```

<p align="left">
  <img src="{{ site.url }}/images/disabledbutton02.png">
</p>

## Solution 2

The second solution is to use a style for the `Button` which sets the `Foreground` for both states. So with this solution it's not necessary to use a `ContentTenplate` like above.

```xml
<Button Width="60" Height="60" Margin="2" IsEnabled="False" Style="{StaticResource AccentButtonStyle}">
  <iconPacks:PackIconModern Width="20" Height="20" Kind="CitySeattle"/>
</Button>
```

```xml
<Style x:Key="AccentButtonStyle" BasedOn="{StaticResource {x:Type Button}}" TargetType="{x:Type Button}">
  <Setter Property="Foreground" Value="Blue"/>
  <Style.Triggers>
    <Trigger Property="IsEnabled" Value="False">
      <Setter Property="Foreground" Value="Gray"/>
    </Trigger>
  </Style.Triggers>
</Style>
```

<p align="left">
  <img src="{{ site.url }}/images/disabledbutton02.png">
</p>

## Where it came from

This issue was reported at [MahApps.Metro](https://github.com/MahApps/MahApps.Metro) together with the `MahApps.Metro.Styles.MetroCircleButtonStyle`. The style itself doesn't set the `Foreground` for the `ContentTemplate` correct.

```xml
<Trigger Property="IsEnabled" Value="False">
  <Setter TargetName="PART_ContentPresenter" Property="TextElement.Foreground" Value="{DynamicResource GrayBrush7}" />
  <!-- This was incorrect!!!
  <Setter Property="Foreground" Value="{DynamicResource GrayBrush7}" />
  -->
  <Setter TargetName="ellipse" Property="Opacity" Value="0.3" />
</Trigger>
```

<p align="left">
  <img src="{{ site.url }}/images/disabledbutton03.png">
</p>

For this simple sample I used the [MahApps.Metro.IconPacks](https://github.com/MahApps/MahApps.Metro.IconPacks).

Happy coding!