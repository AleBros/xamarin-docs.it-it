---
title: Markup C# di Novell. Forms
description: Il markup C# è un set esplicito di metodi e classi helper Fluent per semplificare il processo di compilazione di interfacce utente Novell. Forms dichiarative in C#.
ms.prod: xamarin
ms.assetid: D41B9DCD-5C34-4C2F-B177-FC082AB2E9E0
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/25/2020
ms.openlocfilehash: fa758b1240570f90ebf8a723401176f6be9dd6ac
ms.sourcegitcommit: 8d13d2262d02468c99c4e18207d50cd82275d233
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82532611"
---
# <a name="xamarinforms-c-markup"></a>Markup C# di Novell. Forms

![](~/media/shared/preview.png "This API is currently pre-release")

[![Scaricare l'](~/media/shared/download.png) esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-csharpmarkupdemos/)

Il markup C# è un set esplicito di metodi e classi helper Fluent per semplificare il processo di compilazione di interfacce utente Novell. Forms dichiarative in C#. L'API Fluent fornita dal markup C# è disponibile nello `Xamarin.Forms.Markup` spazio dei nomi.

Analogamente a XAML, il markup C# consente una netta separazione tra il markup dell'interfaccia utente e la logica dell'interfaccia utente. Questa operazione può essere eseguita separando il markup dell'interfaccia utente e la logica dell'interfaccia utente in file di classe parziali distinti. Per una pagina di accesso, ad esempio, il markup dell'interfaccia utente si trova in un file denominato *LoginPage.cs*, mentre la logica dell'interfaccia utente si trova in un file denominato *LoginPage.Logic.cs*.

Il markup C# è disponibile da Novell. Forms 4,6. Tuttavia, è attualmente sperimentale e può essere usata solo aggiungendo la riga di codice seguente al file *app.cs* :

```csharp
Device.SetFlags(new string[]{ "Markup_Experimental" });
```

> [!NOTE]
> Il markup C# è disponibile in tutte le piattaforme supportate da Novell. Forms.

## <a name="basic-example"></a>Esempio di base

Nell'esempio seguente viene illustrata [`Entry`](xref:Xamarin.Forms.Entry) la creazione di un oggetto in C#:

```csharp
Entry entry = new Entry
{
    Placeholder = "Enter number",
    Keyboard = Keyboard.Numeric,
    BackgroundColor = Color.AliceBlue,
    TextColor = Color.Black,
    FontSize = 15,
    HeightRequest = 44,
    Margin = fieldMargin
};
entry.SetBinding(Entry.TextProperty, new Binding("RegistrationCode", BindingMode.TwoWay));
grid.Children.Add(entry, 0, 2);
Grid.SetColumnSpan(entry, 2);
```

Questo esempio crea un [`Entry`](xref:Xamarin.Forms.Entry) oggetto che i dati vengono associati alla `RegistrationCode` proprietà di ViewModel, usando un' `TwoWay` associazione. È impostato per essere visualizzato in una riga specifica in un [`Grid`](xref:Xamarin.Forms.Grid)oggetto e si estende a tutte le colonne `Grid`nell'oggetto. Viene inoltre impostata l'altezza dell'oggetto `Entry` , insieme alle dimensioni del carattere del testo e alla relativa `Margin`proprietà.

Il markup C# consente di riscrivere il codice usando l'API Fluent:

```csharp
using Xamarin.Forms.Markup;
using static Xamarin.Forms.Markup.GridRowsColumns;

Entry entry = new Entry { Placeholder = "Enter number", Keyboard = Keyboard.Numeric, BackgroundColor = Color.AliceBlue, TextColor = Color.Black } .Font (15)
                         .Row (BodyRow.CodeEntry) .ColumnSpan (All<BodyCol>()) .Margin (fieldMargin) .Height (44)
                         .Bind (nameof(vm.RegistrationCode), BindingMode.TwoWay);
```

Questo esempio è identico all'esempio precedente, ma l'API di markup Fluent C# semplifica il processo di compilazione dell'interfaccia utente in C#.

> [!NOTE]
> Il markup C# include metodi di estensione che impostano proprietà di visualizzazione specifiche. Questi metodi di estensione non hanno lo scopo di sostituire tutti i setter di proprietà. Sono invece progettate per migliorare la leggibilità del codice e possono essere usate in combinazione con i setter di proprietà. È consigliabile usare sempre un metodo di estensione quando ne esiste uno per una proprietà, ma è possibile scegliere il saldo preferito.

## <a name="data-binding"></a>Associazione dati

Il markup C# include `Bind` un metodo di estensione, insieme agli overload, che crea un data binding tra una proprietà View associable e una proprietà specificata. Il `Bind` metodo conosce la proprietà associabile predefinita per la maggior parte dei controlli inclusi in Novell. Forms. Pertanto, in genere non è necessario specificare la proprietà di destinazione quando si usa questo metodo. Tuttavia, è anche possibile registrare la proprietà associabile predefinita per altri controlli:

```csharp
using Xamarin.Forms.Markup;
//...

DefaultBindableProperties.Register(HoverButton.CommandProperty, RadialGauge.ValueProperty);
```

Il `Bind` metodo può essere utilizzato per eseguire l'associazione a qualsiasi proprietà associabile:

```csharp
using Xamarin.Forms.Markup;
// ...

new Label { Text = "No data available" }
           .Bind (Label.IsVisibleProperty, nameof(vm.Empty))
```

Inoltre, il `BindCommand` metodo di estensione può essere associato alle proprietà e `Command` `CommandParameter` predefinite di un controllo in una singola chiamata al metodo:

```csharp
using Xamarin.Forms.Markup;
// ...

new TextCell { Text = "Tap me" }
              .BindCommand (nameof(vm.TapCommand))
```

Per impostazione predefinita, `CommandParameter` l'oggetto è associato al contesto di associazione. È inoltre possibile specificare il percorso e l'origine del binding `Command` per e `CommandParameter` le associazioni:

```csharp
using Xamarin.Forms.Markup;
// ...

new TextCell { Text = "Tap Me" }
              .BindCommand (nameof(vm.TapCommand), vm, nameof(Item.Id))
```

In questo esempio, il contesto di associazione è `Item` un'istanza di, pertanto non è necessario specificare un'origine per `Id` `CommandParameter` l'associazione.

Se è necessario eseguire l'associazione solo `Command`a, è possibile `null` passare all' `parameterPath` argomento del `BindCommand` metodo. In alternativa, usare il `Bind` metodo.

È anche possibile registrare le proprietà `Command` predefinite `CommandParameter` e per altri controlli:

```csharp
using Xamarin.Forms.Markup;
//...

DefaultBindableProperties.RegisterCommand(
    (CustomViewA.CommandProperty, CustomViewA.CommandParameterProperty),
    (CustomViewB.CommandProperty, CustomViewB.CommandParameterProperty)
);
```

Il codice del convertitore inline può essere passato `Bind` nel metodo con `convert` i `convertBack` parametri e:

```csharp
using Xamarin.Forms.Markup;
//...

new Label { Text = "Tree" }
           .Bind (Label.MarginProperty, nameof(TreeNode.TreeDepth),
                  convert: (int depth) => new Thickness(depth * 20, 0, 0, 0))
```

Sono supportati anche i parametri del convertitore di tipi Safe:

```csharp
using Xamarin.Forms.Markup;
//...

new Label { }
           .Bind (nameof(viewModel.Text),
                  convert: (string text, int repeat) => string.Concat(Enumerable.Repeat(text, repeat)))
```

Inoltre, è possibile riutilizzare il codice e le istanze del convertitore con `FuncConverter` la classe:

```csharp
using Xamarin.Forms.Markup;
//...

FuncConverter<int, Thickness> treeMarginConverter = new FuncConverter<int, Thickness>(depth => new Thickness(depth * 20, 0, 0, 0));
new Label { Text = "Tree" }
           .Bind (Label.MarginProperty, nameof(TreeNode.TreeDepth), converter: treeMarginConverter),
```

La `FuncConverter` classe supporta `CultureInfo` anche gli oggetti:

```csharp
using Xamarin.Forms.Markup;
//...

cultureAwareConverter = new FuncConverter<DateTimeOffset, string, int>(
    (date, daysToAdd, culture) => date.AddDays(daysToAdd).ToString(culture)
);
```

È anche possibile associare dati a `Span` oggetti specificati con la `FormattedText` proprietà:

```csharp
using Xamarin.Forms.Markup;
//...

new Label { } .FormattedText (
    new Span { Text = "Built with " },
    new Span { TextColor = Color.Blue, TextDecorations = TextDecorations.Underline }
              .BindTapGesture (nameof(vm.ContinueToCSharpForMarkupCommand))
              .Bind (nameof(vm.Title))
)
```

### <a name="gesture-recognizers"></a>Strumenti di riconoscimento dei movimenti

`Command`le `CommandParameter` proprietà e possono essere dati associati `GestureElement` ai `View` tipi e usando `BindClickGesture`i `BindSwipeGesture`metodi di `BindTapGesture` estensione, e:

```csharp
using Xamarin.Forms.Markup;
//...

new Label { Text = "Tap Me" }
           .BindTapGesture (nameof(vm.TapCommand))
```

Questo esempio crea un riconoscitore di movimento del tipo specificato e lo aggiunge all'oggetto [`Label`](xref:Xamarin.Forms.Label). I `Bind*Gesture` metodi di estensione offrono gli stessi parametri dei `BindCommand` metodi di estensione. Per impostazione predefinita `Bind*Gesture` , tuttavia, non `CommandParameter`viene associato `BindCommand` , mentre fa.

Per inizializzare un riconoscitore di movimento con i `ClickGesture`parametri `PanGesture`, `PinchGesture`usare `SwipeGesture`i metodi `TapGesture` di estensione,,, e:

```csharp
using Xamarin.Forms.Markup;
//...

new Label { Text = "Tap Me" }
           .TapGesture (g => g.Bind(nameof(vm.DoubleTapCommand)).NumberOfTapsRequired = 2)
```

Poiché un riconoscitore di movimento `BindableObject`è un, è possibile `Bind` usare `BindCommand` i metodi di estensione e quando lo si inizializza. È anche possibile inizializzare tipi di Riconoscitore di `Gesture<TGestureElement, TGestureRecognizer>` movimenti personalizzati con il metodo di estensione.

## <a name="layout"></a>Layout

Il markup C# include una serie di metodi di estensione del layout che supportano le visualizzazioni di posizionamento nei layout e il contenuto delle visualizzazioni:

| Type | Metodi di estensione |
|---|---|
| `FlexLayout` | `AlignSelf`, `Basis`, `Grow`, `Menu`, `Order`, `Shrink` |
| `Grid` | `Row`, `Column`, `RowSpan`, `ColumnSpan` |
| `Label` | `TextLeft`, `TextCenterHorizontal`, `TextRight` <br/> `TextTop`, `TextCenterVertical`, `TextBottom` <br/> `TextCenter` |
| `Layout` | `Padding`, `Paddings` |
| `LayoutOptions` | `Left`, `CenterHorizontal`, `FillHorizontal`, `Right` <br/> `LeftExpand`, `CenterExpandHorizontal`, `FillExpandHorizontal`, `RightExpand` <br /> `Top`, `Bottom`, `CenterVertical`, `FillVertical` <br /> `TopExpand`, `BottomExpand`, `CenterExpandVertical`, `FillExpandVertical` <br /> `Center`, `Fill`, `CenterExpand`, `FillExpand` |
| `View` | `Margin`, `Margins` |
| `VisualElement` | `Height`, `Width`, `MinHeight`, `MinWidth`, `Size`, `MinSize` |

### <a name="left-to-right-and-right-to-left-support"></a>Supporto da sinistra a destra e da destra a sinistra

Per il markup C# progettato per supportare la direzione del flusso da sinistra a destra (LTR) o da destra a sinistra (RTL), i metodi di estensione elencati sopra offrono il set di nomi più intuitivo, `Left`ovvero `Right`, `Top` e `Bottom`.

Per rendere disponibile il set corretto di metodi di estensione Left e Right e nel processo rendere esplicita la direzione del flusso per la quale è stato progettato il markup, includere una `using` delle due direttive `using Xamarin.Forms.Markup.LeftToRight;`seguenti `using Xamarin.Forms.Markup.RightToLeft;`: o.

Per il markup C# progettato per supportare la direzione del flusso da sinistra a destra e da destra a sinistra, è consigliabile usare i metodi di estensione nella tabella seguente anziché uno degli spazi dei nomi precedenti:

| Type | Metodi di estensione |
|---|---|
| `Label` | `TextStart`, `TextEnd` |
| `LayoutOptions` | `Start`, `End` <br/> `StartExpand`, `EndExpand` |

### <a name="layout-line-convention"></a>Convenzione della linea di layout

La convenzione consigliata consiste nell'inserire tutti i metodi di estensione del layout per una vista su una sola riga nell'ordine seguente:

1. Riga e colonna che contengono la visualizzazione.
1. Allineamento all'interno della riga e della colonna.
1. Margini attorno alla visualizzazione.
1. Dimensioni visualizzazione.
1. Spaziatura interna nella visualizzazione.
1. Allineamento del contenuto all'interno della spaziatura interna.

Il codice seguente illustra un esempio di questa convenzione:

```csharp
new Label { }
           .Row (BodyRow.Prompt) .ColumnSpan (All<BodyCol>()) .FillExpandHorizontal () .CenterVertical () .Margin (fieldNameMargin) .TextCenterHorizontal () // Layout line
```

Coerentemente seguendo la convenzione è possibile leggere rapidamente il markup C# e creare una mappa mentale in cui si trova il contenuto della visualizzazione nell'interfaccia utente.

## <a name="grid-rows-and-columns"></a>Righe e colonne di Grid

Le enumerazioni possono essere utilizzate per [`Grid`](xref:Xamarin.Forms.Grid) definire righe e colonne anziché utilizzare numeri. Questo offre il vantaggio che la rinumerazione non è necessaria per l'aggiunta o la rimozione di righe o colonne.

> [!IMPORTANT]
> Per [`Grid`](xref:Xamarin.Forms.Grid) la definizione di righe e colonne con enumerazioni `using` è necessaria la direttiva seguente:`using static Xamarin.Forms.Markup.GridRowsColumns;`

Nell'esempio di codice riportato di seguito viene illustrato come definire e [`Grid`](xref:Xamarin.Forms.Grid) utilizzare righe e colonne utilizzando enumerazioni:

```csharp
using Xamarin.Forms.Markup;
using Xamarin.Forms.Markup.LeftToRight;
using static Xamarin.Forms.Markup.GridRowsColumns;
// ...

enum BodyRow
{
    Prompt,
    CodeHeader,
    CodeEntry,
    Button
}

enum BodyCol
{
    FieldLabel,
    FieldValidation
}

View Build() => new Grid
{
    RowDefinitions = Rows.Define(
        (BodyRow.Prompt    , 170 ),
        (BodyRow.CodeHeader, 75  ),
        (BodyRow.CodeEntry , Auto),
        (BodyRow.Button    , Auto)
    ),

    ColumnDefinitions = Columns.Define(
        (BodyCol.FieldLabel     , 160 ),
        (BodyCol.FieldValidation, Star)
    ),

    Children =
    {
        new Label { LineBreakMode = LineBreakMode.WordWrap } .Font (15) .Bold ()
                   .Row (BodyRow.Prompt) .ColumnSpan (All<BodyCol>()) .FillExpandHorizontal () .CenterVertical () .Margin (fieldNameMargin) .TextCenterHorizontal ()
                   .Bind (nameof(vm.RegistrationPrompt)),

        new Label { Text = "Registration code" } .Bold ()
                   .Row (BodyRow.CodeHeader) .Column(BodyCol.FieldLabel) .Bottom () .Margin (fieldNameMargin),

        new Label { } .Italic ()
                   .Row (BodyRow.CodeHeader) .Column (BodyCol.FieldValidation) .Right () .Bottom () .Margin (fieldNameMargin)
                   .Bind (nameof(vm.RegistrationCodeValidationMessage)),

        new Entry { Placeholder = "E.g. 123456", Keyboard = Keyboard.Numeric, BackgroundColor = Color.AliceBlue, TextColor = Color.Black } .Font (15)
                   .Row (BodyRow.CodeEntry) .ColumnSpan (All<BodyCol>()) .Margin (fieldMargin) .Height (44)
                   .Bind (nameof(vm.RegistrationCode), BindingMode.TwoWay),

        new Button { Text = "Verify" } .Style (FilledButton)
                    .Row (BodyRow.Button) .ColumnSpan (All<BodyCol>()) .FillExpandHorizontal () .Margin (PageMarginSize)
                    .Bind (Button.IsVisibleProperty, nameof(vm.CanVerifyRegistrationCode))
                    .Bind (nameof(vm.VerifyRegistrationCodeCommand)),
    }
};
```

Inoltre, è possibile definire concisamente righe e colonne senza enumerazioni:

```csharp
new Grid
{
    RowDefinitions = Rows.Define (Auto, Star, 20),
    ColumnDefinitions = Columns.Define (Auto, Star, 20, 40)
    // ...
}
```

## <a name="fonts"></a>Tipi di carattere

I controlli nell'elenco seguente possono chiamare `FontSize`i metodi di `Bold`estensione `Italic`,, `Font` e per impostare l'aspetto del testo visualizzato dal controllo:

- `Button`
- `DatePicker`
- `Editor`
- `Entry`
- `Label`
- `Picker`
- `SearchBar`
- `Span`
- `TimePicker`

## <a name="effects"></a>Effetti

Gli effetti possono essere collegati ai controlli con `Effect` il metodo di estensione:

```csharp
using Xamarin.Forms.Markup;
// ...

new Button { Text = "Tap Me" }
            .Effects (new ButtonMixedCaps())
```

## <a name="logic-integration"></a>Integrazione logica

Il `Invoke` metodo di estensione può essere usato per eseguire il codice inline nel markup C#:

```csharp
using Xamarin.Forms.Markup;
// ...

new ListView { } .Invoke (l => l.ItemTapped += OnListViewItemTapped)
```

Inoltre, è possibile usare il `Assign` metodo di estensione per accedere a un controllo dall'esterno del markup dell'interfaccia utente (nel file di logica dell'interfaccia utente):

```csharp
using Xamarin.Forms.Markup;
// ...

new ListView { } .Assign (out MyListView)
```

## <a name="styles"></a>Stili

Nell'esempio seguente viene illustrato come creare stili impliciti ed espliciti utilizzando il markup C#:

```csharp
using Xamarin.Forms.Markup;
using Xamarin.Forms;

namespace CSharpForMarkupDemos
{
    public static class Styles
    {
        static Style<Button> buttons, filledButton;
        static Style<Label> labels;
        static Style<Span> link;

        #region Implicit styles

        public static ResourceDictionary Implicit => new ResourceDictionary { Buttons, Labels };

        public static Style<Button> Buttons => buttons ?? (buttons = new Style<Button>(
            (Button.HeightRequestProperty, 44),
            (Button.FontSizeProperty, 13),
            (Button.HorizontalOptionsProperty, LayoutOptions.Center),
            (Button.VerticalOptionsProperty, LayoutOptions.Center)
        ));

        public static Style<Label> Labels => labels ?? (labels = new Style<Label>(
            (Label.FontSizeProperty, 13),
            (Label.TextColorProperty, Color.Black)
        ));

        #endregion Implicit styles

        #region Explicit styles

        public static Style<Button> FilledButton => filledButton ?? (filledButton = new Style<Button>(
            (Button.TextColorProperty, Color.White),
            (Button.BackgroundColorProperty, Color.FromHex("#1976D2")),
            (Button.CornerRadiusProperty, 5)
        )).BasedOn(Buttons);

        public static Style<Span> Link => link ?? (link = new Style<Span>(
            (Span.TextColorProperty, Color.Blue),
            (Span.TextDecorationsProperty, TextDecorations.Underline)
        ));

        #endregion Explicit styles
    }
}
```

Gli stili impliciti possono essere utilizzati caricando questi elementi nel dizionario risorse dell'applicazione:

```csharp
public App()
{
    Resources = Styles.Implicit;
    // ...
}
```

Gli stili espliciti possono essere utilizzati `Style` con il metodo di estensione.

```csharp
using static CSharpForMarkupExample.Styles;
// ...

new Button { Text = "Tap Me" } .Style (FilledButton),
```

> [!NOTE]
> Oltre al metodo di `Style` estensione `ApplyToDerivedTypes`, sono disponibili anche `BasedOn` `Add`i metodi di estensione, `CanCascade` , e.

In alternativa, è possibile creare metodi di estensione dello stile personalizzati:

```csharp
public static TButton Filled<TButton>(this TButton button) where TButton : Button
{
    button.Buttons(); // Equivalent to Style .BasedOn (Buttons)
    button.TextColor = Color.White;
    button.BackgroundColor = Color.Red;
    return button;
}
```

Il `Filled` metodo di estensione può quindi essere utilizzato come segue:

```csharp
new Button { Text = "Tap Me" } .Filled ()
```

## <a name="platform-specifics"></a>Funzionalità specifiche della piattaforma

Il `Invoke` metodo di estensione può essere utilizzato per applicare specifiche della piattaforma. Tuttavia, per evitare errori di ambiguità, non `using` includere direttamente le direttive per gli `Xamarin.Forms.PlatformConfiguration.*Specific` spazi dei nomi. Creare invece un alias dello spazio dei nomi e utilizzare la piattaforma specifica tramite l'alias:

```csharp
using Xamarin.Forms.Markup;
using PciOS = Xamarin.Forms.PlatformConfiguration.iOSSpecific;
// ...

new ListView { } .Invoke (l => PciOS.ListView.SetGroupHeaderStyle(l, PciOS.GroupHeaderStyle.Grouped))
```

Inoltre, se si utilizzano spesso specifiche specifiche della piattaforma, è possibile creare metodi di estensione Fluent in una classe di estensioni personalizzata:

```csharp
public static T iOSGroupHeaderStyle<T>(this T listView, PciOS.GroupHeaderStyle style) where T : Forms.ListView
{
  PciOS.ListView.SetGroupHeaderStyle(listView, style);
  return listView;
}
```

Il metodo di estensione può quindi essere utilizzato come segue:

```csharp
new ListView { } .iOSGroupHeaderStyle(PciOS.GroupHeaderStyle.Grouped)
```

Per ulteriori informazioni sulle specifiche della piattaforma, vedere funzionalità della piattaforma [Android](~/xamarin-forms/platform/android/index.md), [funzionalità della piattaforma iOS](~/xamarin-forms/platform/ios/index.md)e [funzionalità della piattaforma Windows](~/xamarin-forms/platform/windows/index.md).

## <a name="recommended-convention"></a>Convenzione consigliata

Un ordine e un raggruppamento consigliati di proprietà e metodi helper sono:

- **Scopo**: qualsiasi metodo di proprietà o helper il cui valore identifichi lo scopo del controllo `Text`, `Placeholder`ad esempio,,`Assign`.
- **Other**: tutte le proprietà o i metodi helper che non sono layout o binding, sulla stessa riga o su più righe.
- **Layout**: il layout viene ordinato in senso inverso: righe e colonne, opzioni di layout, margine, dimensioni, spaziatura interna e allineamento del contenuto.
- **Bind**: data binding viene eseguita alla fine della catena di metodi, con una proprietà associata per riga. Se la proprietà associabile *predefinita* è associata, deve trovarsi alla fine della catena di metodi.

Il codice seguente illustra un esempio di come segue questa convenzione:

```csharp
new Button { Text = "Verify" /* purpose */ } .Style (FilledButton) // other
            .Row (BodyRow.Button) .ColumnSpan (All<BodyCol>()) .FillExpandHorizontal () .Margin (10) // layout
            .Bind (Button.IsVisibleProperty, nameof(vm.CanVerifyRegistrationCode)) // bind
            .Bind (nameof(vm.VerifyRegistrationCodeCommand)), // bind default

new Label { }
           .Assign (out animatedMessageLabel) // purpose
           .Invoke (label => label.SizeChanged += MessageLabel_SizeChanged) // other
           .Row (BodyRow.Message) .ColumnSpan (All<BodyCol>()) // layout
           .Bind (nameof(vm.Message)), // bind default
```

Applicando costantemente questa convenzione è possibile analizzare rapidamente il markup C# e creare un'immagine mentale del layout dell'interfaccia utente.

## <a name="related-links"></a>Link correlati

- [CSharpForMarkupDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-csharpmarkupdemos/)
- [Funzionalità della piattaforma Android](~/xamarin-forms/platform/android/index.md)
- [funzionalità della piattaforma iOS](~/xamarin-forms/platform/ios/index.md)
- [Funzionalità della piattaforma Windows](~/xamarin-forms/platform/windows/index.md)
