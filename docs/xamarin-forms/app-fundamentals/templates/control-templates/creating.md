---
title: Creazione di un ControlTemplate
description: Modelli di controllo possono essere definiti a livello di applicazione o a livello di pagina. In questo articolo viene illustrato come creare e utilizzare modelli di controllo.
ms.prod: xamarin
ms.assetid: A9AEB052-FBF5-4589-9BD4-6D6F62BED7F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 214f967ccc07a7c952ba87927c34c3ab32623391
ms.sourcegitcommit: d80d93957040a14b4638a91b0eac797cfaade840
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/07/2018
ms.locfileid: "34848278"
---
# <a name="creating-a-controltemplate"></a>Creazione di un ControlTemplate

_Modelli di controllo possono essere definiti a livello di applicazione o a livello di pagina. In questo articolo viene illustrato come creare e utilizzare modelli di controllo._

## <a name="creating-a-controltemplate-in-xaml"></a>Creazione di ControlTemplate in XAML

Per definire un [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) a livello di applicazione, un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) deve essere aggiunto alla `App` classe. Per impostazione predefinita, tutte le applicazioni di xamarin. Forms create da un modello utilizzano il **App** classe per implementare il [ `Application` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Application/) sottoclasse. Per dichiarare un `ControlTemplate` a livello di applicazione, l'applicazione `ResourceDictionary` tramite XAML, il valore predefinito **App** classe deve essere sostituita con un file XAML **App** classe e code-behind associato come Nell'esempio di codice seguente:

```xaml
<Application xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.App">
    <Application.Resources>
        <ResourceDictionary>
            <ControlTemplate x:Key="TealTemplate">
                <Grid>
                    ...
                    <BoxView ... />
                    <Label Text="Control Template Demo App"
                           TextColor="White"
                           VerticalOptions="Center" ... />
                    <ContentPresenter ... />
                    <BoxView Color="Teal" ... />
                    <Label Text="(c) Xamarin 2016"
                           TextColor="White"
                           VerticalOptions="Center" ... />
                </Grid>
            </ControlTemplate>
            <ControlTemplate x:Key="AquaTemplate">
                ...
            </ControlTemplate>
        </ResourceDictionary>
    </Application.Resources>
</Application>
```

Ogni [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) istanza viene creata come un oggetto riutilizzabile in un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/).  Infatti, assegnando a ogni dichiarazione univoca `x:Key` attributo, che fornisce una chiave descrittiva di `ResourceDictionary`.

Nell'esempio di codice seguente viene associato `App` codice:

```csharp
public partial class App : Application
{
  public App ()
  {
    InitializeComponent ();
    MainPage = new HomePage ();
  }
}
```

Nonché l'impostazione di [ `MainPage` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Application.MainPage/) proprietà, il code-behind deve chiamare anche il `InitializeComponent` metodo per caricare e analizzare il codice XAML associato.

Nell'esempio di codice riportato di seguito viene illustrato un [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) l'applicazione di `TealTemplate` per il [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/):

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentView x:Name="contentView" Padding="0,20,0,0"
                 ControlTemplate="{StaticResource TealTemplate}">
        <StackLayout VerticalOptions="CenterAndExpand">
            <Label Text="Welcome to the app!" HorizontalOptions="Center" />
            <Button Text="Change Theme" Clicked="OnButtonClicked" />
        </StackLayout>
    </ContentView>
</ContentPage>
```

Il `TealTemplate` viene assegnato al [ `ContentView.ControlTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TemplatedView.ControlTemplate/) proprietà utilizzando il `StaticResource` estensione di markup. Il [ `ContentView.Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/) è impostata su un [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) che definisce il contenuto da visualizzare sul [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/). Verrà visualizzato da questo contenuto di [ `ContentPresenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/) contenuti nel `TealTemplate`. Ciò comporta l'aspetto illustrato nelle schermate seguenti:

![](creating-images/teal-theme.png "Modello di controllo verde acqua")

### <a name="re-theming-an-application-at-runtime"></a>Re-temi un'applicazione in fase di esecuzione

Fare clic su di **modificare il tema del** viene eseguita la `OnButtonClicked` (metodo), come illustrato nell'esempio di codice seguente:

```csharp
void OnButtonClicked (object sender, EventArgs e)
{
  originalTemplate = !originalTemplate;
  contentView.ControlTemplate = (originalTemplate) ? tealTemplate : aquaTemplate;
}
```

Questo metodo sostituisce attivo [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) istanza con l'alternativa `ControlTemplate` istanza risultante nella schermata seguente:

![](creating-images/aqua-theme.png "Modello di controllo azzurro")

> [!NOTE]
> In un `ContentPage`, `Content` proprietà può essere assegnata e il `ControlTemplate` proprietà può anche essere impostata. In tal caso, se il `ControlTemplate` contiene un `ContentPresenter` istanza, il contenuto assegnato al `Content` proprietà verrà presentata dal `ContentPresenter` all'interno di `ControlTemplate`.

### <a name="setting-a-controltemplate-with-a-style"></a>L'impostazione di ControlTemplate con uno stile

Oggetto [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) può essere applicata anche tramite un [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) per espandere ulteriormente il possibilità di tema. Questo può essere ottenuto tramite la creazione di un *implicita* o *esplicita* stile per la vista di destinazione in un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/), impostazione e il `ControlTemplate` proprietà della destinazione visualizzare il [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) istanza. Nell'esempio di codice riportato di seguito viene illustrato un *implicita* stile che è stato aggiunto al livello di applicazione [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/):

```xaml
<Style TargetType="ContentView">
    <Setter Property="ControlTemplate" Value="{StaticResource TealTemplate}" />
</Style>
```

Poiché il [ `Style` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Style/) istanza *implicita*, verrà applicata a tutti `ContentView` istanze dell'applicazione. Pertanto, non è più necessario impostare il [ `ContentView.ControlTemplate` ](https://developer.xamarin.com/api/property/Xamarin.Forms.TemplatedView.ControlTemplate/) proprietà, come illustrato nell'esempio di codice seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentView x:Name="contentView" Padding="0,20,0,0">
      ...
    </ContentView>
</ContentPage>
```

Per ulteriori informazioni sugli stili, vedere [stili](~/xamarin-forms/user-interface/styles/index.md).

### <a name="creating-a-controltemplate-at-page-level"></a>Creazione di ControlTemplate a livello di pagina

Oltre a creare [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) istanze a livello di applicazione, possono anche essere creati a livello di pagina, come illustrato nell'esempio di codice seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentPage.Resources>
        <ResourceDictionary>
            <ControlTemplate x:Key="TealTemplate">
                ...
            </ControlTemplate>
            <ControlTemplate x:Key="AquaTemplate">
                ...
            </ControlTemplate>
        </ResourceDictionary>
    </ContentPage.Resources>
    <ContentView ... ControlTemplate="{StaticResource TealTemplate}">
        ...
    </ContentView>
</ContentPage>
```

Quando si aggiunge un [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) a livello di pagina, un [ `ResourceDictionary` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/) viene aggiunto al [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/)e quindi la `ControlTemplate` sono incluse le istanze nel `ResourceDictionary`.

## <a name="creating-a-controltemplate-in-c35"></a>Creazione di ControlTemplate in C&#35;

Per definire un [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) a livello di applicazione, un `class` deve essere creato che rappresenta il `ControlTemplate`. La classe deve derivare dal [layout](~/xamarin-forms/user-interface/layouts/index.md) vengono utilizzate per il modello, come illustrato nell'esempio di codice seguente:

```csharp
class TealTemplate : Grid
{
  public TealTemplate ()
  {
    ...
    var contentPresenter = new ContentPresenter ();
    Children.Add (contentPresenter, 0, 1);
    Grid.SetColumnSpan (contentPresenter, 2);
    ...
  }
}

class AquaTemplate : Grid
{
  ...
}
```

Il `AquaTemplate` è identica alla classe il `TealTemplate` classe, ad eccezione del fatto che vengono utilizzati colori diversi per il [ `BoxView.Color` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BoxView.Color/) e [ `Label.TextColor` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Label.TextColor/) proprietà.

Nell'esempio di codice riportato di seguito viene illustrato un [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) l'applicazione di `TealTemplate` per il [ `ContentView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/):

```csharp
public class HomePageCS : ContentPage
{
  ...
  ControlTemplate tealTemplate = new ControlTemplate (typeof(TealTemplate));
  ControlTemplate aquaTemplate = new ControlTemplate (typeof(AquaTemplate));

  public HomePageCS ()
  {
    var button = new Button { Text = "Change Theme" };
    var contentView = new ContentView {
      Padding = new Thickness (0, 20, 0, 0),
      Content = new StackLayout {
        VerticalOptions = LayoutOptions.CenterAndExpand,
        Children = {
          new Label { Text = "Welcome to the app!", HorizontalOptions = LayoutOptions.Center },
          button
        }
      },
      ControlTemplate = tealTemplate
    };
    ...
    Content = contentView;
  }
}
```

Il [ `ControlTemplate` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/) che specifica il tipo delle classi che definiscono i modelli di controllo, vengono create istanze di `ControlTemplate` costruttore.

Il [ `ContentView.Content` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ContentView.Content/) è impostata su un [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) che definisce il contenuto da visualizzare sul [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/). Verrà visualizzato da questo contenuto di [ `ContentPresenter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/) contenuti nel `TealTemplate`. Lo stesso meccanismo descritto in precedenza viene utilizzato per modificare il tema in fase di esecuzione per il `AquaTheme`.

## <a name="summary"></a>Riepilogo

In questo articolo viene illustrato come creare e utilizzare modelli di controllo. Modelli di controllo possono essere definiti a livello di applicazione o a livello di pagina.


## <a name="related-links"></a>Collegamenti correlati

- [Stili](~/xamarin-forms/user-interface/styles/index.md)
- [Tema semplice (esempio)](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simpletheme/)
- [ControlTemplate](https://developer.xamarin.com/api/type/Xamarin.Forms.ControlTemplate/)
- [ContentPresenter](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPresenter/)
- [ContentView](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentView/)
- [ResourceDictionary](https://developer.xamarin.com/api/type/Xamarin.Forms.ResourceDictionary/)
