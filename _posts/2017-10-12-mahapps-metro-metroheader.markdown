---
layout:     post
title:      "MahApps.Metro MetroHeader"
date:       2017-10-12 11:04:46 +0200
comments:   true
categories: [Develop,MahApps.Metro]
tags:       [WPF,UWP,Xaml,C#,UI,MahApps.Metro,MetroHeader]
---

The `MetroHeader` control is a new control in [MahApps.Metro](https://github.com/MahApps/MahApps.Metro) v1.6.0 which can contains any other content and shows a header on top of this content. It's derived from the `GroupBox` control, so it should work like this.

```xml
<Controls:MetroHeader Margin="2" Header="TextBox Header">
    <Controls:MetroHeader.HeaderTemplate>
        <DataTemplate>
            <StackPanel VerticalAlignment="Center" Orientation="Horizontal">
                <iconPacks:PackIconMaterial VerticalAlignment="Center"
                                            Kind="FaceProfile" />
                <TextBlock Margin="2 0 0 0"
                           VerticalAlignment="Center"
                           Text="{Binding}" />
            </StackPanel>
        </DataTemplate>
    </Controls:MetroHeader.HeaderTemplate>
    <TextBox Text="Enabled" />
</Controls:MetroHeader>
```

![2017-10-11_15h58_35](https://user-images.githubusercontent.com/658431/31493203-754d8f58-af4e-11e7-8c40-c37b5834e1f5.png)

But, why not use the `GroupBox` directly with a custom style?

```xml
<GroupBox Margin="2" Header="TextBox Header" Style={DynamicResource MetroHeaderGroupBoxStyle}>
    <GroupBox.HeaderTemplate>
        <DataTemplate>
            <StackPanel VerticalAlignment="Center" Orientation="Horizontal">
                <iconPacks:PackIconMaterial VerticalAlignment="Center"
                                            Kind="FaceProfile" />
                <TextBlock Margin="2 0 0 0"
                           VerticalAlignment="Center"
                           Text="{Binding}" />
            </StackPanel>
        </DataTemplate>
    </GroupBox.HeaderTemplate>
    <TextBox Text="Enabled" />
</GroupBox>
```

Yes, it's possible to do this and you can see it's similar to the `MetroHeader` usage. But you must always say that you want the `MetroHeaderGroupBoxStyle`.

This is not necessary for the `MetroHeader` control, cause it uses a default style which comes automatically with `MahApps`.

Happy coding!
