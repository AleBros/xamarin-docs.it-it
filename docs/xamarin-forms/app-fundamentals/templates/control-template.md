---
title: Modelli di controllo Xamarin.Forms
description: I modelli di controllo Xamarin.Forms definiscono la struttura visiva dei controlli personalizzati derivati da ContentView e delle pagine derivate da ContentPage.
ms.prod: xamarin
ms.assetid: 8B8E2360-6531-44A3-A7C8-9A8808DE9B86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/13/2020
ms.openlocfilehash: a73123b89cba932f2e2cb907645f6fe858cf6176
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "79305136"
---
# <a name="xamarinforms-control-templates"></a>Modelli di controllo Xamarin.Forms

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplatedemos)

I modelli di controllo Xamarin.Forms consentono [`ContentView`](xref:Xamarin.Forms.ContentView) di definire [`ContentPage`](xref:Xamarin.Forms.ContentPage) la struttura visiva dei controlli personalizzati derivati e delle pagine derivate. I modelli di controllo separano l'interfaccia utente (UI) per un controllo personalizzato o una pagina dalla logica che implementa il controllo o la pagina. Il contenuto aggiuntivo può anche essere inserito nel controllo personalizzato basato su modelli, o nella pagina basata su modelli, in una posizione predefinita.

È ad esempio possibile creare un modello di controllo per ridefinire l'interfaccia utente fornita da un controllo personalizzato. Il modello di controllo può quindi essere utilizzato dall'istanza del controllo personalizzato richiesta. In alternativa, è possibile creare un modello di controllo che definisce qualsiasi interfaccia utente comune che verrà usata da più pagine in un'applicazione. Il modello di controllo può quindi essere utilizzato da più pagine, anche se ogni pagina visualizza comunque contenuto univoco.

## <a name="create-a-controltemplate"></a>Creare un ControlTemplate

L'esempio seguente mostra il codice per un controllo personalizzato `CardView`:

```csharp
public class CardView : ContentView
{
    public static readonly BindableProperty CardTitleProperty = BindableProperty.Create(nameof(CardTitle), typeof(string), typeof(CardView), string.Empty);
    public static readonly BindableProperty CardDescriptionProperty = BindableProperty.Create(nameof(CardDescription), typeof(string), typeof(CardView), string.Empty);
    // ...

    public string CardTitle
    {
        get => (string)GetValue(CardTitleProperty);
        set => SetValue(CardTitleProperty, value);
    }

    public string CardDescription
    {
        get => (string)GetValue(CardDescriptionProperty);
        set => SetValue(CardDescriptionProperty, value);
    }
    // ...
}
```

La `CardView` classe , che [`ContentView`](xref:Xamarin.Forms.ContentView) deriva dalla classe , rappresenta un controllo personalizzato che visualizza i dati in un layout simile a una scheda. La classe contiene proprietà, supportate da proprietà associabili, per i dati visualizzati. Tuttavia, la classe `CardView` non definisce alcuna interfaccia utente. L'interfaccia utente verrà invece definita con un modello di controllo. Per altre informazioni sulla creazione di controlli personalizzati derivati da `ContentView`, vedere [Xamarin.Forms ContentView](~/xamarin-forms/user-interface/layouts/contentview.md).

Viene creato un modello [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) di controllo con il tipo. Quando si `ControlTemplate`crea un [`View`](xref:Xamarin.Forms.View) oggetto , si combinano gli oggetti per compilare l'interfaccia utente per un controllo personalizzato o una pagina. Un `ControlTemplate` deve avere un solo oggetto `View` come elemento radice. Tuttavia, l'elemento radice contiene in genere altri oggetti `View`. La combinazione degli oggetti costituisce la struttura visiva del controllo.

Mentre [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) un oggetto può essere definito inline, [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) l'approccio tipico per dichiarare un è come risorsa in un dizionario risorse. Poiché i modelli di controllo sono risorse, rispettano le stesse regole di ambito che si applicano a tutte le risorse. Ad esempio, se si dichiara un modello di controllo nell'elemento radice del file XAML di definizione dell'applicazione, il modello può essere usato ovunque nell'applicazione. Se si definisce il modello in una pagina, solo tale pagina può usare il modello di controllo. Per altre informazioni sulle risorse, vedere [Dizionari risorse di Xamarin.Forms](~/xamarin-forms/xaml/resource-dictionaries.md).

L'esempio XAML [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) seguente `CardView` mostra un per gli oggetti:The following XAML example shows a for objects:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    <ContentPage.Resources>
      <ControlTemplate x:Key="CardViewControlTemplate">
          <Frame BindingContext="{Binding Source={RelativeSource TemplatedParent}}"
                 BackgroundColor="{Binding CardColor}"
                 BorderColor="{Binding BorderColor}"
                 CornerRadius="5"
                 HasShadow="True"
                 Padding="8"
                 HorizontalOptions="Center"
                 VerticalOptions="Center">
              <Grid>
                  <Grid.RowDefinitions>
                      <RowDefinition Height="75" />
                      <RowDefinition Height="4" />
                      <RowDefinition Height="Auto" />
                  </Grid.RowDefinitions>
                  <Grid.ColumnDefinitions>
                      <ColumnDefinition Width="75" />
                      <ColumnDefinition Width="200" />
                  </Grid.ColumnDefinitions>
                  <Frame IsClippedToBounds="True"
                         BorderColor="{Binding BorderColor}"
                         BackgroundColor="{Binding IconBackgroundColor}"
                         CornerRadius="38"
                         HeightRequest="60"
                         WidthRequest="60"
                         HorizontalOptions="Center"
                         VerticalOptions="Center">
                      <Image Source="{Binding IconImageSource}"
                             Margin="-20"
                             WidthRequest="100"
                             HeightRequest="100"
                             Aspect="AspectFill" />
                  </Frame>
                  <Label Grid.Column="1"
                         Text="{Binding CardTitle}"
                         FontAttributes="Bold"
                         FontSize="Large"
                         VerticalTextAlignment="Center"
                         HorizontalTextAlignment="Start" />
                  <BoxView Grid.Row="1"
                           Grid.ColumnSpan="2"
                           BackgroundColor="{Binding BorderColor}"
                           HeightRequest="2"
                           HorizontalOptions="Fill" />
                  <Label Grid.Row="2"
                         Grid.ColumnSpan="2"
                         Text="{Binding CardDescription}"
                         VerticalTextAlignment="Start"
                         VerticalOptions="Fill"
                         HorizontalOptions="Fill" />
              </Grid>
          </Frame>
      </ControlTemplate>
    </ContentPage.Resources>
    ...
</ContentPage>
```

Quando [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) un oggetto viene dichiarato come risorsa, deve `x:Key` avere una chiave specificata con l'attributo in modo che possa essere identificata nel dizionario risorse. In questo esempio, l'elemento radice del `CardViewControlTemplate` è un oggetto [`Frame`](xref:Xamarin.Forms.Frame). L'oggetto `Frame` usa l'estensione di markup `RelativeSource` per impostare il relativo `BindingContext` sull'istanza dell'oggetto di runtime a cui verrà applicato il modello, noto come *oggetto padre basato su modelli*. `Frame` L'oggetto utilizza [`Grid`](xref:Xamarin.Forms.Grid)una `Frame` [`Image`](xref:Xamarin.Forms.Image)combinazione [`Label`](xref:Xamarin.Forms.Label)di [`BoxView`](xref:Xamarin.Forms.BoxView) oggetti , , , `CardView` e per definire la struttura visiva di un oggetto. Le espressioni di associazione di questi oggetti vengono risolte in base alle proprietà di `CardView`, dato che `BindingContext` viene ereditato dall'elemento `Frame` radice. Per altre informazioni sull'estensione di markup `RelativeSource`, vedere [Associazioni relative di Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md).

## <a name="consume-a-controltemplate"></a>Utilizzare un ControlTemplate

Un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) oggetto può [`ContentView`](xref:Xamarin.Forms.ContentView) essere applicato a [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) un controllo personalizzato derivato impostandone la proprietà sull'oggetto modello del controllo. Analogamente, [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) un oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) può essere applicato [`ControlTemplate`](xref:Xamarin.Forms.TemplatedPage.ControlTemplate) a una pagina derivata impostandone la proprietà sull'oggetto modello di controllo. In fase di [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) esecuzione, quando viene applicato un `ControlTemplate` oggetto, tutti i controlli definiti in vengono aggiunti alla struttura ad albero visuale del controllo personalizzato basato su modelli o della pagina basata su modelli.

L'esempio seguente `CardViewControlTemplate` mostra l'assegnazione alla [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) proprietà di ogni `CardView` oggetto:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:controls="clr-namespace:ControlTemplateDemos.Controls"
             ...>
    <StackLayout Margin="30">
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="John Doe"
                           CardDescription="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla elit dolor, convallis non interdum."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Jane Doe"
                           CardDescription="Phasellus eu convallis mi. In tempus augue eu dignissim fermentum. Morbi ut lacus vitae eros lacinia."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Xamarin Monkey"
                           CardDescription="Aliquam sagittis, odio lacinia fermentum dictum, mi erat scelerisque erat, quis aliquet arcu."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"
                           ControlTemplate="{StaticResource CardViewControlTemplate}" />
    </StackLayout>
</ContentPage>
```

In questo esempio, i controlli nel `CardViewControlTemplate` diventano parte della struttura ad albero visuale per ogni oggetto `CardView`. Poiché [`Frame`](xref:Xamarin.Forms.Frame) l'oggetto radice per `BindingContext` il modello di `Frame` controllo imposta its per l'elemento `CardView` padre basato su modelli, il e i relativi elementi figlio risolvere le espressioni di associazione rispetto alle proprietà di ogni oggetto.

Gli screenshot seguenti mostrano il `CardViewControlTemplate` applicato ai tre oggetti `CardView`:

[![Screenshot degli oggetti CardView basati su modelli, in iOS e Android](control-template-images/relativesource-controltemplate.png "Oggetti CardView basati su modelli")](control-template-images/relativesource-controltemplate-large.png#lightbox "Oggetti CardView basati su modelli")

> [!IMPORTANT]
> Il momento in [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) cui un oggetto viene applicato a un'istanza del controllo può essere rilevato eseguendo l'override del `OnApplyTemplate` metodo nel controllo personalizzato basato su modelli o nella pagina basata su modelli. Per altre informazioni, vedere [Ottenere un elemento denominato da un modello](#get-a-named-element-from-a-template).

## <a name="pass-parameters-with-templatebinding"></a>Passare parametri con TemplateBinding

L'estensione `TemplateBinding` di markup associa una proprietà [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) di un elemento che si trova in un oggetto a una proprietà pubblica definita dal controllo personalizzato basato su modelli o dalla pagina basata su modelli. Quando si usa `TemplateBinding`, si consente alle proprietà del controllo di fungere da parametri per il modello. Pertanto, quando viene impostata una proprietà per un controllo personalizzato basato su modelli o una pagina basata su modelli, tale valore viene passato all'elemento per cui è stato definito il `TemplateBinding`.

> [!IMPORTANT]
> L'estensione `TemplateBinding` di markup è [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) un'alternativa alla creazione di un che utilizza l'estensione `RelativeSource` di markup per impostare l'elemento `BindingContext` radice nel modello sul relativo elemento padre basato su modelli. L'estensione di markup `TemplateBinding` elimina l'associazione `RelativeSource` e sostituisce le espressioni `Binding` con espressioni `TemplateBinding`.

L'estensione di markup `TemplateBinding` definisce le proprietà seguenti:

- `Path`, di tipo `string`, il percorso della proprietà.
- `Mode`, di tipo `BindingMode`, la direzione in cui le modifiche vengono propagate tra l'*origine* e la *destinazione*.
- `Converter`, di tipo `IValueConverter`, il convertitore di valori di associazione.
- `ConverterParameter`, di tipo `object`, il parametro per il convertitore di valori di associazione.
- `StringFormat`, di tipo `string`, il formato di stringa per l'associazione.

`ContentProperty` per l'estensione di markup `TemplateBinding` è `Path`. È pertanto possibile omettere la parte "Path=" dell'estensione di markup se il percorso è il primo elemento nell'espressione `TemplateBinding`. Per altre informazioni sull'uso di queste proprietà in un'espressione di associazione, vedere [Associazione di dati di Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

> [!WARNING]
> L'estensione `TemplateBinding` di markup deve [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)essere utilizzata solo all'interno di un oggetto . Tuttavia, se si tenta di usare un'espressione `TemplateBinding` al di fuori di un `ControlTemplate` non verrà generato un errore di compilazione o un'eccezione.

L'esempio XAML [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) seguente `CardView` mostra un `TemplateBinding` per gli oggetti, che usa l'estensione di markup:The following XAML example shows a for objects, that uses the markup extension:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    <ContentPage.Resources>
        <ControlTemplate x:Key="CardViewControlTemplate">
            <Frame BackgroundColor="{TemplateBinding CardColor}"
                   BorderColor="{TemplateBinding BorderColor}"
                   CornerRadius="5"
                   HasShadow="True"
                   Padding="8"
                   HorizontalOptions="Center"
                   VerticalOptions="Center">
                <Grid>
                    <Grid.RowDefinitions>
                        <RowDefinition Height="75" />
                        <RowDefinition Height="4" />
                        <RowDefinition Height="Auto" />
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="75" />
                        <ColumnDefinition Width="200" />
                    </Grid.ColumnDefinitions>
                    <Frame IsClippedToBounds="True"
                           BorderColor="{TemplateBinding BorderColor}"
                           BackgroundColor="{TemplateBinding IconBackgroundColor}"
                           CornerRadius="38"
                           HeightRequest="60"
                           WidthRequest="60"
                           HorizontalOptions="Center"
                           VerticalOptions="Center">
                        <Image Source="{TemplateBinding IconImageSource}"
                               Margin="-20"
                               WidthRequest="100"
                               HeightRequest="100"
                               Aspect="AspectFill" />
                    </Frame>
                    <Label Grid.Column="1"
                           Text="{TemplateBinding CardTitle}"
                           FontAttributes="Bold"
                           FontSize="Large"
                           VerticalTextAlignment="Center"
                           HorizontalTextAlignment="Start" />
                    <BoxView Grid.Row="1"
                             Grid.ColumnSpan="2"
                             BackgroundColor="{TemplateBinding BorderColor}"
                             HeightRequest="2"
                             HorizontalOptions="Fill" />
                    <Label Grid.Row="2"
                           Grid.ColumnSpan="2"
                           Text="{TemplateBinding CardDescription}"
                           VerticalTextAlignment="Start"
                           VerticalOptions="Fill"
                           HorizontalOptions="Fill" />
                </Grid>
            </Frame>
        </ControlTemplate>
    </ContentPage.Resources>
    ...
</ContentPage>
```

In questo esempio, l'estensione di markup `TemplateBinding` risolve le espressioni di associazione in base alle proprietà di ogni oggetto `CardView`. Gli screenshot seguenti mostrano il `CardViewControlTemplate` applicato ai tre oggetti `CardView`:

[![Screenshot degli oggetti CardView basati su modelli, in iOS e Android](control-template-images/templatebinding-controltemplate.png "Oggetti CardView basati su modelli")](control-template-images/templatebinding-controltemplate-large.png#lightbox "Oggetti CardView basati su modelli")

> [!IMPORTANT]
> L'uso dell'estensione di markup `TemplateBinding` equivale a impostare il `BindingContext` dell'elemento radice nel modello sul relativo elemento padre basato su modelli con l'estensione di markup `RelativeSource` e quindi a risolvere le associazioni degli oggetti figlio con l'estensione di markup `Binding`. L'estensione di markup `TemplateBinding` crea infatti un `Binding` con `RelativeBindingSource.TemplatedParent` come `Source`.

## <a name="apply-a-controltemplate-with-a-style"></a>Applicare un ControlTemplate con uno stile

I modelli di controllo possono anche essere applicati con gli stili. A tale scopo, è possibile creare uno stile *implicito* o *esplicito* che utilizza il [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate).

Nell'esempio di codice XAML seguente viene illustrato uno stile *implicito* che utilizza il `CardViewControlTemplate`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:controls="clr-namespace:ControlTemplateDemos.Controls"
             ...>
    <ContentPage.Resources>
        <ControlTemplate x:Key="CardViewControlTemplate">
            ...
        </ControlTemplate>

        <Style TargetType="controls:CardView">
            <Setter Property="ControlTemplate"
                    Value="{StaticResource CardViewControlTemplate}" />
        </Style>
    </ContentPage.Resources>
    <StackLayout Margin="30">
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="John Doe"
                           CardDescription="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla elit dolor, convallis non interdum."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png" />
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Jane Doe"
                           CardDescription="Phasellus eu convallis mi. In tempus augue eu dignissim fermentum. Morbi ut lacus vitae eros lacinia."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png"/>
        <controls:CardView BorderColor="DarkGray"
                           CardTitle="Xamarin Monkey"
                           CardDescription="Aliquam sagittis, odio lacinia fermentum dictum, mi erat scelerisque erat, quis aliquet arcu."
                           IconBackgroundColor="SlateGray"
                           IconImageSource="user.png" />
    </StackLayout>
</ContentPage>
```

In questo esempio, [`Style`](xref:Xamarin.Forms.Style) l'implicita `CardView` viene applicata [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) automaticamente `CardView` a `CardViewControlTemplate`ogni oggetto e imposta la proprietà di ciascuno su . *implicit*

Per altre informazioni sugli stili, vedere [Stili di Xamarin.Forms](~/xamarin-forms/user-interface/styles/index.md).

## <a name="redefine-a-controls-ui"></a>Ridefinire l'interfaccia utente di un controllo

Quando [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) viene creata un'istanza `ControlTemplate` di [`ContentView`](xref:Xamarin.Forms.ContentView) un oggetto e [`ContentPage`](xref:Xamarin.Forms.ContentPage) assegnato alla proprietà di un controllo personalizzato derivato o di una `ControlTemplate`pagina derivata, la struttura visiva definita per il controllo o la pagina personalizzata viene sostituita con la struttura visiva definita nell'oggetto .

Ad esempio, il controllo personalizzato `CardViewUI` definisce la relativa interfaccia utente usando il codice XAML seguente:

```xaml
<ContentView xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="ControlTemplateDemos.Controls.CardViewUI"
             x:Name="this">
    <Frame BindingContext="{x:Reference this}"
           BackgroundColor="{Binding CardColor}"
           BorderColor="{Binding BorderColor}"
           CornerRadius="5"
           HasShadow="True"
           Padding="8"
           HorizontalOptions="Center"
           VerticalOptions="Center">
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="75" />
                <RowDefinition Height="4" />
                <RowDefinition Height="Auto" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="75" />
                <ColumnDefinition Width="200" />
            </Grid.ColumnDefinitions>
            <Frame IsClippedToBounds="True"
                   BorderColor="{Binding BorderColor, FallbackValue='Black'}"
                   BackgroundColor="{Binding IconBackgroundColor, FallbackValue='Gray'}"
                   CornerRadius="38"
                   HeightRequest="60"
                   WidthRequest="60"
                   HorizontalOptions="Center"
                   VerticalOptions="Center">
                <Image Source="{Binding IconImageSource}"
                       Margin="-20"
                       WidthRequest="100"
                       HeightRequest="100"
                       Aspect="AspectFill" />
            </Frame>
            <Label Grid.Column="1"
                   Text="{Binding CardTitle, FallbackValue='Card title'}"
                   FontAttributes="Bold"
                   FontSize="Large"
                   VerticalTextAlignment="Center"
                   HorizontalTextAlignment="Start" />
            <BoxView Grid.Row="1"
                     Grid.ColumnSpan="2"
                     BackgroundColor="{Binding BorderColor, FallbackValue='Black'}"
                     HeightRequest="2"
                     HorizontalOptions="Fill" />
            <Label Grid.Row="2"
                   Grid.ColumnSpan="2"
                   Text="{Binding CardDescription, FallbackValue='Card description'}"
                   VerticalTextAlignment="Start"
                   VerticalOptions="Fill"
                   HorizontalOptions="Fill" />
        </Grid>
    </Frame>
</ContentView>
```

Tuttavia, i controlli che costituiscono questa interfaccia utente [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)possono essere sostituiti [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) definendo `CardViewUI` una nuova struttura visiva in un oggetto e assegnandola alla proprietà di un oggetto:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             ...>
    <ContentPage.Resources>
        <ControlTemplate x:Key="CardViewCompressed">
            <Grid>
                <Grid.RowDefinitions>
                    <RowDefinition Height="100" />
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="100" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>
                <Image Source="{TemplateBinding IconImageSource}"
                        BackgroundColor="{TemplateBinding IconBackgroundColor}"
                        WidthRequest="100"
                        HeightRequest="100"
                        Aspect="AspectFill"
                        HorizontalOptions="Center"
                        VerticalOptions="Center" />
                <StackLayout Grid.Column="1">
                    <Label Text="{TemplateBinding CardTitle}"
                           FontAttributes="Bold" />
                    <Label Text="{TemplateBinding CardDescription}" />
                </StackLayout>
            </Grid>
        </ControlTemplate>
    </ContentPage.Resources>
    <StackLayout Margin="30">
        <controls:CardViewUI BorderColor="DarkGray"
                             CardTitle="John Doe"
                             CardDescription="Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nulla elit dolor, convallis non interdum."
                             IconBackgroundColor="SlateGray"
                             IconImageSource="user.png"
                             ControlTemplate="{StaticResource CardViewCompressed}" />
        <controls:CardViewUI BorderColor="DarkGray"
                             CardTitle="Jane Doe"
                             CardDescription="Phasellus eu convallis mi. In tempus augue eu dignissim fermentum. Morbi ut lacus vitae eros lacinia."
                             IconBackgroundColor="SlateGray"
                             IconImageSource="user.png"
                             ControlTemplate="{StaticResource CardViewCompressed}" />
        <controls:CardViewUI BorderColor="DarkGray"
                             CardTitle="Xamarin Monkey"
                             CardDescription="Aliquam sagittis, odio lacinia fermentum dictum, mi erat scelerisque erat, quis aliquet arcu."
                             IconBackgroundColor="SlateGray"
                             IconImageSource="user.png"
                             ControlTemplate="{StaticResource CardViewCompressed}" />
    </StackLayout>
</ContentPage>
```

In questo esempio, la `CardViewUI` struttura visiva dell'oggetto viene ridefinita in un che fornisce una struttura visiva più compatta adatta per un elenco condensato:In this example, the visual structure of the object is redefined in a [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) that provides a more compact visual structure that's suitable for a condensed list:

[![Screenshot degli oggetti CardViewUI basati su modelli, in iOS e Android](control-template-images/redefine-controltemplate.png "Oggetti CardViewUI basati su modelli")](control-template-images/redefine-controltemplate-large.png#lightbox "Oggetti CardViewUI basati su modelli")

## <a name="substitute-content-into-a-contentpresenter"></a>Sostituire il contenuto in un ContentPresenter

Un [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) può essere inserito in un modello di controllo per contrassegnare la posizione in cui il contenuto deve essere visualizzato dal controllo personalizzato basato su modelli o dalla pagina basata su modelli. Il controllo personalizzato o la pagina che utilizza il modello di controllo definirà quindi il contenuto che verrà visualizzato dal `ContentPresenter`. Il diagramma seguente illustra un `ControlTemplate` per una pagina che contiene alcuni controlli, tra cui un `ContentPresenter` contrassegnato da un rettangolo blu:

![Modello di controllo per ContentPage](control-template-images/control-template.png "Modello di controllo per ContentPage")

Il codice XAML seguente `TealTemplate` mostra un [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) modello di controllo denominato che contiene un nella relativa struttura visiva:The following XAML shows a control template named that contains a in its visual structure:

```xaml
<ControlTemplate x:Key="TealTemplate">
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="0.1*" />
            <RowDefinition Height="0.8*" />
            <RowDefinition Height="0.1*" />
        </Grid.RowDefinitions>
        <BoxView Color="Teal" />
        <Label Margin="20,0,0,0"
               Text="{TemplateBinding HeaderText}"
               TextColor="White"
               FontSize="Title"
               VerticalOptions="Center" />
        <ContentPresenter Grid.Row="1" />
        <BoxView Grid.Row="2"
                 Color="Teal" />
        <Label x:Name="changeThemeLabel"
               Grid.Row="2"
               Margin="20,0,0,0"
               Text="Change Theme"
               TextColor="White"
               HorizontalOptions="Start"
               VerticalOptions="Center">
            <Label.GestureRecognizers>
                <TapGestureRecognizer Tapped="OnChangeThemeLabelTapped" />
            </Label.GestureRecognizers>
        </Label>
        <controls:HyperlinkLabel Grid.Row="2"
                                 Margin="0,0,20,0"
                                 Text="Help"
                                 TextColor="White"
                                 Url="https://docs.microsoft.com/xamarin/xamarin-forms/"
                                 HorizontalOptions="End"
                                 VerticalOptions="Center" />
    </Grid>
</ControlTemplate>
```

Nell'esempio `TealTemplate` seguente viene [`ControlTemplate`](xref:Xamarin.Forms.TemplatedPage.ControlTemplate) illustrato [`ContentPage`](xref:Xamarin.Forms.ContentPage) assegnato alla proprietà di una pagina derivata:The following example shows assigned to the property of a derived page:

```xaml
<controls:HeaderFooterPage xmlns="http://xamarin.com/schemas/2014/forms"
                           xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                           xmlns:controls="clr-namespace:ControlTemplateDemos.Controls"                           
                           ControlTemplate="{StaticResource TealTemplate}"
                           HeaderText="MyApp"
                           ...>
    <StackLayout Margin="10">
        <Entry Placeholder="Enter username" />
        <Entry Placeholder="Enter password"
               IsPassword="True" />
        <Button Text="Login" />
    </StackLayout>
</controls:HeaderFooterPage>
```

In fase `TealTemplate` di esecuzione, quando viene applicato alla pagina, il contenuto della pagina viene sostituito nel [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) modello di controllo definito:

[![Screenshot dell'oggetto pagina basata su modelli, in iOS e Android](control-template-images/tealtemplate-contentpage.png "ContentPage basata su modelli")](control-template-images/tealtemplate-contentpage-large.png#lightbox "ContentPage basata su modelli")

## <a name="get-a-named-element-from-a-template"></a>Ottenere un elemento denominato da un modello

Gli elementi denominati in un modello di controllo possono essere recuperati dal controllo personalizzato basato su modelli o dalla pagina basata su modelli. Ciò può essere `GetTemplateChild` ottenuto con il metodo , [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) che restituisce l'elemento denominato nella struttura ad albero visuale di cui è stata creata un'istanza, se trovata. In caso contrario, viene restituito `null`.

Dopo la creazione di un'istanza di un modello di controllo, viene chiamato il metodo `OnApplyTemplate` del modello. Il metodo `GetTemplateChild` deve pertanto essere chiamato da un override di `OnApplyTemplate` nel controllo basato su modelli o nella pagina basata su modelli.

> [!IMPORTANT]
> Il metodo `GetTemplateChild` deve essere chiamato solo dopo la chiamata del metodo `OnApplyTemplate`.

Il codice XAML seguente `TealTemplate` mostra un modello [`ContentPage`](xref:Xamarin.Forms.ContentPage) di controllo denominato che può essere applicato alle pagine derivate:The following XAML shows a control template named that can be applied to derived pages:

```xaml
<ControlTemplate x:Key="TealTemplate">
    <Grid>
        ...
        <Label x:Name="changeThemeLabel"
               Grid.Row="2"
               Margin="20,0,0,0"
               Text="Change Theme"
               TextColor="White"
               HorizontalOptions="Start"
               VerticalOptions="Center">
            <Label.GestureRecognizers>
                <TapGestureRecognizer Tapped="OnChangeThemeLabelTapped" />
            </Label.GestureRecognizers>
        </Label>
        ...
    </Grid>
</ControlTemplate>
```

In questo esempio, l'elemento [`Label`](xref:Xamarin.Forms.Label) è denominato e può essere recuperato nel codice per la pagina basata su modelli. Si ottiene questo risultato chiamando il metodo `GetTemplateChild` dall'override di `OnApplyTemplate` per la pagina basata su modelli:

```csharp
public partial class AccessTemplateElementPage : HeaderFooterPage
{
    Label themeLabel;

    public AccessTemplateElementPage()
    {
        InitializeComponent();
    }

    protected override void OnApplyTemplate()
    {
        base.OnApplyTemplate();
        themeLabel = (Label)GetTemplateChild("changeThemeLabel");
        themeLabel.Text = OriginalTemplate ? "Aqua Theme" : "Teal Theme";
    }
}
```

In questo esempio, [`Label`](xref:Xamarin.Forms.Label) `changeThemeLabel` l'oggetto `ControlTemplate` denominato viene recuperato dopo la creazione di un'istanza dell'oggetto. La classe `AccessTemplateElementPage` può quindi accedere e modificare `changeThemeLabel`. Gli screenshot seguenti mostrano che il testo visualizzato da `Label` è stato modificato:

[![Screenshot dell'oggetto pagina basata su modelli, in iOS e Android](control-template-images/get-named-element.png "ContentPage basata su modelli")](control-template-images/get-named-element-large.png#lightbox "ContentPage basata su modelli")

## <a name="bind-to-a-viewmodel"></a>Associare a un ViewModel

Un'associazione [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) dati può essere associata a un viewmodel, anche quando l'associazione `ControlTemplate` all'elemento padre basato su modelli (l'istanza dell'oggetto runtime a cui viene applicato il modello).

Nell'esempio di codice XAML riportato di seguito viene illustrata una pagina che utilizza un elemento ViewModel denominato `PeopleViewModel`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:ControlTemplateDemos"
             xmlns:controls="clr-namespace:ControlTemplateDemos.Controls"
             ...>
    <ContentPage.BindingContext>
        <local:PeopleViewModel />
    </ContentPage.BindingContext>

    <ContentPage.Resources>
        <DataTemplate x:Key="PersonTemplate">
            <controls:CardView BorderColor="DarkGray"
                               CardTitle="{Binding Name}"
                               CardDescription="{Binding Description}"
                               ControlTemplate="{StaticResource CardViewControlTemplate}" />
        </DataTemplate>
    </ContentPage.Resources>

    <StackLayout Margin="10"
                 BindableLayout.ItemsSource="{Binding People}"
                 BindableLayout.ItemTemplate="{StaticResource PersonTemplate}" />
</ContentPage>
```

In questo esempio, il `BindingContext` della pagina è impostato su un'istanza di `PeopleViewModel`. Questo ViewModel espone una raccolta di `People` e un `ICommand` denominato `DeletePersonCommand`. Il [`StackLayout`](xref:Xamarin.Forms.StackLayout) nella pagina utilizza un layout associabile `People` per l'associazione dati alla raccolta e il `ItemTemplate` layout associabile è impostato sulla `PersonTemplate` risorsa. Specifica [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) che ogni elemento `People` della raccolta verrà visualizzato `CardView` utilizzando un oggetto . La struttura visiva `CardView` dell'oggetto [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) viene `CardViewControlTemplate`definita utilizzando un oggetto denominato :

```xaml
<ControlTemplate x:Key="CardViewControlTemplate">
    <Frame BindingContext="{Binding Source={RelativeSource TemplatedParent}}"
           BackgroundColor="{Binding CardColor}"
           BorderColor="{Binding BorderColor}"
           CornerRadius="5"
           HasShadow="True"
           Padding="8"
           HorizontalOptions="Center"
           VerticalOptions="Center">
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="75" />
                <RowDefinition Height="4" />
                <RowDefinition Height="Auto" />
            </Grid.RowDefinitions>
            <Label Text="{Binding CardTitle}"
                   FontAttributes="Bold"
                   FontSize="Large"
                   VerticalTextAlignment="Center"
                   HorizontalTextAlignment="Start" />
            <BoxView Grid.Row="1"
                     BackgroundColor="{Binding BorderColor}"
                     HeightRequest="2"
                     HorizontalOptions="Fill" />
            <Label Grid.Row="2"
                   Text="{Binding CardDescription}"
                   VerticalTextAlignment="Start"
                   VerticalOptions="Fill"
                   HorizontalOptions="Fill" />
            <Button Text="Delete"
                    Command="{Binding Source={RelativeSource AncestorType={x:Type local:PeopleViewModel}}, Path=DeletePersonCommand}"
                    CommandParameter="{Binding CardTitle}"
                    HorizontalOptions="End" />
        </Grid>
    </Frame>
</ControlTemplate>
```

In questo esempio, l'elemento radice di [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) è un [`Frame`](xref:Xamarin.Forms.Frame) oggetto. L'oggetto `Frame` usa l'estensione di markup `RelativeSource` per impostare il relativo `BindingContext` sull'elemento padre basato su modelli. Le espressioni di associazione dell'oggetto `Frame` e dei relativi figli vengono risolte in base alle proprietà di `CardView`, dato che `BindingContext` viene ereditato dall'elemento `Frame` radice. Gli screenshot seguenti mostrano la pagina che visualizza la raccolta `People`, costituita da tre elementi:

[![Screenshot degli oggetti CardView basati su modelli, in iOS e Android](control-template-images/viewmodel-controltemplate.png "Oggetti CardView basati su modelli")](control-template-images/viewmodel-controltemplate-large.png#lightbox "Oggetti CardView basati su modelli")

Mentre gli oggetti [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) nel associare alle proprietà [`Button`](xref:Xamarin.Forms.Button) nel relativo elemento padre basato su modelli, `DeletePersonCommand` l'interno del modello di controllo viene associato sia al relativo elemento padre basato su modelli e a nel viewmodel. Ciò è `Button.Command` dovuto al fatto che la proprietà ridefinisce l'origine `PeopleViewModel`dell'associazione in modo che sia il contesto di associazione del predecessore il cui tipo di contesto di associazione è , ovvero . [`StackLayout`](xref:Xamarin.Forms.StackLayout) La parte `Path` delle espressioni di associazione può quindi risolvere la proprietà `DeletePersonCommand`. Tuttavia, `Button.CommandParameter` la proprietà non modifica l'origine dell'associazione, [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)ereditandola invece dal relativo elemento padre nell'oggetto . Pertanto, la proprietà `CommandParameter` viene associata alla proprietà `CardTitle` di `CardView`.

[`Button`](xref:Xamarin.Forms.Button) L'effetto complessivo delle associazioni `Button` è che quando `DeletePersonCommand` viene `PeopleViewModel` toccato, viene eseguito `CardName` il nella classe, con il valore della proprietà passata a . `DeletePersonCommand` Ciò comporta la rimozione dell'oggetto `CardView` specificato dal layout associabile:

[![Screenshot degli oggetti CardView basati su modelli, in iOS e Android](control-template-images/viewmodel-itemdeleted.png "Oggetti CardView basati su modelli")](control-template-images/viewmodel-itemdeleted-large.png#lightbox "Oggetti CardView basati su modelli")

Per altre informazioni sulle associazioni relative, vedere [Associazioni relative di Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md).

## <a name="related-links"></a>Collegamenti correlati

- [ControlTemplateDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplatedemos)
- [ContentView di Xamarin.Forms](~/xamarin-forms/user-interface/layouts/contentview.md)
- [Associazioni relative di Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md)
- [Dizionari risorse di Xamarin.Forms](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Associazione di dati di Xamarin.Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Stili di Xamarin.Forms](~/xamarin-forms/user-interface/styles/index.md)
