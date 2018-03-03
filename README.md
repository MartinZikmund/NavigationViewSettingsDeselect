# UWP `NavigationView` Settings Item Deselect Bug

This repro demonstrates a bug found in UWP `NavigationView`. When the user sets the `SelectedItem` to `null`, he should be able to deselect the currently selected item. Unfortunately it does not work when the special Settings item is selected.

## Steps to reproduce

1. Create a new UWP App
2. In `MainPage.xaml` add the following:
``` xaml
<NavigationView x:Name="NavigationView">
    <NavigationView.MenuItems>
        <NavigationViewItem Content="Test item" />
    </NavigationView.MenuItems>
    <Button Click="Deselect_Click" Content="Deselect" />
</NavigationView>
```
3. In `MainPage.xaml.cs` add the following:
``` c#
private void Deselect_Click(object sender, RoutedEventArgs e)
{
    Debug.WriteLine(NavigationView.SelectedItem);
    NavigationView.SelectedItem = null;
}
```
4. Launch the app
5. Select the **Test item** in the `NavigationView` and then click the **Deselect** button
6. Notice `Windows.UI.Xaml.Controls.NavigationViewItem` is written in Output and that **Test item** is deselected.
7. Select the **Settings** in the `NavigationView` and then click the **Deselect** button
8. Notice `Windows.UI.Xaml.Controls.NavigationViewItem` is written in Output and that **Settings** are still selected.

## Expected behavior

After **Settings** is selected `SelectedItem` is set to this special `NavigationViewItem` and setting `SelectedItem` to `null` deselects it.

## Actual behavior

After **Settings** is selected `SelectedItem` is set to this special `NavigationViewItem`, but setting `SelectedItem` to `null` keeps it selected.

## Temporary workaround

You may temporarily add a dummy `NavigationViewItem` into the control, select it and then deselect it and remove it.

``` c#
public static class NavigationViewExtensions
{
    public static void ClearSelection(this NavigationView navigationView)
    {
        var temporaryItem = new NavigationViewItem();
        navigationView.MenuItems.Add(temporaryItem);
        navigationView.SelectedItem = temporaryItem;
        navigationView.SelectedItem = null;
        navigationView.MenuItems.Remove(temporaryItem);
    }
}
```
