# WPF Cheat Sheet – Übersicht für den Einstieg

Dieses Dokument enthält eine strukturierte Übersicht über die wichtigsten Konzepte, Elemente und Techniken in Windows Presentation Foundation (WPF). Ideal für Einsteiger, die eine saubere UI mit MVVM-Architektur entwickeln wollen.

---

## Inhaltsverzeichnis

| Thema                    | Beschreibung                                                   |
|--------------------------|----------------------------------------------------------------|
| XAML                     | Deklarative Beschreibung der UI in XML-Syntax                  |
| Binding                  | Datenbindung von UI-Elementen an Datenmodelle                 |
| Commands                 | Aktionen in der UI über ICommand-Schnittstelle                |
| MVVM                     | Architektur: Model - View - ViewModel                         |
| DataContext              | Datenquelle für Bindings setzen                               |
| INotifyPropertyChanged   | Benachrichtigt View bei Änderungen im ViewModel               |
| Dependency Properties    | Erweiterte Properties mit Bindung & Styling                   |
| Styles & Templates       | Optische Gestaltung und Wiederverwendbarkeit von UI-Elementen |
| Triggers                 | Reaktion auf Änderungen von Werten oder Zuständen             |
| Ressourcen (ResourceDictionary) | Wiederverwendbare Farben, Styles und ControlTemplates     |
| Events                   | Direkte Ereignisbehandlung in Code-Behind                     |
| Value Converters         | Umwandlung von Daten bei der Anzeige                          |
| Navigation               | Seitenstruktur mit Frame/Page                                 |
| User Controls            | Wiederverwendbare UI-Komponenten                              |
| DataGrid/ListView        | Daten in Tabellenform anzeigen und bearbeiten                 |
| Visual Tree / Logical Tree | Strukturelle UI-Darstellung intern                           |
| Styling & Ressourcen     | Gestaltung von UI ähnlich zu CSS – Farben, Fonts, Templates   |
| Datenzugriff             | Anbindung von Datenmodellen, ObservableCollection, Services   |

---

## Häufig verwendete XAML-Elemente

### UI-Elemente (Interaktiv)
| Element     | Beschreibung                            | Beispiel |
|-------------|-----------------------------------------|----------|
| `Button`    | Löst eine Aktion aus bei Klick          | `<Button Content="OK" />` |
| `TextBox`   | Eingabefeld für Text                    | `<TextBox Text="Hallo" />` |
| `CheckBox`  | Auswahlfeld mit Haken                   | `<CheckBox Content="Ja" />` |
| `ComboBox`  | Dropdown-Liste zur Auswahl              | `<ComboBox ItemsSource="{Binding Optionen}" />` |
| `ListBox`   | Liste von auswählbaren Einträgen        | `<ListBox ItemsSource="{Binding Elemente}" />` |
| `Label`     | Textanzeige ohne Eingabe                | `<Label Content="Info" />` |
| `Slider`    | Schieberegler                          | `<Slider Minimum="0" Maximum="100" />` |
| `Image`     | Bildanzeige                             | `<Image Source="logo.png" />` |

### Layout-Elemente
| Element     | Beschreibung                            | Beispiel |
|-------------|-----------------------------------------|----------|
| `Grid`      | Tabellarisches Layout                   | `<Grid><Grid.RowDefinitions>...</Grid></Grid>` |
| `StackPanel`| Stapelt Elemente vertikal oder horizontal | `<StackPanel Orientation="Vertical">...</StackPanel>` |
| `WrapPanel` | Wie StackPanel, aber umbricht automatisch | `<WrapPanel>...</WrapPanel>` |
| `DockPanel` | Elemente an Seiten andocken             | `<DockPanel><Button DockPanel.Dock="Top" /></DockPanel>` |
| `Canvas`    | Absolute Positionierung (selten)        | `<Canvas><Button Canvas.Left="10" /></Canvas>` |

---

## 1. XAML (Extensible Application Markup Language)

- Dient zur Beschreibung von UI-Elementen in einer deklarativen XML-ähnlichen Sprache.
- Trennung von UI-Logik (XAML) und Programmlogik (C#).

```xaml
<Window x:Class="Beispiel.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="Demo" Height="200" Width="300">
    <Grid>
        <Button Content="Klick mich" Width="100" Height="30" />
    </Grid>
</Window>
```

---

## 2. Binding (Datenbindung)

- Bindet UI-Elemente an Properties im ViewModel.
- Ermöglicht automatische Aktualisierung bei Änderungen.

```xaml
<TextBox Text="{Binding Benutzername, UpdateSourceTrigger=PropertyChanged}" />
```

```csharp
public string Benutzername
{
    get => _benutzername;
    set { _benutzername = value; OnPropertyChanged(); }
}
```

---

## 3. Styling & Ressourcen (vergleichbar mit CSS)

- Styles können zentral definiert und wiederverwendet werden.
- Ressourcen (Farben, Fonts, Brushes) werden im `ResourceDictionary` gepflegt.

```xaml
<Window.Resources>
    <SolidColorBrush x:Key="PrimärFarbe" Color="#FF6200EE" />

    <Style TargetType="Button">
        <Setter Property="Background" Value="{StaticResource PrimärFarbe}" />
        <Setter Property="Foreground" Value="White" />
        <Setter Property="Padding" Value="10" />
    </Style>
</Window.Resources>

<Button Content="Speichern" />
```

- Themes oder externe Ressourcen können in App.xaml eingebunden werden:

```xaml
<Application.Resources>
    <ResourceDictionary>
        <ResourceDictionary.MergedDictionaries>
            <ResourceDictionary Source="/Styles/Farben.xaml" />
        </ResourceDictionary.MergedDictionaries>
    </ResourceDictionary>
</Application.Resources>
```

---

## 4. Datenzugriff und -verarbeitung

- Datenmodelle sollten observable sein (z. B. `ObservableCollection<T>`, `INotifyPropertyChanged`).
- Datenquellen können statisch oder dynamisch eingebunden werden (z. B. per Service).

```csharp
public ObservableCollection<Person> PersonenListe { get; } = new();
```

```xaml
<ListBox ItemsSource="{Binding PersonenListe}" />
```

- Für externe Daten können Services genutzt werden:
```csharp
public class DatenService
{
    public IEnumerable<Person> LadePersonen()
    {
        // Datenbank oder API-Aufruf
    }
}
```

- Die Verbindung erfolgt über das ViewModel:
```csharp
_personenListe = new ObservableCollection<Person>(_datenService.LadePersonen());
```

---

## 5. Commands

- Commands werden verwendet, um Aktionen auszulagern und testbar zu machen.
- Häufig verwendet mit `ICommand`-Implementierungen (z. B. `RelayCommand`).

```xaml
<Button Content="Speichern" Command="{Binding SpeichernCommand}" />
```

```csharp
public ICommand SpeichernCommand { get; }

public ViewModel()
{
    SpeichernCommand = new RelayCommand(Speichern);
}

private void Speichern()
{
    // Logik zum Speichern
}
```

---

## 6. MVVM (Model-View-ViewModel)

- Trennung von UI, Logik und Daten.
- `ViewModel` ist Bindeglied zwischen `Model` und `View`.
- Unterstützt Testbarkeit und Wiederverwendbarkeit.

```plaintext
View       → XAML
ViewModel  → Bindings, Properties, Commands
Model      → Reine Daten (z. B. Klassen, DTOs)
```

---

## 7. DataContext

- Der `DataContext` bestimmt, auf welches Objekt Bindings zugreifen.

```xaml
<Window.DataContext>
    <local:MeinViewModel />
</Window.DataContext>
```

- Alternativ im Code:
```csharp
this.DataContext = new MeinViewModel();
```

---

## 8. INotifyPropertyChanged

- Ermöglicht automatische UI-Aktualisierung bei Änderungen im ViewModel.

```csharp
public class ViewModelBase : INotifyPropertyChanged
{
    public event PropertyChangedEventHandler PropertyChanged;

    protected void OnPropertyChanged([CallerMemberName] string name = null)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(name));
    }
}
```

---

## 9. Dependency Properties

### Unterschied zwischen normalen Properties und Dependency Properties

In WPF gibt es zwei zentrale Arten von Properties: klassische CLR-Properties (z. B. in Datenmodellen oder ViewModels) und Dependency Properties (z. B. in UI-Elementen oder UserControls).

| Eigenschaftstyp        | Verwendung                          | Speichert Wert | Unterstützt Binding | Unterstützt Styling/Animation |
|------------------------|--------------------------------------|----------------|----------------------|-------------------------------|
| Normale Property       | Datenmodelle, ViewModel              | Ja             | Ja (mit INotify)     | Nein                          |
| Dependency Property    | UserControls, WPF-Steuerelemente     | Intern in WPF  | Ja                   | Ja                            |

### Beispiel: Dependency Property in einem Control

```csharp
public static readonly DependencyProperty IstAktivProperty =
    DependencyProperty.Register("IstAktiv", typeof(bool), typeof(MyControl));

public bool IstAktiv
{
    get => (bool)GetValue(IstAktivProperty);        // Zugriff auf internen WPF-Speicher
    set => SetValue(IstAktivProperty, value);       // Setzen des Wertes
}
```

Diese Property kann in XAML gebunden, gestylt und animiert werden:
```xaml
<local:MyControl IstAktiv="True" />
```

### Vergleich: Normale Auto-Property im ViewModel oder Model

```csharp
public class Lager
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Adresse { get; set; }
}
```

Diese wird üblicherweise für Datenhaltung und Bindings im MVVM-Kontext genutzt und mit `INotifyPropertyChanged` ergänzt, um die UI zu aktualisieren.


- Spezielle Properties mit erweitertem Funktionsumfang (Binding, Animation, Styling).

```csharp
public static readonly DependencyProperty IstAktivProperty =
    DependencyProperty.Register("IstAktiv", typeof(bool), typeof(MyControl));

public bool IstAktiv
{
    get => (bool)GetValue(IstAktivProperty);
    set => SetValue(IstAktivProperty, value);
}
```

---

## 10. Triggers

- Ermöglichen visuelle Änderungen basierend auf Zuständen.

```xaml
<Style TargetType="Button">
    <Style.Triggers>
        <Trigger Property="IsMouseOver" Value="True">
            <Setter Property="Background" Value="LightBlue" />
        </Trigger>
    </Style.Triggers>
</Style>
```

---

## 11. Events

- Ereignisse können im Code-Behind behandelt werden.

```xaml
<Button Content="Klick" Click="Button_Click" />
```

```csharp
private void Button_Click(object sender, RoutedEventArgs e)
{
    MessageBox.Show("Geklickt!");
}
```

---

## 12. Value Converters

- Wandeln Werte bei der Bindung um (z. B. bool → Sichtbarkeit).

```csharp
public class BoolToVisibilityConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (bool)value ? Visibility.Visible : Visibility.Collapsed;
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
        return (Visibility)value == Visibility.Visible;
    }
}
```

```xaml
<Window.Resources>
    <local:BoolToVisibilityConverter x:Key="BoolToVis" />
</Window.Resources>

<TextBlock Text="Online" Visibility="{Binding IstOnline, Converter={StaticResource BoolToVis}}" />
```

---

## 13. Navigation

- Innerhalb eines `Frame`-Elements können `Page`-Instanzen geladen werden.

```xaml
<Frame x:Name="MainFrame" />
```

```csharp
MainFrame.Navigate(new Uri("Pages/Startseite.xaml", UriKind.Relative));
```

---

## 14. User Controls

- Eigene wiederverwendbare UI-Komponenten

```xaml
<UserControl x:Class="MyApp.Controls.Kopfzeile">
    <Grid>
        <TextBlock Text="Titel" FontSize="20" />
    </Grid>
</UserControl>
```

```xaml
<local:Kopfzeile />
```

---

## 15. DataGrid/ListView

- Anzeige von Datensätzen in tabellarischer Form mit Spalten.

```xaml
<DataGrid ItemsSource="{Binding BenutzerListe}" AutoGenerateColumns="False">
    <DataGrid.Columns>
        <DataGridTextColumn Header="Name" Binding="{Binding Name}" />
        <DataGridTextColumn Header="Alter" Binding="{Binding Alter}" />
    </DataGrid.Columns>
</DataGrid>
```

---

## 16. Visual Tree vs. Logical Tree

- **Logical Tree**: Beschreibt die hierarchische Struktur von UI-Elementen aus Sicht von XAML und Datenbindung.
- **Visual Tree**: Repräsentiert alle tatsächlichen visuellen Elemente, inklusive untergeordneter Komponenten (z. B. Border, TextBlock innerhalb eines Button-Templates).

### Beispiel: Logical Tree
```xaml
<StackPanel>
    <Button Content="Klick mich" />
</StackPanel>
```
- Logical Tree: StackPanel → Button
- Wird z. B. für Ressourcen, DataContext-Vererbung oder Events verwendet

### Beispiel: Visual Tree (vereinfacht)
```xaml
<Button Content="Klick mich" />
```
- Visual Tree (intern): Button → Border → ContentPresenter → TextBlock
- Diese Struktur entsteht durch das Standard-ControlTemplate des Buttons

### Beispiel zur Inspektion des Visual Tree in C#
```csharp
var child = VisualTreeHelper.GetChild(buttonElement, 0); // z. B. Border
var grandChild = VisualTreeHelper.GetChild(child, 0);    // z. B. ContentPresenter
```
- VisualTreeHelper wird z. B. für Custom Controls, Visual State Management oder Fokussteuerung genutzt

- Der Button besteht visuell aus mehreren internen Komponenten (z. B. Border, ContentPresenter, TextBlock), die im Visual Tree sichtbar sind.
- Zugriff erfolgt z. B. mit `VisualTreeHelper`:

```csharp
DependencyObject parent = VisualTreeHelper.GetParent(element);
```

---
