---
title: Grid
description: Viste presente nelle griglie.
ms.topic: article
ms.prod: xamarin
ms.assetid: 762B1802-D185-494C-B643-74EED55882FE
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/26/2017
ms.openlocfilehash: 18df6082f634d633d3f1ca8ea1e8d3f493532f47
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="grid"></a>Grid

[`Grid`](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/) supporta la disposizione delle viste in righe e colonne. Dimensioni proporzionale o dimensioni assolute, è possono impostare le righe e colonne. Il `Grid` layout non deve essere confuso con tabelle tradizionali e non deve presentare i dati tabulari. `Grid` non ha il concetto di riga, colonna o cella formattazione. A differenza delle tabelle HTML, `Grid` è destinato esclusivamente per definire il layout del contenuto.

[![](grid-images/layouts-sml.png "Xamarin.Forms Layouts")](grid-images/layouts.png#lightbox "Xamarin.Forms Layouts")

In questo articolo illustra:

- **[Scopo](#Purpose)**  &ndash; utilizzi comuni di `Grid`.
- **[Utilizzo](#Usage)**  &ndash; illustrato come utilizzare `Grid` per ottenere la progettazione desiderata.
  - **[Righe e colonne](#Rows_and_Columns)**  &ndash; specificare righe e colonne per la `Grid`.
  - **[Inserimento di viste](#Placing_Views)**  &ndash; aggiungere visualizzazioni a griglia in righe e colonne specifiche.
  - **[Spaziatura](#Spacing)**  &ndash; configurare gli spazi tra le righe e colonne.
  - **[Intervalli](#Spans)**  &ndash; configurare gli elementi di estendersi su più righe o colonne.

![](grid-images/grid.png "Esplorazione della griglia")

## <a name="purpose"></a>Scopo

`Grid` può essere utilizzato per organizzare le visualizzazioni in una griglia. Ciò risulta utile in molti casi:

- Disposizione di pulsanti di un'app calcolatrice
- La disposizione dei pulsanti le scelte in una griglia, ad esempio il iOS o Android schermate home
- Disposizione di visualizzazioni in modo che siano delle stesse dimensioni in una dimensione (come nel alcune barre degli strumenti)

## <a name="usage"></a>Utilizzo

A differenza delle tabelle tradizionali, `Grid` non deduce il numero e dimensioni di righe e colonne dal contenuto. In alternativa, `Grid` è `RowDefinitions` e `ColumnDefinitions` raccolte. Questi blocchi contengono le definizioni di verranno disposti come numero di righe e colonne. Le visualizzazioni vengono aggiunte al `Grid` con indici di colonna e riga specificata, che identifica la riga e colonna deve essere inserita in una vista.

<a name="Rows_and_Columns" />

### <a name="rows-and-columns"></a>Righe e colonne

Informazioni di riga e di colonna vengono archiviate in `Grid`del `RowDefinitions`  &  `ColumnDefinitions` proprietà, ovvero ogni raccolte di [ `RowDefinition` ](https://developer.xamarin.com/api/type/Xamarin.Forms.RowDefinition/) e [ `ColumnDefinition` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ColumnDefinition/)oggetti, rispettivamente. `RowDefinition` ha una singola proprietà, `Height`, e `ColumnDefinition` ha una singola proprietà `Width`. Le opzioni per l'altezza e larghezza sono come segue:

- **Auto** &ndash; automaticamente le dimensioni in base al contenuto della riga o colonna. Specificato come [ `GridUnitType.Auto` ](https://developer.xamarin.com/api/type/Xamarin.Forms.GridUnitType/) in c# o come `Auto` in XAML.
- **Proportional(*)** &ndash; dimensioni di righe e colonne come una percentuale di spazio rimanente. Specifica un valore e `GridUnitType.Star` in c# e come `#*` in XAML, con `#` il valore desiderato. Specifica una riga o colonna con `*` indurla a riempire lo spazio disponibile.
- **Assoluto** &ndash; ridimensiona le colonne e righe con valori di altezza e larghezza fissi di specifici. Specifica un valore e `GridUnitType.Absolute` in c# e come `#` in XAML, con `#` il valore desiderato.

> [!NOTE]
> I valori di larghezza per le colonne vengono impostati come ' * ' per impostazione predefinita in xamarin. Forms, che assicura che la colonna verrà riempire lo spazio disponibile.

Si consideri un'applicazione che richiede tre righe e due colonne. La riga inferiore deve essere esattamente 200px altezza e la riga superiore deve essere due volte la stessa altezza riga centrale. La colonna a sinistra deve essere sufficientemente ampio per adattarle al contenuto e la colonna di destra deve riempire lo spazio rimanente.

In XAML:

```xaml
<Grid>
  <Grid.RowDefinitions>
    <RowDefinition Height="2*" />
    <RowDefinition Height="*" />
    <RowDefinition Height="200" />
  </Grid.RowDefinitions>
  <Grid.ColumnDefinitions>
    <ColumnDefinition Width="Auto" />
    <ColumnDefinition Width="*" />
  </Grid.ColumnDefinitions>
</Grid>
```

In C#:

```csharp
var grid = new Grid();
grid.RowDefinitions.Add (new RowDefinition { Height = new GridLength(2, GridUnitType.Star) });
grid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
grid.RowDefinitions.Add (new RowDefinition { Height = new GridLength(200)});
grid.ColumnDefinitions.Add (new ColumnDefinition{ Width = new GridLength (200) });
```

<a name="Placing_Views" />

### <a name="placing-views-in-a-grid"></a>Inserimento di viste in una griglia

Per posizionare le viste in un `Grid` sarà necessario aggiungerli come elementi figlio nella griglia, quindi specificare la riga e colonna appartengono.

In XAML, utilizzare `Grid.Row` e `Grid.Column` in ogni vista singoli per specificare la selezione host. Si noti che `Grid.Row` e `Grid.Column` specificare posizione in base a elenchi di righe e colonne in base zero. Ciò significa che in una 4x4 griglia, nella cella superiore sinistra è (0,0) e nella cella inferiore destra è (3,3).

Il `Grid` illustrato di seguito contiene quattro celle:

![](grid-images/label-grid.png "Griglia con quattro viste")

In XAML:

```xaml
<Grid>
  <Grid.RowDefinitions>
    <RowDefinition Height="*" />
    <RowDefinition Height="*" />
  </Grid.RowDefinitions>
  <Grid.ColumnDefinitions>
    <ColumnDefinition Width="*" />
    <ColumnDefinition Width="*" />
  </Grid.ColumnDefinitions>
  <Label Text="Top Left" Grid.Row="0" Grid.Column="0" />
  <Label Text="Top Right" Grid.Row="0" Grid.Column="1" />
  <Label Text="Bottom Left" Grid.Row="1" Grid.Column="0" />
  <Label Text="Bottom Right" Grid.Row="1" Grid.Column="1" />
</Grid>
```

In C#:

```csharp
var grid = new Grid();

grid.RowDefinitions.Add(new RowDefinition { Height = new GridLength(1, GridUnitType.Star)});
grid.RowDefinitions.Add(new RowDefinition { Height = new GridLength(1, GridUnitType.Star)});
grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(1, GridUnitType.Star)});
grid.ColumnDefinitions.Add(new ColumnDefinition { Width = new GridLength(1, GridUnitType.Star)});

var topLeft = new Label { Text = "Top Left" };
var topRight = new Label { Text = "Top Right" };
var bottomLeft = new Label { Text = "Bottom Left" };
var bottomRight = new Label { Text = "Bottom Right" };

grid.Children.Add(topLeft, 0, 0);
grid.Children.Add(topRight, 0, 1);
grid.Children.Add(bottomLeft, 1, 0);
grid.Children.Add(bottomRight, 1, 1);
```

Il codice sopra riportato crea griglia con quattro etichette, due colonne e due righe. Si noti che ogni etichetta avrà le stesse dimensioni e che le righe si espandono per utilizzare tutto lo spazio disponibile.

Nell'esempio precedente, le visualizzazioni vengono aggiunte al [ `Grid.Children` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Grid.Children/) insieme utilizzando la [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid+IGridList%3CT%3E.Add/p/Xamarin.Forms.View/System.Int32/System.Int32/) overload che specifica gli argomenti a sinistro e dall'alto. Quando si utilizza il [ `Add` ](https://developer.xamarin.com/api/member/Xamarin.Forms.Grid+IGridList%3CT%3E.Add/p/Xamarin.Forms.View/System.Int32/System.Int32/System.Int32/System.Int32/) overload che specifichi a sinistra, destra, superiore e inferiore argomenti, mentre a sinistra e argomenti superiore verranno sempre fare riferimento alle celle all'interno di [ `Grid` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/), destra e gli argomenti nella parte inferiore è ammesso per fare riferimento alle celle che si trovano all'esterno di `Grid`. In questo modo l'argomento a destra deve essere sempre maggiore di argomento a sinistra e l'argomento inferiore deve essere sempre maggiore dell'argomento principale. Nell'esempio seguente viene illustrato il codice equivalente utilizzando sia `Add` Overload:

```csharp
// left, top
grid.Children.Add(topLeft, 0, 0);
grid.Children.Add(topRight, 1, 0);
grid.Children.Add(bottomLeft, 0, 1);
grid.Children.Add(bottomRight, 1, 1);

// left, right, top, bottom
grid.Children.Add(topLeft, 0, 1, 0, 1);
grid.Children.Add(topRight, 1, 2, 0, 1);
grid.Children.Add(bottomLeft, 0, 1, 1, 2);
grid.Children.Add(bottomRight, 1, 2, 1, 2);
```

### <a name="spacing"></a>Spacing (Spaziatura)

`Grid` proprietà per controllare la spaziatura tra le righe e colonne.  Le proprietà seguenti sono disponibili per la personalizzazione di `Grid`:

- **Spaziatura** &ndash; la quantità di spazio tra le colonne.
- **RowSpacing** &ndash; la quantità di spazio tra righe.

Il codice XAML seguente specifica un `Grid` con due colonne, una riga e 5 px di spaziatura tra le colonne:

```xaml
<Grid ColumnSpacing="5">
  <Grid.ColumnDefinitions>
    <ColumnDefinitions Width="*" />
    <ColumnDefinitions Width="*" />
  </Grid.ColumnDefinitions>
</Grid>
```

In C#:

```csharp
var grid = new Grid { ColumnSpacing = 5 };
grid.ColumnDefnitions.Add(new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star)});
grid.ColumnDefnitions.Add(new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star)});
```

### <a name="spans"></a>Intervalli

Spesso quando si utilizza una griglia, vi sia un elemento che deve occupare più di una riga o colonna. Si consideri una semplice applicazione Calcolatrice:

![](grid-images/calculator.png "Applicazione Calulator")

Si noti che il pulsante 0 si estende su due colonne, come nei calcolatori incorporati per ogni piattaforma. Questa operazione viene eseguita utilizzando il `ColumnSpan` proprietà che specifica il numero di colonne un elemento deve occupare. Il codice XAML per tale pulsante:

```xaml
<Button Text = "0" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" />
```

E in c#:

```csharp
Button zeroButton = new Button { Text = "0" };
controlGrid.Children.Add (zeroButton, 0, 4);
Grid.SetColumnSpan (zeroButton, 2);
```

Si noti che nel codice, i metodi statici del `Grid` classe vengono utilizzate per eseguire modifiche di posizionamento incluse le modifiche di `ColumnSpan` e `RowSpan`. Si noti che, a differenza di altre proprietà che è possibile impostare in qualsiasi momento, le proprietà impostate tramite i metodi statici devono già essere nella griglia prima della modifica.

Il codice XAML completo per l'app calcolatrice precedente è il seguente:

```xaml
<?xml version="1.0" encoding="UTF-8"?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
x:Class="LayoutSamples.CalculatorGridXAML"
Title = "Calculator - XAML"
BackgroundColor="#404040">
    <ContentPage.Resources>
        <ResourceDictionary>
            <Style x:Key="plainButton" TargetType="Button">
                <Setter Property="BackgroundColor" Value="#eee"/>
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="BorderRadius" Value="0"/>
                <Setter Property="FontSize" Value="40" />
            </Style>
            <Style x:Key="darkerButton" TargetType="Button">
                <Setter Property="BackgroundColor" Value="#ddd"/>
                <Setter Property="TextColor" Value="Black" />
                <Setter Property="BorderRadius" Value="0"/>
                <Setter Property="FontSize" Value="40" />
            </Style>
            <Style x:Key="orangeButton" TargetType="Button">
                <Setter Property="BackgroundColor" Value="#E8AD00"/>
                <Setter Property="TextColor" Value="White" />
                <Setter Property="BorderRadius" Value="0"/>
                <Setter Property="FontSize" Value="40" />
            </Style>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentPage.Content>
        <Grid x:Name="controlGrid" RowSpacing="1" ColumnSpacing="1">
            <Grid.RowDefinitions>
                <RowDefinition Height="150" />
                <RowDefinition Height="*" />
                <RowDefinition Height="*" />
                <RowDefinition Height="*" />
                <RowDefinition Height="*" />
                <RowDefinition Height="*" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>
            <Label Text="0" Grid.Row="0" HorizontalTextAlignment="End" VerticalTextAlignment="End" TextColor="White"
        FontSize="60" Grid.ColumnSpan="4" />
            <Button Text = "C" Grid.Row="1" Grid.Column="0"
        Style="{StaticResource darkerButton}" />
            <Button Text = "+/-" Grid.Row="1" Grid.Column="1"
        Style="{StaticResource darkerButton}" />
            <Button Text = "%" Grid.Row="1" Grid.Column="2"
        Style="{StaticResource darkerButton}" />
            <Button Text = "div" Grid.Row="1" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
            <Button Text = "7" Grid.Row="2" Grid.Column="0"
        Style="{StaticResource plainButton}" />
            <Button Text = "8" Grid.Row="2" Grid.Column="1"
        Style="{StaticResource plainButton}" />
            <Button Text = "9" Grid.Row="2" Grid.Column="2"
        Style="{StaticResource plainButton}" />
            <Button Text = "X" Grid.Row="2" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
            <Button Text = "4" Grid.Row="3" Grid.Column="0"
        Style="{StaticResource plainButton}" />
            <Button Text = "5" Grid.Row="3" Grid.Column="1"
        Style="{StaticResource plainButton}" />
            <Button Text = "6" Grid.Row="3" Grid.Column="2"
        Style="{StaticResource plainButton}" />
            <Button Text = "-" Grid.Row="3" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
            <Button Text = "1" Grid.Row="4" Grid.Column="0"
        Style="{StaticResource plainButton}" />
            <Button Text = "2" Grid.Row="4" Grid.Column="1"
        Style="{StaticResource plainButton}" />
            <Button Text = "3" Grid.Row="4" Grid.Column="2"
        Style="{StaticResource plainButton}" />
            <Button Text = "+" Grid.Row="4" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
            <Button Text = "0" Grid.ColumnSpan="2"
        Grid.Row="5" Grid.Column="0" Style="{StaticResource plainButton}" />
            <Button Text = "." Grid.Row="5" Grid.Column="2"
        Style="{StaticResource plainButton}" />
            <Button Text = "=" Grid.Row="5" Grid.Column="3"
        Style="{StaticResource orangeButton}" />
        </Grid>
    </ContentPage.Content>
</ContentPage>
```

Si noti che l'etichetta nella parte superiore della griglia sia zero pulsante occuping più di una colonna. Sebbene un layout simile può essere ottenuto usando griglie nidificate, di `ColumnSpan`  &  `RowSpan` approccio è più semplice.

L'implementazione c#:

```csharp
public CalculatorGridCode ()
{
  Title = "Calculator - C#";
  BackgroundColor = Color.FromHex ("#404040");

  var plainButton = new Style (typeof(Button)) {
    Setters = {
      new Setter { Property = Button.BackgroundColorProperty, Value = Color.FromHex ("#eee") },
      new Setter { Property = Button.TextColorProperty, Value = Color.Black },
      new Setter { Property = Button.BorderRadiusProperty, Value = 0 },
      new Setter { Property = Button.FontSizeProperty, Value = 40 }
    }
  };
  var darkerButton = new Style (typeof(Button)) {
    Setters = {
      new Setter { Property = Button.BackgroundColorProperty, Value = Color.FromHex ("#ddd") },
      new Setter { Property = Button.TextColorProperty, Value = Color.Black },
      new Setter { Property = Button.BorderRadiusProperty, Value = 0 },
      new Setter { Property = Button.FontSizeProperty, Value = 40 }
    }
  };
  var orangeButton = new Style (typeof(Button)) {
    Setters = {
      new Setter { Property = Button.BackgroundColorProperty, Value = Color.FromHex ("#E8AD00") },
      new Setter { Property = Button.TextColorProperty, Value = Color.White },
      new Setter { Property = Button.BorderRadiusProperty, Value = 0 },
      new Setter { Property = Button.FontSizeProperty, Value = 40 }
    }
  };

  var controlGrid = new Grid { RowSpacing = 1, ColumnSpacing = 1 };
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (150) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });
  controlGrid.RowDefinitions.Add (new RowDefinition { Height = new GridLength (1, GridUnitType.Star) });

  controlGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
  controlGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
  controlGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });
  controlGrid.ColumnDefinitions.Add (new ColumnDefinition { Width = new GridLength (1, GridUnitType.Star) });

  var label = new Label {
    Text = "0",
    HorizontalTextAlignment = TextAlignment.End,
    VerticalTextAlignment = TextAlignment.End,
    TextColor = Color.White,
    FontSize = 60
  };
  controlGrid.Children.Add (label, 0, 0);

  Grid.SetColumnSpan (label, 4);

  controlGrid.Children.Add (new Button { Text = "C", Style = darkerButton }, 0, 1);
  controlGrid.Children.Add (new Button { Text = "+/-", Style = darkerButton }, 1, 1);
  controlGrid.Children.Add (new Button { Text = "%", Style = darkerButton }, 2, 1);
  controlGrid.Children.Add (new Button { Text = "div", Style = orangeButton }, 3, 1);
  controlGrid.Children.Add (new Button { Text = "7", Style = plainButton }, 0, 2);
  controlGrid.Children.Add (new Button { Text = "8", Style = plainButton }, 1, 2);
  controlGrid.Children.Add (new Button { Text = "9", Style = plainButton }, 2, 2);
  controlGrid.Children.Add (new Button { Text = "X", Style = orangeButton }, 3, 2);
  controlGrid.Children.Add (new Button { Text = "4", Style = plainButton }, 0, 3);
  controlGrid.Children.Add (new Button { Text = "5", Style = plainButton }, 1, 3);
  controlGrid.Children.Add (new Button { Text = "6", Style = plainButton }, 2, 3);
  controlGrid.Children.Add (new Button { Text = "-", Style = orangeButton }, 3, 3);
  controlGrid.Children.Add (new Button { Text = "1", Style = plainButton }, 0, 4);
  controlGrid.Children.Add (new Button { Text = "2", Style = plainButton }, 1, 4);
  controlGrid.Children.Add (new Button { Text = "3", Style = plainButton }, 2, 4);
  controlGrid.Children.Add (new Button { Text = "+", Style = orangeButton }, 3, 4);
  controlGrid.Children.Add (new Button { Text = ".", Style = plainButton }, 2, 5);
  controlGrid.Children.Add (new Button { Text = "=", Style = orangeButton }, 3, 5);

  var zeroButton = new Button { Text = "0", Style = plainButton };
  controlGrid.Children.Add (zeroButton, 0, 5);
  Grid.SetColumnSpan (zeroButton, 2);

  Content = controlGrid;
}
```


## <a name="related-links"></a>Collegamenti correlati

- [Creazione di App per dispositivi mobili con xamarin. Forms, capitolo 17](https://developer.xamarin.com/r/xamarin-forms/book/chapter17.pdf)
- [Griglia](https://developer.xamarin.com/api/type/Xamarin.Forms.Grid/)
- [Layout (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Layout/)
- [Esempio BusinessTumble (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/BusinessTumble/)
