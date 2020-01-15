---
title: Modelli di controllo Novell. Forms
description: I modelli di controllo Novell. Forms definiscono la struttura visiva dei controlli personalizzati derivati da ContentView e le pagine derivate ContentPage.
ms.prod: xamarin
ms.assetid: 8B8E2360-6531-44A3-A7C8-9A8808DE9B86
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/13/2020
ms.openlocfilehash: 707e105b8535cbbb2819c5b8daeaa32bf6c5da37
ms.sourcegitcommit: 211fed94fb96127a3e158ae1ff5d7eb831a203d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75956595"
---
# <a name="xamarinforms-control-templates"></a>Modelli di controllo Novell. Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplatedemos)

I modelli di controllo Novell. Forms consentono di definire la struttura visiva di [`ContentView`](xref:Xamarin.Forms.ContentView) controlli personalizzati derivati e [`ContentPage`](xref:Xamarin.Forms.ContentPage) pagine derivate. I modelli di controllo separano l'interfaccia utente (UI) per un controllo personalizzato o una pagina dalla logica che implementa il controllo o la pagina. Il contenuto aggiuntivo può essere inserito anche nel controllo personalizzato basato su modelli, o nella pagina basata su modelli, in una posizione predefinita.

È ad esempio possibile creare un modello di controllo per ridefinire l'interfaccia utente fornita da un controllo personalizzato. Il modello di controllo può quindi essere utilizzato dall'istanza del controllo personalizzato richiesta. In alternativa, è possibile creare un modello di controllo che definisce qualsiasi interfaccia utente comune che verrà usata da più pagine in un'applicazione. Il modello di controllo può quindi essere utilizzato da più pagine, con ogni pagina che visualizza ancora il contenuto univoco.

## <a name="create-a-controltemplate"></a>Creare un ControlTemplate

Nell'esempio seguente viene illustrato il codice per un `CardView` controllo personalizzato:

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

La classe `CardView`, che deriva dalla classe [`ContentView`](xref:Xamarin.Forms.ContentView) , rappresenta un controllo personalizzato che Visualizza i dati in un layout simile a una scheda. La classe contiene proprietà, supportate da proprietà associabili, per i dati visualizzati. Tuttavia, la classe `CardView` non definisce alcuna interfaccia utente. L'interfaccia utente verrà invece definita con un modello di controllo. Per ulteriori informazioni sulla creazione di `ContentView` controlli personalizzati derivati, vedere [Novell. Forms contentView](~/xamarin-forms/user-interface/layouts/contentview.md).

Viene creato un modello di controllo con il tipo di [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) . Quando si crea una `ControlTemplate`, si combinano [`View`](xref:Xamarin.Forms.View) oggetti per compilare l'interfaccia utente per un controllo o una pagina personalizzata. Un `ControlTemplate` deve avere un solo `View` come elemento radice. Tuttavia, l'elemento radice contiene in genere altri oggetti `View`. La combinazione degli oggetti costituisce la struttura visiva del controllo.

Mentre un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) può essere definito inline, l'approccio tipico per dichiarare una [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) è come una risorsa in un dizionario risorse. Poiché i modelli di controllo sono risorse, rispettano le stesse regole di ambito che si applicano a tutte le risorse. Se, ad esempio, si dichiara un modello di controllo nell'elemento radice del file XAML della definizione dell'applicazione, il modello può essere usato in qualsiasi punto dell'applicazione. Se si definisce il modello in una pagina, solo la pagina può usare il modello di controllo. Per altre informazioni sulle risorse, vedere [dizionari risorse Novell. Forms](~/xamarin-forms/xaml/resource-dictionaries.md).

Nell'esempio di codice XAML seguente viene illustrato un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) per gli oggetti `CardView`:

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

Quando una [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) viene dichiarata come risorsa, deve disporre di una chiave specificata con l'attributo `x:Key` in modo che possa essere identificata nel dizionario risorse. In questo esempio, l'elemento radice del `CardViewControlTemplate` è un oggetto [`Frame`](xref:Xamarin.Forms.Frame) . L'oggetto `Frame` usa l'estensione di markup `RelativeSource` per impostare la relativa `BindingContext` sull'istanza dell'oggetto di runtime a cui verrà applicato il modello, noto come *elemento padre basato su modelli*. L'oggetto `Frame` utilizza una combinazione di oggetti [`Grid`](xref:Xamarin.Forms.Grid), `Frame`, [`Image`](xref:Xamarin.Forms.Image), [`Label`](xref:Xamarin.Forms.Label)e [`BoxView`](xref:Xamarin.Forms.BoxView) per definire la struttura visiva di un oggetto `CardView`. Le espressioni di associazione di questi oggetti vengono risolte in base a `CardView` proprietà, a causa dell'eredità della `BindingContext` dall'elemento `Frame` radice. Per ulteriori informazioni sull'estensione di markup `RelativeSource`, vedere [binding relativi di Novell. Forms](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md).

## <a name="consume-a-controltemplate"></a>Utilizzare un oggetto ControlTemplate

Un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) può essere applicato a un controllo personalizzato derivato da [`ContentView`](xref:Xamarin.Forms.ContentView) impostando la relativa proprietà [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) sull'oggetto modello di controllo. Analogamente, è possibile applicare una [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) a una pagina derivata [`ContentPage`](xref:Xamarin.Forms.ContentPage) impostando la relativa proprietà [`ControlTemplate`](xref:Xamarin.Forms.TemplatedPage.ControlTemplate) sull'oggetto modello di controllo. In fase di esecuzione, quando viene applicata una [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) , tutti i controlli definiti nel `ControlTemplate` vengono aggiunti alla struttura ad albero visuale del controllo personalizzato basato su modelli o della pagina basata su modelli.

Nell'esempio seguente viene illustrato il `CardViewControlTemplate` assegnato alla proprietà [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) di ogni oggetto `CardView`:

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

In questo esempio, i controlli nella `CardViewControlTemplate` diventano parte della struttura ad albero visuale per ogni oggetto `CardView`. Poiché l'oggetto [`Frame`](xref:Xamarin.Forms.Frame) radice per il modello di controllo imposta la relativa `BindingContext` sull'elemento padre basato su modelli, il `Frame` e i relativi elementi figlio risolvono le espressioni di associazione in base alle proprietà di ogni oggetto `CardView`.

Gli screenshot seguenti mostrano il `CardViewControlTemplate` applicato ai tre oggetti `CardView`:

[![Screenshot degli oggetti CardView basati su modelli, in iOS e Android](control-template-images/relativesource-controltemplate.png "Oggetti CardView basati su modelli")](control-template-images/relativesource-controltemplate-large.png#lightbox "Oggetti CardView basati su modelli")

> [!IMPORTANT]
> Il momento in cui un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) viene applicato a un'istanza del controllo può essere rilevato eseguendo l'override del metodo `OnApplyTemplate` nel controllo personalizzato basato su modelli o nella pagina basata su modelli. Per altre informazioni, vedere [ottenere un elemento denominato da un modello](#get-a-named-element-from-a-template).

## <a name="pass-parameters-with-templatebinding"></a>Passare parametri con TemplateBinding

L'estensione di markup `TemplateBinding` associa una proprietà di un elemento che si trova in un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) a una proprietà pubblica definita dal controllo personalizzato basato su modelli o dalla pagina basata su modelli. Quando si usa un `TemplateBinding`, si abilitano le proprietà del controllo in modo che fungano da parametri per il modello. Pertanto, quando viene impostata una proprietà in un controllo personalizzato basato su modelli o in una pagina basata su modelli, tale valore viene passato all'elemento in cui è presente il `TemplateBinding`.

> [!IMPORTANT]
> L'estensione di markup `TemplateBinding` rappresenta un'alternativa alla creazione di una [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) che usa l'estensione di markup `RelativeSource` per impostare il `BindingContext` dell'elemento radice nel modello sul relativo elemento padre basato su modelli. Il `TemplateBinding` estensione di markup Elimina l'associazione `RelativeSource` e sostituisce le espressioni di `Binding` con espressioni `TemplateBinding`.

L'estensione di markup `TemplateBinding` definisce le proprietà seguenti:

- `Path`, di tipo `string`, il percorso della proprietà.
- `Mode`, di tipo `BindingMode`, la direzione in cui le modifiche vengono propagate tra l' *origine* e la *destinazione*.
- `Converter`, di tipo `IValueConverter`, il convertitore di valori di associazione.
- `ConverterParameter`, di tipo `object`, il parametro per il convertitore di valori di associazione.
- `StringFormat`, di tipo `string`, il formato di stringa per l'associazione.

Il `ContentProperty` per l'estensione di markup `TemplateBinding` è `Path`. È pertanto possibile omettere la parte "Path =" dell'estensione di markup se il percorso è il primo elemento nell'espressione `TemplateBinding`. Per ulteriori informazioni sull'utilizzo di queste proprietà in un'espressione di associazione, vedere [Data Binding Novell. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md).

> [!WARNING]
> L'estensione di markup `TemplateBinding` deve essere utilizzata solo all'interno di un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate). Tuttavia, se si tenta di utilizzare un'espressione `TemplateBinding` al di fuori di una `ControlTemplate` non verrà generato un errore di compilazione o verrà generata un'eccezione.

Nell'esempio di codice XAML riportato di seguito viene illustrato un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) per `CardView` oggetti che utilizza l'estensione di markup `TemplateBinding`:

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
> L'uso dell'estensione di markup `TemplateBinding` equivale a impostare l'`BindingContext` dell'elemento radice nel modello sul relativo elemento padre basato su modelli con l'estensione di markup `RelativeSource` e quindi la risoluzione dei binding degli oggetti figlio con l'estensione di markup `Binding`. Il `TemplateBinding` estensione di markup crea infatti un `Binding` il cui `Source` è `RelativeBindingSource.TemplatedParent`.

## <a name="apply-a-controltemplate-with-a-style"></a>Applicare un oggetto ControlTemplate con uno stile

I modelli di controllo possono essere applicati anche con gli stili. Questa operazione viene eseguita creando uno stile *implicito* o *esplicito* che utilizza il [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate).

Nell'esempio di codice XAML riportato di seguito viene illustrato uno stile *implicito* che utilizza la `CardViewControlTemplate`:

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

In questo esempio, il [`Style`](xref:Xamarin.Forms.Style) *implicito* viene applicato automaticamente a ogni oggetto `CardView` e imposta la proprietà [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) di ogni `CardView` su `CardViewControlTemplate`.

Per ulteriori informazioni sugli stili, vedere [Novell. Forms Styles](~/xamarin-forms/user-interface/styles/index.md).

## <a name="redefine-a-controls-ui"></a>Ridefinire l'interfaccia utente di un controllo

Quando si crea un'istanza di un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) e la si assegna alla proprietà `ControlTemplate` di un controllo personalizzato derivato [`ContentView`](xref:Xamarin.Forms.ContentView) o a una pagina derivata [`ContentPage`](xref:Xamarin.Forms.ContentPage) , la struttura visiva definita per il controllo o la pagina personalizzata viene sostituita dalla struttura visiva definita nella `ControlTemplate`.

Ad esempio, il `CardViewUI` controllo personalizzato definisce la relativa interfaccia utente usando il codice XAML seguente:

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

Tuttavia, i controlli che compongono questa interfaccia utente possono essere sostituiti definendo una nuova struttura visiva in una [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate)e assegnando tale struttura alla proprietà [`ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) di un oggetto `CardViewUI`:

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

In questo esempio, la struttura visiva dell'oggetto `CardViewUI` viene ridefinita in una [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) che fornisce una struttura visiva più compatta adatta per un elenco con densità:

[![Screenshot degli oggetti CardViewUI basati su modelli, in iOS e Android](control-template-images/redefine-controltemplate.png "Oggetti CardViewUI basati su modelli")](control-template-images/redefine-controltemplate-large.png#lightbox "Oggetti CardViewUI basati su modelli")

## <a name="substitute-content-into-a-contentpresenter"></a>Sostituisci contenuto in un ContentPresenter

Un [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) può essere inserito in un modello di controllo per contrassegnare il punto in cui verrà visualizzato il contenuto da visualizzare nel controllo personalizzato basato su modelli o nella pagina basata su modelli. Il controllo personalizzato o la pagina che utilizza il modello di controllo definirà quindi il contenuto che verrà visualizzato dal `ContentPresenter`. Il diagramma seguente illustra un `ControlTemplate` per una pagina che contiene alcuni controlli, tra cui un `ContentPresenter` contrassegnato da un rettangolo blu:

![Modello di controllo per un ContentPage](control-template-images/control-template.png "Modello di controllo per un ContentPage")

Il codice XAML seguente mostra un modello di controllo denominato `TealTemplate` che contiene una [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) nella relativa struttura visiva:

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

Nell'esempio seguente viene illustrato `TealTemplate` assegnato alla proprietà [`ControlTemplate`](xref:Xamarin.Forms.TemplatedPage.ControlTemplate) di una pagina derivata [`ContentPage`](xref:Xamarin.Forms.ContentPage) :

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

In fase di esecuzione, quando `TealTemplate` viene applicato alla pagina, il contenuto della pagina viene sostituito nel [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) definito nel modello di controllo:

[![Screenshot dell'oggetto pagina basata su modelli, in iOS e Android](control-template-images/tealtemplate-contentpage.png "ContentPage basato su modelli")](control-template-images/tealtemplate-contentpage-large.png#lightbox "ContentPage basato su modelli")

## <a name="get-a-named-element-from-a-template"></a>Ottenere un elemento denominato da un modello

Gli elementi denominati in un modello di controllo possono essere recuperati dal controllo personalizzato basato su modelli o dalla pagina basata su modelli. Questa operazione può essere eseguita con il metodo `GetTemplateChild`, che restituisce l'elemento denominato nella struttura ad albero visuale [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) di cui è stata creata un'istanza, se è stato trovato. In caso contrario restituirà `null`.

Dopo la creazione di un'istanza di un modello di controllo, viene chiamato il metodo `OnApplyTemplate` del modello. Il metodo `GetTemplateChild` deve pertanto essere chiamato da un override di `OnApplyTemplate` nel controllo basato su modelli o nella pagina basata su modelli.

> [!IMPORTANT]
> Il metodo `GetTemplateChild` deve essere chiamato solo dopo la chiamata del metodo `OnApplyTemplate`.

Il codice XAML seguente mostra un modello di controllo denominato `TealTemplate` che è possibile applicare alle pagine [`ContentPage`](xref:Xamarin.Forms.ContentPage) derivate:

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

In questo esempio, l'elemento [`Label`](xref:Xamarin.Forms.Label) è denominato e può essere recuperato nel codice per la pagina basata su modelli. Questa operazione viene eseguita chiamando il metodo `GetTemplateChild` dalla sostituzione `OnApplyTemplate` per la pagina basata su modelli:

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

In questo esempio, l'oggetto [`Label`](xref:Xamarin.Forms.Label) denominato `changeThemeLabel` viene recuperato dopo che è stata creata un'istanza della `ControlTemplate`. La classe `AccessTemplateElementPage` può quindi accedere e modificare `changeThemeLabel`. Gli screenshot seguenti mostrano che il testo visualizzato dal `Label` è stato modificato:

[![Screenshot dell'oggetto pagina basata su modelli, in iOS e Android](control-template-images/get-named-element.png "ContentPage basato su modelli")](control-template-images/get-named-element-large.png#lightbox "ContentPage basato su modelli")

## <a name="bind-to-a-viewmodel"></a>Associa a un ViewModel

Un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) può eseguire l'associazione dati a un ViewModel, anche quando il `ControlTemplate` viene associato all'elemento padre basato su modelli (l'istanza dell'oggetto di runtime a cui è applicato il modello).

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

In questo esempio, il `BindingContext` della pagina è impostato su un'istanza di `PeopleViewModel`. Questo ViewModel espone una raccolta di `People` e una `ICommand` denominata `DeletePersonCommand`. Il [`StackLayout`](xref:Xamarin.Forms.StackLayout) nella pagina usa un layout associabile ai dati associati alla raccolta `People` e il `ItemTemplate` del layout associabile viene impostato sulla risorsa `PersonTemplate`. Questa [`DataTemplate`](xref:Xamarin.Forms.DataTemplate) specifica che ogni elemento nella raccolta `People` verrà visualizzato utilizzando un oggetto `CardView`. La struttura visiva dell'oggetto `CardView` viene definita utilizzando una [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) denominata `CardViewControlTemplate`:

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

In questo esempio, l'elemento radice del [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) è un oggetto [`Frame`](xref:Xamarin.Forms.Frame) . L'oggetto `Frame` usa l'estensione di markup `RelativeSource` per impostare la relativa `BindingContext` sull'elemento padre basato su modelli. Le espressioni di associazione dell'oggetto `Frame` e dei relativi elementi figlio si risolvono rispetto a `CardView` proprietà, a causa dell'eredità del `BindingContext` dall'elemento radice `Frame`. Gli screenshot seguenti mostrano la pagina che visualizza la raccolta di `People`, che è costituita da tre elementi:

[![Screenshot degli oggetti CardView basati su modelli, in iOS e Android](control-template-images/viewmodel-controltemplate.png "Oggetti CardView basati su modelli")](control-template-images/viewmodel-controltemplate-large.png#lightbox "Oggetti CardView basati su modelli")

Mentre gli oggetti nella [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) vengono associati alle proprietà nel relativo elemento padre basato su modelli, il [`Button`](xref:Xamarin.Forms.Button) all'interno del modello di controllo è associato sia al relativo elemento padre basato su modelli sia al `DeletePersonCommand` nel ViewModel. Ciò è dovuto al fatto che la proprietà `Button.Command` ridefinisce l'origine del binding in modo che sia il contesto di associazione del predecessore il cui tipo di contesto di associazione è `PeopleViewModel`, ovvero il [`StackLayout`](xref:Xamarin.Forms.StackLayout). La `Path` parte delle espressioni di associazione può quindi risolvere la proprietà `DeletePersonCommand`. Tuttavia, la proprietà `Button.CommandParameter` non modifica l'origine del binding, ma la eredita dal relativo elemento padre nell' [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate). Pertanto, la proprietà `CommandParameter` viene associata alla proprietà `CardTitle` dell'`CardView`.

L'effetto complessivo delle associazioni [`Button`](xref:Xamarin.Forms.Button) è che, quando viene toccato il `Button`, viene eseguito il `DeletePersonCommand` della classe `PeopleViewModel` con il valore della proprietà `CardName` passato al `DeletePersonCommand`. Ciò comporta la rimozione del `CardView` specificato dal layout associabile:

[![Screenshot degli oggetti CardView basati su modelli, in iOS e Android](control-template-images/viewmodel-itemdeleted.png "Oggetti CardView basati su modelli")](control-template-images/viewmodel-itemdeleted-large.png#lightbox "Oggetti CardView basati su modelli")

Per ulteriori informazioni sulle associazioni relative, vedere [binding relativi di Novell. Forms](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md).

## <a name="related-links"></a>Collegamenti correlati

- [ControlTemplateDemos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/templates-controltemplatedemo)
- [Novell. Forms ContentView](~/xamarin-forms/user-interface/layouts/contentview.md)
- [Binding relativi di Novell. Forms](~/xamarin-forms/app-fundamentals/data-binding/relative-bindings.md)
- [Dizionari risorse Novell. Forms](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Data Binding Novell. Forms](~/xamarin-forms/app-fundamentals/data-binding/index.md)
- [Stili di Novell. Forms](~/xamarin-forms/user-interface/styles/index.md)
