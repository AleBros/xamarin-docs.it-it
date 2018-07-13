---
title: Creazione di un ControlTemplate
description: Modelli di controllo possono essere definiti a livello di applicazione o a livello di pagina. Questo articolo illustra come creare e utilizzare modelli di controllo.
ms.prod: xamarin
ms.assetid: A9AEB052-FBF5-4589-9BD4-6D6F62BED7F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: b83668f6836b1d5d98f67592bf3e2b01e7319edc
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998189"
---
# <a name="creating-a-controltemplate"></a>Creazione di un ControlTemplate

_Modelli di controllo possono essere definiti a livello di applicazione o a livello di pagina. Questo articolo illustra come creare e utilizzare modelli di controllo._

## <a name="creating-a-controltemplate-in-xaml"></a>Creazione di un oggetto ControlTemplate in XAML

Per definire un [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) a livello di applicazione, una [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) deve essere aggiunto al `App` classe. Per impostazione predefinita, tutte le applicazioni xamarin. Forms create da un modello utilizzano le **App** classe per implementare le [ `Application` ](xref:Xamarin.Forms.Application) sottoclasse. Per dichiarare un `ControlTemplate` a livello di applicazione, dell'applicazione `ResourceDictionary` tramite XAML, il valore predefinito **App** classe deve essere sostituita con un XAML **App** classe e code-behind associato, come illustrato nell'esempio di codice seguente:

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

Ciascuna [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) istanza viene creata come un oggetto riutilizzabile in un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary).  Questo risultato viene ottenuto, assegnando un valore univoco a ogni dichiarazione `x:Key` attributo, che fornisce una chiave descrittiva nel `ResourceDictionary`.

Nell'esempio di codice seguente viene associato `App` code-behind:

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

Nonché impostazione di [ `MainPage` ](xref:Xamarin.Forms.Application.MainPage) proprietà, il code-behind deve anche chiamare il `InitializeComponent` metodo per caricare e analizzare il XAML associato.

Nell'esempio di codice riportato di seguito viene illustrato un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) applicando il `TealTemplate` per il [ `ContentView` ](xref:Xamarin.Forms.ContentView):

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

Il `TealTemplate` viene assegnato al [ `ContentView.ControlTemplate` ](xref:Xamarin.Forms.TemplatedView.ControlTemplate) usando il `StaticResource` estensione di markup. Il [ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content) è impostata su un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) che definisce il contenuto da visualizzare sul [ `ContentPage` ](xref:Xamarin.Forms.ContentPage). Questo contenuto verrà visualizzato per il [ `ContentPresenter` ](xref:Xamarin.Forms.ContentPresenter) contenuti nel `TealTemplate`. Ciò comporta l'aspetto illustrato negli screenshot seguenti:

![](creating-images/teal-theme.png "Modello di controllo verde acqua")

### <a name="re-theming-an-application-at-runtime"></a>Re-temi un'applicazione in fase di esecuzione

Facendo clic sui **modificare il tema** pulsante esegue la `OnButtonClicked` metodo, che viene visualizzato nell'esempio di codice seguente:

```csharp
void OnButtonClicked (object sender, EventArgs e)
{
  originalTemplate = !originalTemplate;
  contentView.ControlTemplate = (originalTemplate) ? tealTemplate : aquaTemplate;
}
```

Questo metodo sostituisce attivo [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) istanza con l'alternativa `ControlTemplate` istanza risultante nello screenshot seguente:

![](creating-images/aqua-theme.png "Modello di controllo azzurro")

> [!NOTE]
> In un `ContentPage`, il `Content` proprietà può essere assegnata e il `ControlTemplate` proprietà può anche essere impostata. Quando ciò si verifica, se il `ControlTemplate` contiene un `ContentPresenter` dell'istanza, il contenuto assegnato al `Content` proprietà proporrà il `ContentPresenter` all'interno del `ControlTemplate`.

### <a name="setting-a-controltemplate-with-a-style"></a>L'impostazione di un oggetto ControlTemplate con uno stile

Oggetto [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) può essere applicata anche tramite un [ `Style` ](xref:Xamarin.Forms.Style) per espandere un'ulteriore possibilità di tema. Ciò può essere ottenuta tramite la creazione di un' *implicita* o *esplicita* stile per la vista di destinazione in un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary)e impostando il `ControlTemplate` proprietà della destinazione visualizzare il [ `Style` ](xref:Xamarin.Forms.Style) istanza. Il codice seguente esempio illustra un' *implicita* stile di visualizzazione che è stato aggiunto a livello di applicazione [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary):

```xaml
<Style TargetType="ContentView">
    <Setter Property="ControlTemplate" Value="{StaticResource TealTemplate}" />
</Style>
```

Poiché il [ `Style` ](xref:Xamarin.Forms.Style) istanza viene *implicita*, verrà applicata a tutti `ContentView` istanze dell'applicazione. Pertanto, non è più necessario impostare il [ `ContentView.ControlTemplate` ](xref:Xamarin.Forms.TemplatedView.ControlTemplate) proprietà, come illustrato nell'esempio di codice seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentView x:Name="contentView" Padding="0,20,0,0">
      ...
    </ContentView>
</ContentPage>
```

Per altre informazioni sugli stili, vedere [stili](~/xamarin-forms/user-interface/styles/index.md).

### <a name="creating-a-controltemplate-at-page-level"></a>Creazione di un oggetto ControlTemplate a livello di pagina

Oltre alla creazione [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) istanze a livello di applicazione, possono anche essere creati a livello di pagina, come illustrato nell'esempio di codice seguente:

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

Quando si aggiunge un [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) a livello di pagina, un [ `ResourceDictionary` ](xref:Xamarin.Forms.ResourceDictionary) viene aggiunto al [ `ContentPage` ](xref:Xamarin.Forms.ContentPage)e quindi il `ControlTemplate` sono incluse le istanze nel `ResourceDictionary`.

## <a name="creating-a-controltemplate-in-c35"></a>Creazione di un oggetto ControlTemplate in C&#35;

Per definire un [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) a livello di applicazione, una `class` deve essere creato che rappresenta il `ControlTemplate`. La classe deve derivare dal [layout](~/xamarin-forms/user-interface/layouts/index.md) in uso per il modello, come illustrato nell'esempio di codice seguente:

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

Il `AquaTemplate` è identica alla classe il `TealTemplate` classe, ad eccezione del fatto che vengono usati colori diversi per il [ `BoxView.Color` ](xref:Xamarin.Forms.BoxView.Color) e [ `Label.TextColor` ](xref:Xamarin.Forms.Label.TextColor) proprietà.

Nell'esempio di codice riportato di seguito viene illustrato un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) applicando il `TealTemplate` per il [ `ContentView` ](xref:Xamarin.Forms.ContentView):

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

Il [ `ControlTemplate` ](xref:Xamarin.Forms.ControlTemplate) istanze vengono create specificando il tipo delle classi che definiscono i modelli di controllo, il `ControlTemplate` costruttore.

Il [ `ContentView.Content` ](xref:Xamarin.Forms.ContentView.Content) è impostata su un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) che definisce il contenuto da visualizzare sul [ `ContentPage` ](xref:Xamarin.Forms.ContentPage). Questo contenuto verrà visualizzato per il [ `ContentPresenter` ](xref:Xamarin.Forms.ContentPresenter) contenuti nel `TealTemplate`. Lo stesso meccanismo descritto in precedenza viene usato per modificare il tema in fase di esecuzione per il `AquaTheme`.

## <a name="summary"></a>Riepilogo

Questo articolo è stato illustrato come creare e utilizzare modelli di controllo. Modelli di controllo possono essere definiti a livello di applicazione o a livello di pagina.


## <a name="related-links"></a>Collegamenti correlati

- [Stili](~/xamarin-forms/user-interface/styles/index.md)
- [Tema semplice (esempio)](https://developer.xamarin.com/samples/xamarin-forms/templates/controltemplates/simpletheme/)
- [Oggetto ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentPresenter](xref:Xamarin.Forms.ContentPresenter)
- [ContentView](xref:Xamarin.Forms.ContentView)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
