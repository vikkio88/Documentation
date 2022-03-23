# Compiled Bindings

Bindings defined in the XAML are using reflection in order to find and access the requested property in your `ViewModel`. In Avalonia you can also use compiled bindings, which has some benefits:
  * If you use compiled bindings and the property you bind to is not found, you will get a compile-time error. Hence you get a much better debugging experience.
  * Reflection is known to be slow ([see this article on codeproject.com](https://www.codeproject.com/Articles/1161127/Why-is-reflection-slow)). Using compiled bindings can therefore improve the performance of your application. 
 
## Enable and disable compiled bindings

Compiled bindings are not enabled by default. To enable compiled bindings, you will need to define the `DataType` of the object you want to bind to first. In [`DataTemplates`](https://docs.avaloniaui.net/misc/wpf/datatemplates) there is a property `DataType`, for all other elements you can set it via `x:DataType`. Most likely you will set `x:DataType` in your root node, for example in a `Window` or an `UserControl`. From Avalonia version `0.10.12` onward you can also specify the `DataType` in the `Binding` directly.

You can now enable or disable compiled bindings by setting `x:CompileBindings="[True|False]"`. All child nodes will inherit this property, so you can enable it in your root node and disable it for a specific child, if needed.

```xml
<!-- Set DataType and enable compiled bindings -->
<UserControl xmlns="https://github.com/avaloniaui"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:vm="using:MyApp.ViewModels"
             x:DataType="vm:MyViewModel"
             x:CompileBindings="True">
    <StackPanel>
        <TextBlock Text="Last name:" />
        <TextBox Text="{Binding LastName}" />
        <TextBlock Text="Given name:" />
        <TextBox Text="{Binding GivenName}" />
        <TextBlock Text="E-Mail:" />
        <!-- Set DataType inside the Binding-markup -->
        <TextBox Text="{Binding MailAddress, DataType={x:Type vm:MyViewModel}}" />
        
        <!-- We cannot use complied bindings to bind to methods, so we opt them out for the button -->
        <Button x:CompildedBindings="False" 
                Content="Send an E-Mail"
                Command="{Binding SendEmailCommand}" />
    </StackPanel>
</UserControl>
```

## CompiledBinding-Markup

If you don't want to enable compiled bindings for all child nodes, you can also use the `CompiledBinding`-markup. You still need to define the `DataType`, but you can omit `x:CompileBindings="True"`. 

```xml
<!-- Set DataType -->
<UserControl xmlns="https://github.com/avaloniaui"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:vm="using:MyApp.ViewModels"
             x:DataType="vm:MyViewModel">
    <StackPanel>
        <TextBlock Text="Last name:" />
        <!-- use CompiledBinging markup for your binding -->
        <TextBox Text="{CompiledBinding LastName}" />
        <TextBlock Text="Given name:" />
        <TextBox Text="{CompiledBinding GivenName}" />
        <TextBlock Text="E-Mail:" />
        <TextBox Text="{CompiledBinding MailAddress}" />
        
        <!-- We cannot use complied bindings to bind to methods, so we use the normal Binding -->
        <Button x:CompildedBindings="False" 
                Content="Send an E-Mail"
                Command="{Binding SendEmailCommand}" />
    </StackPanel>
</UserControl>
```

## ReflectionBinding-Markup
If you have compiled bindings enabled in the root node (via `x:CompileBindings="True"`) and you either don't want to use complied binding at a certain position or you hit one of the [known limitations](#known-limitations), you can use the `ReflectionBinding`-markup. 

```xml
<!-- Set DataType -->
<UserControl xmlns="https://github.com/avaloniaui"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:vm="using:MyApp.ViewModels"
             x:DataType="vm:MyViewModel">
    <StackPanel>
        <TextBlock Text="Last name:" />
        <!-- use CompiledBinging markup for your binding -->
        <TextBox Text="{Binding LastName}" />
        <TextBlock Text="Given name:" />
        <TextBox Text="{Binding GivenName}" />
        <TextBlock Text="E-Mail:" />
        <TextBox Text="{Binding MailAddress}" />
        
        <!-- We cannot use complied bindings to bind to methods, so we use ReflectionBinding instead -->
        <Button x:CompildedBindings="False" 
                Content="Send an E-Mail"
                Command="{ReflectionBinding SendEmailCommand}" />
    </StackPanel>
</UserControl>
```

## Known limitations

Compiled bindings have some known limitations: 
* Compiled bindings cannot be used to bind to named elements
* Compiled bindings cannot be used in Styles with RelativeSource set to TemplatedParent (e.g.:  `{Binding Width, RelativeSource={RelativeSource TemplatedParent}}`)

If you hit one of those limitations you should disable compiled bindings for the failing XAML-node or use `ReflectionBinding` instead.