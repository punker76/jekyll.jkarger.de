---
layout:     post
title:      "Generate WPF ListView columns automatically"
date:       2011-11-08 23:16 +0100
comments:   true
categories: [Develop,Tips&Tricks]
tags:       [WPF,Xaml,C#,StackOverflow]
---

Sometimes it's not necessary to display a list of elements with a `DataGrid`, for a simple short way we can use the lighter `ListView`.
My solution, also posted at [stackoverflow](http://stackoverflow.com/a/8050721/920384), generates a `ListView` with columns for all properties with a given datatype automatically.

```csharp
public class BaseListView : ListView
{
  public Type DataType { get; set; }

  protected override void OnPropertyChanged(DependencyPropertyChangedEventArgs e)
  {
    base.OnPropertyChanged(e);

    if (e.Property.Name == "ItemsSource"
        && e.OldValue != e.NewValue
        && e.NewValue != null
        && this.DataType != null)
    {
      CreateColumns(this);
    }
  }

  private static void CreateColumns(BaseListView lv)
  {
    var gridView = new GridView { AllowsColumnReorder = true };

    var properties = lv.DataType.GetProperties();
    foreach (var pi in properties)
    {
      var browsableAttribute = pi.GetCustomAttributes(true).FirstOrDefault(a => a is BrowsableAttribute) as BrowsableAttribute;
      if (browsableAttribute != null && !browsableAttribute.Browsable)
      {
        continue;
      }
      
      var binding = new Binding { Path = new PropertyPath(pi.Name), Mode = BindingMode.OneWay };
      var gridViewColumn = new GridViewColumn() { Header = pi.Name, DisplayMemberBinding = binding };
      gridView.Columns.Add(gridViewColumn);
    }
    
    lv.View = gridView;
  }
}
```

```xml
<Grid>
  <local:BaseListView x:Name="listView"
                      DataType="{x:Type DummyType}"
                      ItemsSource="{Binding Mode=OneWay, Path=DummyTypeList}" />
</Grid>
```

That's it.
