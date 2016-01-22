---
layout:     post
title:      "Create new MahApps window in separate thread"
date:       2016-01-22 15:17:00 +0100
comments:   true
categories: [UI,Thread,WPF,MahApps.Metro,C#,XAML]
---

So, as the title says, people often wants to show a window in a separate ui thread. If we now google about this we can find many articles which explains the hole situation and shows also some code snippets. But sometimes there are situations where this code not works, one of this is often asked by people who uses [MahApps.Metro](https://github.com/MahApps/MahApps.Metro).

> How can I run a MetroWindow in a separate thread?

And this people get also this exception:

```
Exception thrown: 'System.Windows.Markup.XamlParseException' in PresentationFramework.dll
```

Here are two blog articles which explains the solution:

- [Launching a WPF Window in a Separate Thread](http://reedcopsey.com/2011/11/28/launching-a-wpf-window-in-a-separate-thread-part-1/) by Reed Copsey, Jr.
- [Threading model in WPF and application resources](http://sergey-yatsenko.blogspot.de/2010/09/threading-model-in-wpf-and-application.html) by Sergey Yatsenko

And here is my solution with MahApps.Metro that works in my machine™.

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
    // To solve System.Windows.Markup.XamlParserException the resources must be merged with resource dictionary of window but not application.
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
