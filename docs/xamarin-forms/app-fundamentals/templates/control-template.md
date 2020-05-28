---
title: Xamarin.Formsmodelli di controllo
description: Xamarin.Formsi modelli di controllo definiscono la struttura visiva dei controlli personalizzati derivati da ContentView e le pagine derivate di ContentPage.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 44eebed2a49fbdda5504f9a09873f93466d0326c
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84132548"
---
# <a name="xamarinforms-control-templates"></a>Xamarin.Formsmodelli di controllo

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplatedemos)

Xamarin.Formsi modelli di controllo consentono di definire la struttura visiva dei [`ContentView`](xref:Xamarin.Forms.ContentView) controlli personalizzati derivati e delle [`ContentPage`](xref:Xamarin.Forms.ContentPage) pagine derivate. I modelli di controllo separano l'interfaccia utente (UI) per un controllo personalizzato o una pagina dalla logica che implementa il controllo o la pagina. Il contenuto aggiuntivo può anche essere inserito nel controllo personalizzato basato su modelli, o nella pagina basata su modelli, in una posizione predefinita.

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

La `CardView` classe, che deriva dalla [`ContentView`](xref:Xamarin.Forms.ContentView) classe, rappresenta un controllo personalizzato che Visualizza i dati in un layout simile a una scheda. La classe contiene proprietà, supportate da proprietà associabili, per i dati visualizzati. Tuttavia, la classe `CardView` non definisce alcuna interfaccia utente. L'interfaccia utente verrà invece definita con un modello di controllo. Per ulteriori informazioni sulla creazione di `ContentView` controlli personalizzati derivati, vedere [ Xamarin.Forms contentView](~/xamarin-forms/user-interface/layouts/contentview.md).

Viene creato un modello di controllo con il [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) tipo. Quando si crea un oggetto `ControlTemplate` , si combinano [`View`](xref:Xamarin.Forms.View) gli oggetti per compilare l'interfaccia utente per un controllo o una pagina personalizzata. Un `ControlTemplate` deve avere un solo oggetto `View` come elemento radice. Tuttavia, l'elemento radice contiene in genere altri oggetti `View`. La combinazione di oggetti crea la struttura visiva del controllo.

Mentre un oggetto [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) può essere definito inline, l'approccio tipico per dichiarare un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) è come una risorsa in un dizionario risorse. Poiché i modelli di controllo sono risorse, rispettano le stesse regole di ambito che si applicano a tutte le risorse. Ad esempio, se si dichiara un modello di controllo nell'elemento radice del file XAML di definizione dell'applicazione, il modello può essere usato ovunque nell'applicazione. Se si definisce il modello in una pagina, solo tale pagina può usare il modello di controllo. Per altre informazioni sulle risorse, vedere [ Xamarin.Forms dizionari risorse](~/xamarin-forms/xaml/resource-dictionaries.md).

Nell'esempio di codice XAML seguente viene illustrato un oggetto [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) per `CardView` gli oggetti:

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

Quando un oggetto [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) viene dichiarato come risorsa, deve disporre di una chiave specificata con l' `x:Key` attributo in modo che possa essere identificata nel dizionario risorse. In questo esempio, l'elemento radice del `CardViewControlTemplate` è un oggetto [`Frame`](xref:Xamarin.Forms.Frame). L'oggetto `Frame` usa l'estensione di markup `RelativeSource` per impostare il relativo `BindingContext` sull'istanza dell'oggetto di runtime a cui verrà applicato il modello, noto come *oggetto padre basato su modelli*. L' `Frame` oggetto utilizza una combinazione di [`Grid`](xref:Xamarin.Forms.Grid) `Frame` oggetti,, [`Image`](xref:Xamarin.Forms.Image) , [`Label`](xref:Xamarin.Forms.Label) e [`BoxView`](xref:Xamarin.Forms.BoxView) per definire la struttura visiva di un `CardView` oggetto. Le espressioni di associazione di questi oggetti vengono risolte in base alle proprietà di `CardView`, dato che `BindingContext` viene ereditato dall'elemento `Frame` radice. Per ulteriori informazioni sull' `RelativeSource` estensione di markup, vedere [ Xamarin.Forms associazioni relative](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md).

## <a name="consume-a-controltemplate"></a>Utilizzare un ControlTemplate

Un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) oggetto può essere applicato a un [`ContentView`](xref:Xamarin.Forms.ContentView) controllo personalizzato derivato impostando la relativa [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) proprietà sull'oggetto modello di controllo. Analogamente, è [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) possibile applicare un oggetto a una pagina derivata impostando la [`ContentPage`](xref:Xamarin.Forms.ContentPage) relativa [`ControlTemplate`](xref:Xamarin.Forms.TemplatedPage.ControlTemplate) proprietà sull'oggetto modello di controllo. In fase di esecuzione, quando [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) viene applicato un oggetto, tutti i controlli definiti in `ControlTemplate` vengono aggiunti alla struttura ad albero visuale del controllo personalizzato basato su modelli o della pagina basata su modelli.

Nell'esempio seguente viene illustrato l'oggetto `CardViewControlTemplate` assegnato alla [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) proprietà di ogni `CardView` oggetto:

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

In questo esempio, i controlli nel `CardViewControlTemplate` diventano parte della struttura ad albero visuale per ogni oggetto `CardView`. Poiché l' [`Frame`](xref:Xamarin.Forms.Frame) oggetto radice per il modello di controllo imposta la proprietà sull' `BindingContext` elemento padre basato su modelli, `Frame` e i relativi elementi figlio risolvono le espressioni di associazione in base alle proprietà di ogni `CardView` oggetto.

Gli screenshot seguenti mostrano il `CardViewControlTemplate` applicato ai tre oggetti `CardView`:

[![Screenshot degli oggetti CardView basati su modelli, in iOS e Android](control-template-images/relativesource-controltemplate.png "Oggetti CardView basati su modelli")](control-template-images/relativesource-controltemplate-large.png#lightbox "Oggetti CardView basati su modelli")

> [!IMPORTANT]
> Il momento in cui un oggetto [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) viene applicato a un'istanza del controllo può essere rilevato eseguendo l'override del `OnApplyTemplate` metodo nel controllo personalizzato basato su modelli o nella pagina basata su modelli. Per altre informazioni, vedere [Ottenere un elemento denominato da un modello](#get-a-named-element-from-a-template).

## <a name="pass-parameters-with-templatebinding"></a>Passare parametri con TemplateBinding

L' `TemplateBinding` estensione di markup associa una proprietà di un elemento che si trova in un oggetto [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) a una proprietà pubblica definita dal controllo personalizzato basato su modelli o dalla pagina basata su modelli. Quando si usa `TemplateBinding`, si consente alle proprietà del controllo di fungere da parametri per il modello. Pertanto, quando viene impostata una proprietà per un controllo personalizzato basato su modelli o una pagina basata su modelli, tale valore viene passato all'elemento per cui è stato definito il `TemplateBinding`.

> [!IMPORTANT]
> L' `TemplateBinding` estensione di markup è un'alternativa alla creazione di un oggetto [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) che usa l' `RelativeSource` estensione di markup per impostare la proprietà `BindingContext` dell'elemento radice nel modello sul relativo elemento padre basato su modelli. L'estensione di markup `TemplateBinding` elimina l'associazione `RelativeSource` e sostituisce le espressioni `Binding` con espressioni `TemplateBinding`.

L'estensione di markup `TemplateBinding` definisce le proprietà seguenti:

- `Path`, di tipo `string`, il percorso della proprietà.
- `Mode`, di tipo `BindingMode`, la direzione in cui le modifiche vengono propagate tra l'*origine* e la *destinazione*.
- `Converter`, di tipo `IValueConverter`, il convertitore di valori di associazione.
- `ConverterParameter`, di tipo `object`, il parametro per il convertitore di valori di associazione.
- `StringFormat`, di tipo `string`, il formato di stringa per l'associazione.

`ContentProperty` per l'estensione di markup `TemplateBinding` è `Path`. È pertanto possibile omettere la parte "Path=" dell'estensione di markup se il percorso è il primo elemento nell'espressione `TemplateBinding`. Per ulteriori informazioni sull'utilizzo di queste proprietà in un'espressione di associazione, vedere [ Xamarin.Forms Data Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md).

> [!WARNING]
> L' `TemplateBinding` estensione di markup deve essere usata solo all'interno di un oggetto [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) . Tuttavia, se si tenta di usare un'espressione `TemplateBinding` al di fuori di un `ControlTemplate` non verrà generato un errore di compilazione o un'eccezione.

Nell'esempio di codice XAML riportato di seguito viene illustrato un oggetto [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) per `CardView` gli oggetti che utilizza l' `TemplateBinding` estensione di markup:

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

In questo esempio, l'oggetto *implicito* [`Style`](xref:Xamarin.Forms.Style) viene applicato automaticamente a ogni `CardView` oggetto e imposta la [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) proprietà di ogni oggetto `CardView` su `CardViewControlTemplate` .

Per ulteriori informazioni sugli stili, vedere [ Xamarin.Forms stili](~/xamarin-forms/user-interface/styles/index.md).

## <a name="redefine-a-controls-ui"></a>Ridefinire l'interfaccia utente di un controllo

Quando viene creata un'istanza di un oggetto [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) e viene assegnato alla `ControlTemplate` proprietà di un [`ContentView`](xref:Xamarin.Forms.ContentView) controllo personalizzato derivato, o a una [`ContentPage`](xref:Xamarin.Forms.ContentPage) pagina derivata, la struttura visiva definita per il controllo o la pagina personalizzata viene sostituita con la struttura visiva definita in `ControlTemplate` .

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

Tuttavia, i controlli che comprendono questa interfaccia utente possono essere sostituiti definendo una nuova struttura visiva in un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) oggetto e assegnando tale struttura alla [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) proprietà di un `CardViewUI` oggetto:

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

In questo esempio, la struttura visiva dell' `CardViewUI` oggetto viene ridefinita in un oggetto [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) che fornisce una struttura visiva più compatta adatta per un elenco condensato:

[![Screenshot degli oggetti CardViewUI basati su modelli, in iOS e Android](control-template-images/redefine-controltemplate.png "Oggetti CardViewUI basati su modelli")](control-template-images/redefine-controltemplate-large.png#lightbox "Oggetti CardViewUI basati su modelli")

## <a name="substitute-content-into-a-contentpresenter"></a>Sostituire il contenuto in un ContentPresenter

Un oggetto [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) può essere inserito in un modello di controllo per contrassegnare il punto in cui verrà visualizzato il contenuto da visualizzare nel controllo personalizzato basato su modelli o nella pagina basata su modelli. Il controllo personalizzato o la pagina che utilizza il modello di controllo definirà quindi il contenuto che verrà visualizzato dal `ContentPresenter`. Il diagramma seguente illustra un `ControlTemplate` per una pagina che contiene alcuni controlli, tra cui un `ContentPresenter` contrassegnato da un rettangolo blu:

![Modello di controllo per ContentPage](control-template-images/control-template.png "Modello di controllo per ContentPage")

Il codice XAML seguente mostra un modello di controllo denominato `TealTemplate` che contiene un oggetto [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) nella relativa struttura visiva:

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

Nell'esempio seguente viene mostrato `TealTemplate` assegnato alla [`ControlTemplate`](xref:Xamarin.Forms.TemplatedPage.ControlTemplate) proprietà di una [`ContentPage`](xref:Xamarin.Forms.ContentPage) pagina derivata:

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

In fase di esecuzione, quando `TealTemplate` viene applicato alla pagina, il contenuto della pagina viene sostituito in [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) definito nel modello di controllo:

[![Screenshot dell'oggetto pagina basata su modelli, in iOS e Android](control-template-images/tealtemplate-contentpage.png "ContentPage basata su modelli")](control-template-images/tealtemplate-contentpage-large.png#lightbox "ContentPage basata su modelli")

## <a name="get-a-named-element-from-a-template"></a>Ottenere un elemento denominato da un modello

Gli elementi denominati in un modello di controllo possono essere recuperati dal controllo personalizzato basato su modelli o dalla pagina basata su modelli. Questa operazione può essere eseguita con il `GetTemplateChild` metodo, che restituisce l'elemento denominato nella [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) struttura ad albero visuale di cui è stata creata un'istanza, se è stato trovato. In caso contrario, viene restituito `null`.

Dopo la creazione di un'istanza di un modello di controllo, viene chiamato il metodo `OnApplyTemplate` del modello. Il metodo `GetTemplateChild` deve pertanto essere chiamato da un override di `OnApplyTemplate` nel controllo basato su modelli o nella pagina basata su modelli.

> [!IMPORTANT]
> Il metodo `GetTemplateChild` deve essere chiamato solo dopo la chiamata del metodo `OnApplyTemplate`.

Il codice XAML seguente mostra un modello di controllo denominato `TealTemplate` che può essere applicato alle [`ContentPage`](xref:Xamarin.Forms.ContentPage) pagine derivate:

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

In questo esempio, l' [`Label`](xref:Xamarin.Forms.Label) elemento è denominato e può essere recuperato nel codice per la pagina basata su modelli. Si ottiene questo risultato chiamando il metodo `GetTemplateChild` dall'override di `OnApplyTemplate` per la pagina basata su modelli:

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

In questo esempio, l' [`Label`](xref:Xamarin.Forms.Label) oggetto denominato `changeThemeLabel` viene recuperato dopo che `ControlTemplate` è stata creata un'istanza di. La classe `AccessTemplateElementPage` può quindi accedere e modificare `changeThemeLabel`. Gli screenshot seguenti mostrano che il testo visualizzato da `Label` è stato modificato:

[![Screenshot dell'oggetto pagina basata su modelli, in iOS e Android](control-template-images/get-named-element.png "ContentPage basata su modelli")](control-template-images/get-named-element-large.png#lightbox "ContentPage basata su modelli")

## <a name="bind-to-a-viewmodel"></a>Associare a un ViewModel

[`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)È possibile eseguire l'associazione di dati a un ViewModel, anche quando l' `ControlTemplate` oggetto è associato all'elemento padre basato su modelli (l'istanza dell'oggetto di runtime a cui è applicato il modello).

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

In questo esempio, il `BindingContext` della pagina è impostato su un'istanza di `PeopleViewModel`. Questo ViewModel espone una raccolta di `People` e un `ICommand` denominato `DeletePersonCommand`. Nella [`StackLayout`](xref:Xamarin.Forms.StackLayout) pagina viene utilizzato un layout associabile per l'associazione dati alla `People` raccolta e l'oggetto `ItemTemplate` del layout associabile viene impostato sulla `PersonTemplate` risorsa. [`DataTemplate`](xref:Xamarin.Forms.DataTemplate)Specifica che ogni elemento della `People` raccolta verrà visualizzato utilizzando un `CardView` oggetto. La struttura visiva dell' `CardView` oggetto viene definita utilizzando un oggetto [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) denominato `CardViewControlTemplate` :

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

Mentre gli oggetti nell' [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) associazione alle proprietà nel relativo elemento padre basato su modelli, all' [`Button`](xref:Xamarin.Forms.Button) interno del modello di controllo si associa sia al relativo elemento padre basato su modelli sia a `DeletePersonCommand` nell'oggetto ViewModel. Questo è dovuto al fatto `Button.Command` che la proprietà ridefinisce l'origine di associazione come contesto di associazione del predecessore il cui tipo di contesto di associazione è `PeopleViewModel` , ovvero [`StackLayout`](xref:Xamarin.Forms.StackLayout) . La parte `Path` delle espressioni di associazione può quindi risolvere la proprietà `DeletePersonCommand`. Tuttavia, la `Button.CommandParameter` proprietà non modifica l'origine del binding, bensì eredita dal relativo elemento padre in [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) . Pertanto, la proprietà `CommandParameter` viene associata alla proprietà `CardTitle` di `CardView`.

L'effetto complessivo dei [`Button`](xref:Xamarin.Forms.Button) binding è che, quando `Button` viene toccato, viene eseguito l'oggetto `DeletePersonCommand` nella `PeopleViewModel` classe con il valore della `CardName` proprietà passata a `DeletePersonCommand` . Ciò comporta la rimozione dell'oggetto `CardView` specificato dal layout associabile:

[![Screenshot degli oggetti CardView basati su modelli, in iOS e Android](control-template-images/viewmodel-itemdeleted.png "Oggetti CardView basati su modelli")](control-template-images/viewmodel-itemdeleted-large.png#lightbox "Oggetti CardView basati su modelli")

Per ulteriori informazioni sulle associazioni relative, vedere [ Xamarin.Forms associazioni relative](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md).

## <a name="related-links"></a>Collegamenti correlati

- [ControlTemplateDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplatedemos)
- [Xamarin.FormsContentView](~/xamarin-forms/user-interface/layouts/contentview.md)
- [Xamarin.FormsAssociazioni relative](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md)
- [Xamarin.FormsDizionari risorse](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Xamarin.FormsData Binding](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Xamarin.FormsStili](~/xamarin-forms/user-interface/styles/index.md)
