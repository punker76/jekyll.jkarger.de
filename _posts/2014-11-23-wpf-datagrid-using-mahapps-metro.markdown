---
layout:     post
title:      "WPF DataGrid using MahApps.Metro"
date:       2014-11-23 13:10:17 +0100
comments:   true
categories: [Develop,MahApps.Metro,Tips&Tricks]
tags:       [WPF,Xaml,C#,UI,MahApps.Metro]
---

`MahApps.Metro` ships two cool styles to beautify the `DataGrid` control that ships with .NET 4.0 by default. With this styling you can use filtering, grouping, sorting and other standard features that you would expect of a `DataGrid`. It also contains a `DataGridCheckBoxColumn` style as well as a custom `DataGridNumericUpDownColumn` style.

So for showing the differences, here is the default look:

![default style for DataGrid]({{ site.url }}/images/datagrid_01.png)

Now, if you use the `MahApps.Metro` style for the application, you get this result:

![default style for DataGrid]({{ site.url }}/images/datagrid_02.png)

Looks awesome!

There is another style called `Azure` which can be used by

```xml
<DataGrid Style="{DynamicResource AzureDataGrid}" />
```

![default style for DataGrid]({{ site.url }}/images/datagrid_03.png)

### Removing the right gripper

There is an issue with the style, that can be very confused. The `DataGrid` has a right gripper at the column header without any functionality.

![default style for DataGrid]({{ site.url }}/images/datagrid_04.png)

So how can it be removed? It's only possible to override the `DataGridColumnHeadersPresenter` globally.

```xml
<!-- for ne metro style -->
<Style TargetType="{x:Type DataGridColumnHeadersPresenter}">
    <Setter Property="Template">
        <Setter.Value>
            <ControlTemplate TargetType="DataGridColumnHeadersPresenter">
                <Grid>
                    <Border IsHitTestVisible="False"
                            BorderThickness="0 0 0 3"
                            Background="Transparent"
                            BorderBrush="{DynamicResource GrayBrush5}" />
                    <ItemsPresenter />
                </Grid>
            </ControlTemplate>
        </Setter.Value>
    </Setter>
</Style>
 
<!-- for the azure style -->
<Style TargetType="{x:Type DataGridColumnHeadersPresenter}">
    <Setter Property="Template">
        <Setter.Value>
            <ControlTemplate TargetType="DataGridColumnHeadersPresenter">
                <Grid>
                    <Border IsHitTestVisible="False"
                            Margin="0 0 0 10"
                            BorderThickness="0 0 0 3"
                            Background="Transparent"
                            BorderBrush="{DynamicResource GrayBrush5}" />
                    <ItemsPresenter />
                </Grid>
            </ControlTemplate>
        </Setter.Value>
    </Setter>
</Style>
```

![default style for DataGrid]({{ site.url }}/images/datagrid_05.png)

A complete example can be found on [GitHub](https://github.com/punker76/code-samples).
