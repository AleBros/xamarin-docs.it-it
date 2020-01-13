---
title: Xamarin.Forms ContentView
description: Questo articolo illustra come usare la classe ContentView per creare un controllo personalizzato, ad esempio CardView.
ms.prod: xamarin
ms.assetid: 638402E7-CA44-456B-863B-791F6B6B561D
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/14/2019
ms.openlocfilehash: 69f3311834fd438af97b3d2fa527572f02d2b0cb
ms.sourcegitcommit: fa2898d95b35fcee05503f3829351ba5a7d4a44d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74955079"
---
# <a name="xamarinforms-contentview"></a>Xamarin.Forms ContentView

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-contentviewdemos/)

La classe [`ContentView`](xref:Xamarin.Forms.ContentView) Xamarin.Forms è un tipo di `Layout` che contiene un singolo elemento figlio e viene in genere usato per creare controlli personalizzati riutilizzabili. La classe `ContentView` eredita da [`TemplatedView`](xref:Xamarin.Forms.TemplatedView). In questo articolo e nell'esempio associato viene illustrato come creare un controllo `CardView` personalizzato basato sulla classe `ContentView`.

Lo screenshot seguente mostra un controllo `CardView` che deriva dalla classe `ContentView`:

[schermata dell'applicazione di esempio ![CardView](contentview-images/cardview-list-cropped.png)](contentview-images/cardview-list.png#lightbox)

La classe `ContentView` definisce un'unica proprietà:

* [`Content`](xref:Xamarin.Forms.ContentView.Content) è un oggetto `View`. Questa proprietà è supportata da un oggetto [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) in modo che possa essere la destinazione delle associazioni dati.

Il `ContentView` eredita inoltre una proprietà dalla classe `TemplatedView`:

* [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) è un `ControlTemplate` che può definire o eseguire l'override dell'aspetto del controllo.

Per altre informazioni sulla proprietà `ControlTemplate`, vedere [personalizzare l'aspetto con un oggetto ControlTemplate](#customize-appearance-with-a-controltemplate).

## <a name="create-a-custom-control"></a>Creazione di un controllo personalizzato

La classe `ContentView` offre funzionalità minime, ma può essere usata per creare un controllo personalizzato. Il progetto di esempio definisce un controllo `CardView`, ovvero un elemento dell'interfaccia utente che visualizza un'immagine, un titolo e una descrizione in un layout simile a una scheda.

Il processo per la creazione di un controllo personalizzato consiste nel:

1. Creare una nuova classe usando il modello di `ContentView` in Visual Studio 2019.
1. Definire eventuali proprietà o eventi univoci nel file code-behind per il nuovo controllo personalizzato.
1. Creare l'interfaccia utente per il controllo personalizzato.

> [!NOTE]
> È possibile creare un controllo personalizzato il cui layout è definito nel codice anziché in XAML. Per semplicità, l'applicazione di esempio definisce solo una singola classe di `CardView` con un layout XAML. Tuttavia, l'applicazione di esempio contiene una classe **CardViewCodePage** che mostra il processo di utilizzo del controllo personalizzato nel codice.

### <a name="create-code-behind-properties"></a>Crea proprietà code-behind

Il `CardView` controllo personalizzato definisce le proprietà seguenti:

* `CardTitle`: oggetto `string` che rappresenta il titolo visualizzato sulla scheda.
* `CardDescription`: oggetto `string` che rappresenta la descrizione visualizzata sulla scheda.
* `IconImageSource`: oggetto `ImageSource` che rappresenta l'immagine visualizzata sulla scheda.
* `IconBackgroundColor`: oggetto `Color` che rappresenta il colore di sfondo dell'immagine visualizzata sulla scheda.
* `BorderColor`: oggetto `Color` che rappresenta il colore del bordo della scheda, del bordo dell'immagine e della linea di divisione.
* `CardColor`: oggetto `Color` che rappresenta il colore di sfondo della scheda.

> [!NOTE]
> La proprietà `BorderColor` interessa più elementi a scopo dimostrativo. Se necessario, questa proprietà può essere suddivisa in tre proprietà.

Ogni proprietà è supportata da un'istanza di `BindableProperty`. Il `BindableProperty` di supporto consente a ogni proprietà di essere con stile e associato usando il modello MVVM.

Nell'esempio seguente viene illustrato come creare un `BindableProperty`di supporto:

```csharp
public static readonly BindableProperty CardTitleProperty = BindableProperty.Create(
    "CardTitle",        // the name of the bindable property
    typeof(string),     // the bindable property type
    typeof(CardView),   // the parent object type
    string.Empty);      // the default value for the property
```

La proprietà personalizzata usa i metodi `GetValue` e `SetValue` per ottenere e impostare i valori dell'oggetto `BindableProperty`:

```csharp
public string CardTitle
{
    get => (string)GetValue(CardView.CardTitleProperty);
    set => SetValue(CardView.CardTitleProperty, value);
}
```

Per ulteriori informazioni sugli oggetti `BindableProperty`, vedere [proprietà associabili](~/xamarin-forms/xaml/bindable-properties.md).

### <a name="define-ui"></a>Definire l'interfaccia utente

L'interfaccia utente del controllo personalizzato usa un `ContentView` come elemento radice per il controllo `CardView`. Nell'esempio seguente viene illustrato il `CardView` XAML:

```XAML
<ContentView ...
             x:Name="this"
             x:Class="CardViewDemo.Controls.CardView">
    <Frame BindingContext="{x:Reference this}"
            BackgroundColor="{Binding CardColor}"
            BorderColor="{Binding BorderColor}"
            ...>
        <Grid>
            ...
            <Frame BorderColor="{Binding BorderColor, FallbackValue='Black'}"
                   BackgroundColor="{Binding IconBackgroundColor, FallbackValue='Grey'}"
                   ...>
                <Image Source="{Binding IconImageSource}"
                       .. />
            </Frame>
            <Label Text="{Binding CardTitle, FallbackValue='Card Title'}"
                   ... />
            <BoxView BackgroundColor="{Binding BorderColor, FallbackValue='Black'}"
                     ... />
            <Label Text="{Binding CardDescription, FallbackValue='Card description text.'}"
                   ... />
        </Grid>
    </Frame>
</ContentView>
```

L'elemento `ContentView` imposta la proprietà `x:Name` su **this**, che può essere utilizzato per accedere all'oggetto associato all'istanza di `CardView`. Gli elementi nel layout impostano associazioni sulle rispettive proprietà a valori definiti nell'oggetto associato.

Per altre informazioni sul data binding, vedere [Data binding di Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

> [!NOTE]
> La proprietà `FallbackValue` fornisce un valore predefinito nel caso in cui l'associazione sia `null`. Questo consente anche al visualizzatore [Anteprima XAML](~/xamarin-forms/xaml/xaml-previewer/index.md) in Visual Studio di eseguire il rendering del controllo `CardView`.

## <a name="instantiate-a-custom-control"></a>Creare un'istanza di un controllo personalizzato

Un riferimento allo spazio dei nomi del controllo personalizzato deve essere aggiunto a una pagina che crea un'istanza del controllo personalizzato. Nell'esempio seguente viene illustrato un riferimento allo spazio dei nomi denominato **controlli** aggiunti a un'istanza di `ContentPage` in XAML:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CardViewDemo.Controls" >
```

Una volta aggiunto il riferimento, è possibile creare un'istanza del `CardView` in XAML e le relative proprietà definite:

```xaml
<controls:CardView BorderColor="DarkGray"
                   CardTitle="Slavko Vlasic"
                   CardDescription="Lorem ipsum dolor sit..."
                   IconBackgroundColor="SlateGray"
                   IconImageSource="user.png"/>
```

È anche possibile creare un'istanza di un `CardView` nel codice:

```csharp
CardView card = new CardView
{
    BorderColor = Color.DarkGray,
    CardTitle = "Slavko Vlasic",
    CardDescription = "Lorem ipsum dolor sit...",
    IconBackgroundColor = Color.SlateGray,
    IconImageSource = ImageSource.FromFile("user.png")
};
```

## <a name="customize-appearance-with-a-controltemplate"></a>Personalizzare l'aspetto con un oggetto ControlTemplate

Un controllo personalizzato che deriva dalla classe `ContentView` può definire l'aspetto usando XAML, il codice o non può definire l'aspetto. Indipendentemente dalla modalità di definizione dell'aspetto, un oggetto `ControlTemplate` può eseguire l'override dell'aspetto con un layout personalizzato.

Il layout `CardView` potrebbe occupare troppo spazio per alcuni casi d'uso. Un `ControlTemplate` può eseguire l'override del layout del `CardView` per offrire una visualizzazione più compatta, adatta per un elenco con condensazione:

```xaml
<ContentPage.Resources>
    <ResourceDictionary>
        <ControlTemplate x:Key="CardViewCompressed">
            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="100" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="100" />
                    <ColumnDefinition Width="100*" />
                </Grid.ColumnDefinitions>
                <Image Grid.Row="0"
                       Grid.Column="0"
                       Source="{TemplateBinding IconImageSource}"
                       BackgroundColor="{TemplateBinding IconBackgroundColor}"
                       WidthRequest="100"
                       HeightRequest="100"
                       Aspect="AspectFill"
                       HorizontalOptions="Center"
                       VerticalOptions="Center"/>
                <StackLayout Grid.Row="0"
                             Grid.Column="1">
                    <Label Text="{TemplateBinding CardTitle}"
                           FontAttributes="Bold" />
                    <Label Text="{TemplateBinding CardDescription}" />
                </StackLayout>
            </Grid>
        </ControlTemplate>
    </ResourceDictionary>
</ContentPage.Resources>
```

Il data binding in una `ControlTemplate` usa l'estensione di markup `TemplateBinding` per specificare le associazioni. È quindi possibile impostare la proprietà `ControlTemplate` sull'oggetto ControlTemplate definito utilizzando il relativo valore `x:Key`. Nell'esempio seguente viene illustrata la proprietà `ControlTemplate` impostata in un'istanza `CardView`:

```xaml
<controls:CardView ControlTemplate="{StaticResource CardViewCompressed}"/>
```

Gli screenshot seguenti mostrano un'istanza di `CardView` standard e `CardView` di cui è stato eseguito l'override `ControlTemplate`:

[schermata di ![ControlTemplate CardView](contentview-images/cardview-controltemplates-cropped.png)](contentview-images/cardview-controltemplates.png#lightbox)

Per ulteriori informazioni sui modelli di controllo, vedere [Xamarin.Forms Control Templates](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md).

## <a name="related-links"></a>Collegamenti correlati

* [Applicazione di esempio ContentView](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-contentviewdemos/)
* [Data Binding Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
* [Proprietà associabili](~/xamarin-forms/xaml/bindable-properties.md).
* [Modelli di controllo Xamarin.Forms](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md)
