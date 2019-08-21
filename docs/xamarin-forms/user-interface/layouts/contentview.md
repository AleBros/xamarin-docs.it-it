---
title: Novell. Forms ContentView
description: Questo articolo illustra come usare la classe ContentView per creare un controllo personalizzato, ad esempio CardView.
ms.prod: xamarin
ms.assetid: 638402E7-CA44-456B-863B-791F6B6B561D
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 08/14/2019
ms.openlocfilehash: e340b45148c7528eff1aa511ee9902a4ac2658c0
ms.sourcegitcommit: 9178e2e689f027212ea3e623b556b312985d79fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69658158"
---
# <a name="xamarinforms-contentview"></a>Novell. Forms ContentView

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/en-us/samples/xamarin/xamarin-forms-samples/userinterface-cardview/)

La classe Novell. [`ContentView`](xref:Xamarin.Forms.ContentView) Forms è un tipo `Layout` di che contiene un singolo elemento figlio e viene in genere usato per creare controlli personalizzati riutilizzabili. La `ContentView` classe eredita da [`TemplatedView`](xref:Xamarin.Forms.TemplatedView). Questo articolo e l'esempio associato spiegano come creare un controllo personalizzato `CardView` basato `ContentView` sulla classe.

Lo screenshot seguente mostra un `CardView` controllo che deriva `ContentView` dalla classe:

[![Schermata dell'applicazione di esempio CardView](contentview-images/cardview-list-cropped.png)](contentview-images/cardview-list.png#lightbox)

La `ContentView` classe definisce una singola proprietà:

* [`Content`](xref:Xamarin.Forms.ContentView.Content)è un `View` oggetto. Questa proprietà è supportata da un [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) oggetto in modo che possa essere la destinazione di associazioni dati.

Eredita inoltre una proprietà `TemplatedView` dalla classe: `ContentView`

* [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate)è un `ControlTemplate` oggetto che può definire o eseguire l'override dell'aspetto del controllo.

Per altre informazioni sulla proprietà `ControlTemplate` , vedere [personalizzare l'aspetto con un oggetto ControlTemplate](#customize-appearance-with-a-controltemplate).

## <a name="create-a-custom-control"></a>Creazione di un controllo personalizzato

La `ContentView` classe offre una piccola funzionalità autonoma, ma può essere usata per creare un controllo personalizzato. Il progetto di esempio definisce `CardView` un controllo, ovvero un elemento dell'interfaccia utente che visualizza un'immagine, un titolo e una descrizione in un layout simile a una scheda.

Il processo per la creazione di un controllo personalizzato consiste nel:

1. Creare una nuova classe usando il `ContentView` modello in Visual Studio 2019.
1. Definire eventuali proprietà o eventi univoci nel file code-behind per il nuovo controllo personalizzato.
1. Creare l'interfaccia utente per il controllo personalizzato.

> [!NOTE]
> È possibile creare un controllo personalizzato il cui layout è definito nel codice anziché in XAML. Per semplicità, l'applicazione di esempio definisce solo una `CardView` singola classe con un layout XAML. Tuttavia, l'applicazione di esempio contiene una classe **CardViewCodePage** che mostra il processo di utilizzo del controllo personalizzato nel codice.

### <a name="create-code-behind-properties"></a>Crea proprietà code-behind

Il `CardView` controllo personalizzato definisce le proprietà seguenti:

* `CardTitle``string` : oggetto che rappresenta il titolo visualizzato sulla scheda.
* `CardDescription``string` : oggetto che rappresenta la descrizione visualizzata sulla scheda.
* `IconImageSource``ImageSource` : oggetto che rappresenta l'immagine visualizzata sulla scheda.
* `IconBackgroundColor``Color` : oggetto che rappresenta il colore di sfondo dell'immagine visualizzata sulla scheda.
* `BorderColor``Color` : oggetto che rappresenta il colore del bordo della scheda, del bordo dell'immagine e della linea di divisione.
* `CardColor``Color` : oggetto che rappresenta il colore di sfondo della scheda.

> [!NOTE]
> La `BorderColor` proprietà interessa più elementi a scopo dimostrativo. Se necessario, questa proprietà può essere suddivisa in tre proprietà.

Ogni proprietà è supportata da un' `BindableProperty` istanza di. Il supporto `BindableProperty` consente a ogni proprietà di essere con stile e associato usando il modello MVVM. Per altre informazioni, vedere [associare dati a MVVM](#bind-data-with-mvvm).

Nell'esempio seguente viene illustrato come creare un supporto `BindableProperty`:

```csharp
public static readonly BindableProperty CardTitleProperty = BindableProperty.Create(
    "CardTitle",        // the name of the bindable property
    typeof(string),     // the bindable property type
    typeof(CardView),   // the parent object type
    string.Empty);      // the default value for the property
```

La proprietà personalizzata usa i `GetValue` metodi `SetValue` e per ottenere e impostare i `BindableProperty` valori dell'oggetto:

```csharp
public string CardTitle
{
    get => (string)GetValue(CardView.CardTitleProperty);
    set => SetValue(CardView.CardTitleProperty, value);
}
```

Per ulteriori informazioni sugli `BindableProperty` oggetti, vedere [proprietà associabili](~/xamarin-forms/xaml/bindable-properties.md).

### <a name="define-ui"></a>Definire l'interfaccia utente

L'interfaccia utente del controllo personalizzato `ContentView` USA come elemento radice per il `CardView` controllo. Nell'esempio seguente viene illustrato `CardView` il codice XAML:

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
            <Frame BorderColor="{Binding BorderColor}"
                   BackgroundColor="{Binding IconBackgroundColor}"
                   ...>
                <Image Source="{Binding IconImageSource}"
                       .. />
            </Frame>
            <Label Text="{Binding CardTitle}"
                   ... />
            <BoxView BackgroundColor="{Binding BorderColor}"
                     ... />
            <Label Text="{Binding CardDescription}"
                   ... />
        </Grid>
    </Frame>
</ContentView>
```

L' `ContentView` elemento imposta la `x:Name` proprietà su, che può essere utilizzata `CardView` per accedere all'oggetto associato all'istanza di. Gli elementi nel layout impostano associazioni sulle rispettive proprietà a valori definiti nell'oggetto associato.

Per altre informazioni sul data binding, vedere [Data binding di Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

## <a name="instantiate-a-custom-control"></a>Creare un'istanza di un controllo personalizzato

Un riferimento allo spazio dei nomi del controllo personalizzato deve essere aggiunto a una pagina che crea un'istanza del controllo personalizzato. Nell'esempio seguente viene illustrato un riferimento allo spazio dei nomi denominato `ContentPage` controlli aggiunti a un'istanza in XAML:

```xaml
<ContentPage ...
             xmlns:controls="clr-namespace:CardViewDemo.Controls" >
```

Una volta aggiunto `CardView` il riferimento, è possibile creare un'istanza di in XAML e le relative proprietà definite:

```xaml
<controls:CardView BorderColor="DarkGray"
                   CardTitle="Slavko Vlasic"
                   CardDescription="Lorem ipsum dolor sit..."
                   IconBackgroundColor="SlateGray"
                   IconImageSource="user.png"/>
```

È `CardView` anche possibile creare un'istanza di un oggetto nel codice:

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

### <a name="bind-data-with-mvvm"></a>Associare dati a MVVM

Gli `BindableProperty` oggetti`CardView` nella classe consentono le associazioni di stile MVC (Model-View-ViewModel). L'applicazione di esempio contiene `PersonCollectionViewModel` una classe che definisce una singola proprietà della raccolta:

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{
    ...
    public List<PersonViewModel> Items
    {
        get
        {
            return items;
        }
        set
        {
            items = value;
            NotifyPropertyChanged();
        }
    }
    ...
}
```

La `PersonViewModel` classe rappresenta un profilo personale:

```csharp
public class PersonViewModel : INotifyPropertyChanged
{
    ...

    string photo;
    public string Photo
    {
        get
        {
            return photo;
        }
        set
        {
            photo = value;
            NotifyPropertyChanged();
        }
    }

    string name;
    public string Name
    {
        get
        {
            return name;
        }
        set
        {
            name = value;
            NotifyPropertyChanged();
        }
    }

    string bio;
    public string Bio
    {
        get
        {
            return bio;
        }
        set
        {
            bio = value;
            NotifyPropertyChanged();
        }
    }
    ...
}
```

L' `CardView` oggetto può essere utilizzato per eseguire il rendering `PersonViewModel` della raccolta di oggetti come un elenco di schede. Nell'esempio seguente viene illustrato come associare un' `PersonViewCollection` istanza di a `StackLayout` un'istanza in XAML:

```xaml
<StackLayout HorizontalOptions="Fill"
             VerticalOptions="Fill"
             BindableLayout.ItemsSource="{Binding Items}">
    <BindableLayout.ItemTemplate>
        <DataTemplate>
            <controls:CardView Margin="4"
                               BorderColor="DarkGray"
                               IconBackgroundColor="SlateGray"
                               BindingContext="{Binding .}"
                               CardTitle="{Binding Name}"
                               CardDescription="{Binding Bio}"
                               IconImageSource="{Binding Photo}"/>
        </DataTemplate>
    </BindableLayout.ItemTemplate>
</StackLayout>
```

La `Items` proprietà di un' `PersonViewCollection` `StackLayout` istanza è associata a utilizzando un layout associabile. Definisce l'aspetto di ogni `CardView` oggetto e i dati vengono associati alle proprietà in un `PersonViewModel`oggetto. `DataTemplate` Quando è impostato, viene creato `CardView` un oggetto `Items` per ogni `PersonView` oggetto nella raccolta. `BindingContext` `BindingContext` Viene impostato come illustrato nell'esempio seguente:

```csharp
public partial class CardViewMvvmPage : ContentPage
{
    public CardViewMvvmPage()
    {
        InitializeComponent();
        BindingContext = DataService.GetPersonCollection();
    }
}
```

Per ulteriori informazioni su data binding, vedere [Data Binding Novell. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md). Per ulteriori informazioni sugli `BindableProperty` oggetti, vedere [proprietà associabili](~/xamarin-forms/xaml/bindable-properties.md).

## <a name="customize-appearance-with-a-controltemplate"></a>Personalizzare l'aspetto con un oggetto ControlTemplate

Un controllo personalizzato che deriva dalla `ContentView` classe può definire l'aspetto usando XAML, il codice o non può definire l'aspetto. Indipendentemente dalla modalità di definizione dell'aspetto, `ControlTemplate` un oggetto può eseguire l'override dell'aspetto con un layout personalizzato.

Il `CardView` layout potrebbe occupare una quantità eccessiva di spazio per alcuni casi d'uso. Un `ControlTemplate` può eseguire l' `CardView` override del layout per fornire una visualizzazione più compatta, adatta per un elenco condensato:

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

Il data binding in `ControlTemplate` un oggetto `TemplateBinding` usa l'estensione di markup per specificare le associazioni. La `ControlTemplate` proprietà può quindi essere impostata sull'oggetto ControlTemplate definito, usando il relativo `x:Key` valore. Nell'esempio seguente viene illustrata la `ControlTemplate` proprietà impostata in un' `CardView` istanza:

```xaml
<controls:CardView ControlTemplate="{StaticResource CardViewCompressed}"/>
```

Gli screenshot seguenti mostrano un'istanza standard `CardView` e `CardView` il cui `ControlTemplate` è stato eseguito l'override:

[![Schermata ControlTemplate CardView](contentview-images/cardview-controltemplates-cropped.png)](contentview-images/cardview-controltemplates.png#lightbox)

Per ulteriori informazioni sui modelli di controllo, vedere [Novell. Forms Control Templates](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md).

## <a name="related-links"></a>Collegamenti correlati

* [Applicazione di esempio CardView](https://docs.microsoft.com/en-us/samples/xamarin/xamarin-forms-samples/userinterface-cardview/)
* [Data Binding Novell. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
* [Proprietà associabili](~/xamarin-forms/xaml/bindable-properties.md).
* [Modelli di controllo Novell. Forms](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md)
