---
layout:     post
title:      "Create new MahApps window in separate thread"
date:       2016-01-22 15:17:00 +0100
comments:   true
categories: [Develop,MahApps.Metro,Tips&Tricks]
tags:       [WPF,Xaml,C#,UI,Threads,MahApps.Metro]
---

So, as the title says, people often want to show a window in a separate ui thread. If we now google about this we can find many articles which explain the whole situation and which also show some code snippets. But sometimes there are situations where this code doesn't work. This is often requested by people who use [MahApps.Metro](https://github.com/MahApps/MahApps.Metro).

> How can I run a MetroWindow in a separate thread? Can you explain why I get this exception?

```
Exception thrown: 'System.Windows.Markup.XamlParseException' in PresentationFramework.dll
```

So, here are two blog articles which explain the problem and also explain a solution:

- [Launching a WPF Window in a Separate Thread](http://reedcopsey.com/2011/11/28/launching-a-wpf-window-in-a-separate-thread-part-1/) by Reed Copsey, Jr.
- [Threading model in WPF and application resources](http://sergey-yatsenko.blogspot.de/2010/09/threading-model-in-wpf-and-application.html) by Sergey Yatsenko

Here is my solution with MahApps.Metro that works on my machine™.

```csharp
// Create a thread
var newWindowThread = new Thread(new System.Threading.ParameterizedThreadStart((state) =>
{
    // Create our context, and install it:
    SynchronizationContext.SetSynchronizationContext(new DispatcherSynchronizationContext(Dispatcher.CurrentDispatcher));

    // Create and show the Window
    var testWindow = new MetroWindow()
    {
        WindowStartupLocation = WindowStartupLocation.CenterScreen,
        Title = "Window in separate thread...",
        Width = 500,
        Height = 300,
        BorderThickness = new Thickness(1)
    };
    // To solve System.Windows.Markup.XamlParserException the resources must be merged with the resource dictionary of window but not application.
    testWindow.Resources.MergedDictionaries.Add(new ResourceDictionary { Source = new Uri("/PresentationFramework.Aero, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35, ProcessorArchitecture=MSIL;component/themes/aero.normalcolor.xaml", UriKind.RelativeOrAbsolute) });
    testWindow.Resources.MergedDictionaries.Add(new ResourceDictionary { Source = new Uri("pack://application:,,,/MahApps.Metro;component/Styles/Controls.xaml") });
    testWindow.Resources.MergedDictionaries.Add(new ResourceDictionary { Source = new Uri("pack://application:,,,/MahApps.Metro;component/Styles/Fonts.xaml") });
    testWindow.Resources.MergedDictionaries.Add(new ResourceDictionary { Source = new Uri("pack://application:,,,/MahApps.Metro;component/Styles/Colors.xaml") });
    testWindow.Resources.MergedDictionaries.Add(new ResourceDictionary { Source = new Uri("pack://application:,,,/MahApps.Metro;component/Styles/Accents/Red.xaml") });
    testWindow.Resources.MergedDictionaries.Add(new ResourceDictionary { Source = new Uri("pack://application:,,,/MahApps.Metro;component/Styles/Accents/BaseLight.xaml") });
    testWindow.SetResourceReference(MetroWindow.BorderBrushProperty, "AccentColorBrush");
    testWindow.Closed += (o, args) =>
    {
        Dispatcher.CurrentDispatcher.BeginInvokeShutdown(DispatcherPriority.Background);
        testWindow = null;
    };
    testWindow.Show();

    // Start the Dispatcher Processing
    System.Windows.Threading.Dispatcher.Run();
}));
// Set the apartment state
newWindowThread.SetApartmentState(ApartmentState.STA);
// Make the thread a background thread
newWindowThread.IsBackground = true;
// Start the thread
newWindowThread.Start();
```

It's also possible to use a custom `MetroWindow` instead creating an empty window. In this case it's necessary to put the `MahApps` resources in the XAML code of the window itself.

```xml
<controls:MetroWindow x:Class="MetroDemo.ExampleWindows.CustomWindow"
                      xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
                      xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
                      xmlns:controls="http://metro.mahapps.com/winfx/xaml/controls"
                      xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
                      xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
                      Title="CustomWindow"
                      Width="300"
                      Height="300"
                      mc:Ignorable="d">

    <Window.Resources>
        <ResourceDictionary>
            <ResourceDictionary.MergedDictionaries>
                <ResourceDictionary Source="pack://application:,,,/MahApps.Metro;component/Styles/Controls.xaml" />
                <ResourceDictionary Source="pack://application:,,,/MahApps.Metro;component/Styles/Fonts.xaml" />
                <ResourceDictionary Source="pack://application:,,,/MahApps.Metro;component/Styles/Colors.xaml" />
                <ResourceDictionary Source="pack://application:,,,/MahApps.Metro;component/Styles/Accents/Red.xaml" />
                <ResourceDictionary Source="pack://application:,,,/MahApps.Metro;component/Styles/Accents/BaseLight.xaml" />
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </Window.Resources>

    <Grid>

        <ComboBox x:Name="comboBox1"
                  Width="170"
                  Height="48"
                  Margin="10"
                  HorizontalAlignment="Left"
                  VerticalAlignment="Top">
            <ComboBoxItem>Test 1</ComboBoxItem>
            <ComboBoxItem>Test 2</ComboBoxItem>
        </ComboBox>

    </Grid>
</controls:MetroWindow>
```

Simply change

```csharp
var testWindow = new CustomWindow()
```

![2016-01-22_16h12_04](https://cloud.githubusercontent.com/assets/658431/12514416/87ab92a0-c123-11e5-9948-26dabf1fad13.png)
