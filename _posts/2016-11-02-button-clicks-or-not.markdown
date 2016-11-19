---
layout:     post
title:      "Button clicks or not"
date:       2016-11-02 22:51:57 +0100
comments:   true
categories: [Develop,MahApps.Metro,Tips&Tricks]
tags:       [WPF,Xaml,C#,UI,MahApps.Metro,StackOverflow]
---

How can I prevent a Button click if there is another clickable Control inside the Button?

I've got this question now two times, one at [stackoverflow](http://stackoverflow.com/q/40344271/920384) and the other one at [MahApps.Metro](https://github.com/MahApps/MahApps.Metro/issues/2732).

The solution is quite simple. You can handle this behavior at the Button's click event.

```csharp
private async void ButtonClick(object sender, RoutedEventArgs e)
{
    if (e.OriginalSource != sender)
    {
        // do nothing if we don't hit the button
        e.Handled = true;
    }
    else
    {
        // yes, we hit the button
        await this.ShowMessageAsync(string.Empty, $"Yeah, you clicked the real button!");
    }
}
```

This sample uses the `MahApps.Metro` library, so if you want to test this code you must download the [NuGet package](https://www.nuget.org/packages/MahApps.Metro/).

```xml
<StackPanel xmlns:controls="http://metro.mahapps.com/winfx/xaml/controls"
            Margin="20"
            HorizontalAlignment="Center"
            VerticalAlignment="Center"
            Orientation="Vertical">
    <Button Height="40"
            Margin="0 5"
            BorderThickness="0"
            Click="ButtonClick"
            Style="{DynamicResource SquareButtonStyle}">
        <Grid Margin="0">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="150" />
                <ColumnDefinition Width="100" />
            </Grid.ColumnDefinitions>

            <TextBlock Grid.Column="0"
                        HorizontalAlignment="Center"
                        VerticalAlignment="Center">
                Toggle it
            </TextBlock>
            <controls:ToggleSwitch Grid.Column="1"
                                    HorizontalAlignment="Center"
                                    VerticalAlignment="Center"
                                    IsChecked="True"
                                    OffLabel=""
                                    OnLabel=""
                                    Style="{StaticResource MahApps.Metro.Styles.ToggleSwitch.Win10}" />
        </Grid>
    </Button>

    <Button Height="40"
            Margin="0 5"
            BorderThickness="0"
            Click="ButtonClick"
            Style="{DynamicResource SquareButtonStyle}">
        <Grid Margin="0">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="150" />
                <ColumnDefinition Width="100" />
            </Grid.ColumnDefinitions>

            <TextBlock Grid.Column="0"
                        HorizontalAlignment="Center"
                        VerticalAlignment="Center">
                Counter Button
            </TextBlock>
            <controls:NumericUpDown Grid.Column="1" Width="100" />
        </Grid>
    </Button>
</StackPanel>
```

![]({{ site.url }}/images/clickable.gif)  
