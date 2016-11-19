---
layout:     post
title:      "Modern UI with MahApps.Metro"
date:       2014-11-18 12:45:34 +0100
comments:   true
categories: [Develop,MahApps.Metro,Tips&Tricks]
tags:       [WPF,Xaml,C#,UI,MahApps.Metro]
---

As a **MahApps.Metro** member I'am often confronted with questions like:

> How do I start with MahApps.Metro?

> How can I get the glow window?

> Where is the shadow around the main window?


Now I could say, hey, we have a nice [web page](http://mahapps.com) where you can look at the [quick start guide](http://mahapps.com/guides/quick-start.html), but I want to help users/developers everywhere.

So, this article will show you how fast it is to get a modern UI using MahApps.Metro.

### Let's go

First let's create an empty WPF project with some simple controls.

```xml
<Window x:Class="MahAppsMetroSample.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="MainWindow"
        Height="350" Width="525">
    <Grid>
        
        <StackPanel VerticalAlignment="Center">
            <TextBlock Text="Modern UI with MahApps.Metro" HorizontalAlignment="Center" FontSize="30" />
            <Button Content="Click Me..." HorizontalAlignment="Left" Margin="5" />
            <TextBox Text="Lorem ipsum dolor sit amet, consetetur sadipscing" HorizontalAlignment="Left" Margin="5" />
        </StackPanel>

    </Grid>
</Window>
```

```csharp
using System;
using System.Windows;

namespace MahAppsMetroSample
{
  public partial class MainWindow : Window
  {
    public MainWindow()
    {
      InitializeComponent();
    }
  }
}
```

![normal main window]({{ site.url }}/images/modernui_01.png)

You can see that's not a nice UI, so let's change it.

You can install the latest stable `MahApps.Metro` library via the NuGet GUI (right click on your project, click *Manage NuGet Packages*, select *Online* and search for *MahApps.Metro*) or with the Package Manager Console:

```powershell
PM> Install-Package MahApps.Metro
```

If you need the latest alpha version, use this:

```powershell
PM> Install-Package MahApps.Metro -Pre
```

Now you should see:

```powershell
PM> Install-Package MahApps.Metro
Installing 'MahApps.Metro 0.14.0.0'.
Successfully installed 'MahApps.Metro 0.14.0.0'.
Adding 'MahApps.Metro 0.14.0.0' to MahAppsMetroSample.
Successfully added 'MahApps.Metro 0.14.0.0' to MahAppsMetroSample.
```

After installing, open up `MainWindow.xaml` and put this reference attribute inside the opening `Window` tag:

```xml
xmlns:Controls="clr-namespace:MahApps.Metro.Controls;assembly=MahApps.Metro"
```

or

```xml
xmlns:Controls="http://metro.mahapps.com/winfx/xaml/controls"
```

Now change the opening and closing `Window` tag to `Controls:MetroWindow`.

```xml
<Controls:MetroWindow x:Class="MahAppsMetroSample.MainWindow"
                      xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
                      xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
                      xmlns:Controls="http://metro.mahapps.com/winfx/xaml/controls"
                      Title="MainWindow"
                      Height="350" Width="525">
    <Grid>

        <StackPanel VerticalAlignment="Center">
            <TextBlock Text="Modern UI with MahApps.Metro" HorizontalAlignment="Center" FontSize="30" />
            <Button Content="Click Me..." HorizontalAlignment="Left" Margin="5" />
            <TextBox Text="Lorem ipsum dolor sit amet, consetetur sadipscing" HorizontalAlignment="Left" Margin="5" />
        </StackPanel>

    </Grid>
</Controls:MetroWindow>
```

You'll also need to modify the code behind `MainWindow.xaml.cs` file so that the base class for `MainWindow` matches the `MetroWindow` class of the XAML file.

```csharp
using System;
using MahApps.Metro.Controls; // MahApps.Metro reference

namespace MahAppsMetroSample
{
  public partial class MainWindow : MetroWindow
  {
    public MainWindow()
    {
      InitializeComponent();
    }
  }
}
```

All of `MahApp.Metro's` resources are contained within separate resource dictionaries. In order to adopt the MahApps.Metro accent and theme for most of the controls , you will need to add the following ResourceDictionaries to your App.xaml (**Make sure that all file names are spelled correct. They are Case Sensitive!**).

```xml
<Application x:Class="MahAppsMetroSample.App"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             StartupUri="MainWindow.xaml">
    <Application.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <ResourceDictionary Source="pack://application:,,,/MahApps.Metro;component/Styles/Controls.xaml" />
                <ResourceDictionary Source="pack://application:,,,/MahApps.Metro;component/Styles/Fonts.xaml" />
                <ResourceDictionary Source="pack://application:,,,/MahApps.Metro;component/Styles/Colors.xaml" />
                <ResourceDictionary Source="pack://application:,,,/MahApps.Metro;component/Styles/Accents/Blue.xaml" />
                <ResourceDictionary Source="pack://application:,,,/MahApps.Metro;component/Styles/Accents/BaseLight.xaml" />
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

The end result will look something like this:

![modern ui main window]({{ site.url }}/images/modernui_02.png)

You can choose between this available accents

> "Red", "Green", "Blue", "Purple", "Orange", "Lime", "Emerald", "Teal", "Cyan", "Cobalt", "Indigo", "Violet", "Pink", "Magenta", "Crimson", "Amber", "Yellow", "Brown", "Olive", "Steel", "Mauve", "Taupe", "Sienna"

and themes

> "BaseLight", "BaseDark"

### Borders

The `MetroWindow` has no border by default, but you can choose between 3 different borders.

#### A normal border

```xml
<Controls:MetroWindow x:Class="MahAppsMetroSample.MainWindow"
                      
                      BorderBrush="{DynamicResource AccentColorBrush}"
                      BorderThickness="1"
                      
                      Height="350" Width="525">

</Controls:MetroWindow>
```

![modern ui main window with a border]({{ site.url }}/images/modernui_03.png)

#### A glow border

```xml
<Controls:MetroWindow x:Class="MahAppsMetroSample.MainWindow"
                      
                      GlowBrush="{DynamicResource AccentColorBrush}"
                      
                      Height="350" Width="525">

</Controls:MetroWindow>
```

![modern ui main window with a glow border]({{ site.url }}/images/modernui_04.png)

#### A drop shadow

```xml
<Controls:MetroWindow x:Class="MahAppsMetroSample.MainWindow"
                      
                      EnableDWMDropShadow="True"
                      ResizeMode="CanResizeWithGrip"
                      
                      Height="350" Width="525">

</Controls:MetroWindow>
```

![modern ui main window with drop shadow]({{ site.url }}/images/modernui_05.png)

#### The clean window style

There is another clean window style for your application. Put only the `CleanWindowStyleKey` inside the opening `MetroWindow` tag and use the `CleanWindow` resource.

```xml
<Controls:MetroWindow x:Class="MahAppsMetroSample.MainWindow"
                      
                      Style="{DynamicResource CleanWindowStyleKey}"
                      
                      Height="350" Width="525">
    <Window.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <ResourceDictionary Source="pack://application:,,,/MahApps.Metro;component/Styles/Clean/CleanWindow.xaml" />
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </Window.Resources>

</Controls:MetroWindow>
```

![modern ui main window with clean window style]({{ site.url }}/images/modernui_06.png)

#### The visual studio style

A simple style that looks like `Visual Studio`. To get this, put only the `VSWindowStyleKey` inside the opening `MetroWindow` tag and use the colors and style resource.

```xml
<Controls:MetroWindow x:Class="MahAppsMetroSample.MainWindow"
                      
                      Style="{DynamicResource VSWindowStyleKey}"
                      
                      Height="350" Width="525">
    <Window.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <ResourceDictionary Source="pack://application:,,,/MahApps.Metro;component/Styles/VS/Colors.xaml" />
                <ResourceDictionary Source="pack://application:,,,/MahApps.Metro;component/Styles/VS/Styles.xaml" />
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </Window.Resources>

</Controls:MetroWindow>
```

![modern ui main window with vs window style]({{ site.url }}/images/modernui_07.png)

That's it!

The complete sample can be found on [GitHub](https://github.com/punker76/code-samples/tree/master/MahAppsMetroSample).
