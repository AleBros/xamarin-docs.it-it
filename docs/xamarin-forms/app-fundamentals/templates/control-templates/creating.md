---
title: Creazione di un ControlTemplate
description: È possibile definire i modelli del controllo a livello di applicazione o a livello di pagina. Questo articolo illustra come creare e utilizzare i modelli del controllo.
ms.prod: xamarin
ms.assetid: A9AEB052-FBF5-4589-9BD4-6D6F62BED7F1
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/08/2016
ms.openlocfilehash: 86e10f068af14e65b55885488252af756a90652e
ms.sourcegitcommit: b23a107b0fe3d2f814ae35b52a5855b6ce2a3513
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2019
ms.locfileid: "65926969"
---
# <a name="creating-a-controltemplate"></a>Creazione di un ControlTemplate

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/Templates/ControlTemplates/SimpleTheme/)

_È possibile definire i modelli del controllo a livello di applicazione o a livello di pagina. Questo articolo illustra come creare e usare i modelli del controllo._

## <a name="creating-a-controltemplate-in-xaml"></a>Creazione di un ControlTemplate in XAML

Per definire un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) a livello di applicazione, è necessario aggiungere un elemento [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) alla classe `App`. Per impostazione predefinita, tutte le applicazioni Xamarin.Forms create da un modello utilizzano la classe **App** per implementare la sottoclasse [`Application`](xref:Xamarin.Forms.Application). Per dichiarare un `ControlTemplate` a livello di applicazione, nell'elemento `ResourceDictionary` dell'applicazione è necessario sostituire, usando XAML, la classe **App** predefinita con una classe **App** XAML e il code-behind associato, come illustrato nell'esempio di codice seguente:

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

Ciascuna istanza di [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) viene creata in un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) come oggetto riutilizzabile.  Tale risultato si ottiene assegnando a ogni dichiarazione un valore univoco dell'attributo `x:Key`, che fornisce una chiave descrittiva nell'elemento `ResourceDictionary`.

L'esempio di codice seguente illustra il code-behind `App` associato:

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

Oltre a impostare la proprietà [`MainPage`](xref:Xamarin.Forms.Application.MainPage), il code-behind deve anche chiamare il metodo `InitializeComponent` per caricare e analizzare il codice XAML associato.

L'esempio di codice seguente mostra un elemento [`ContentPage`](xref:Xamarin.Forms.ContentPage) che applica `TealTemplate` a [`ContentView`](xref:Xamarin.Forms.ContentView):

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

L'elemento `TealTemplate` viene assegnato alla proprietà [`ContentView.ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate) usando l'estensione di markup `StaticResource`. La proprietà [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) è impostata su un valore [`StackLayout`](xref:Xamarin.Forms.StackLayout) che definisce il contenuto da visualizzare in [`ContentPage`](xref:Xamarin.Forms.ContentPage). Questo contenuto verrà visualizzato dall'elemento [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) contenuto nel `TealTemplate`. Il risultato è l'aspetto illustrato negli screenshot seguenti:

![](creating-images/teal-theme.png "Modello Teal Control")

### <a name="re-theming-an-application-at-runtime"></a>Modifica del tema di un'applicazione in fase di esecuzione

Quando si fa clic sul pulsante **Cambia tema** viene eseguito il metodo `OnButtonClicked`, come illustrato nell'esempio di codice seguente:

```csharp
void OnButtonClicked (object sender, EventArgs e)
{
  originalTemplate = !originalTemplate;
  contentView.ControlTemplate = (originalTemplate) ? tealTemplate : aquaTemplate;
}
```

Questo metodo sostituisce l'istanza di [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) attiva con l'istanza di `ControlTemplate` alternativa. Il risultato è illustrato nello screenshot seguente:

![](creating-images/aqua-theme.png "Modello Aqua Control")

> [!NOTE]
> Nelle `ContentPage`, può essere assegnata la proprietà `Content` e impostata la proprietà `ControlTemplate`. Quando ciò si verifica, se il `ControlTemplate` contiene un'istanza di `ContentPresenter`, il contenuto assegnato alla proprietà `Content` verrà presentato dall'elemento `ContentPresenter` all'interno del `ControlTemplate`.

### <a name="setting-a-controltemplate-with-a-style"></a>Impostazione di un ControlTemplate con uno stile

È possibile anche applicare un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) tramite un elemento [`Style`](xref:Xamarin.Forms.Style) per espandere ulteriormente la capacità del tema. A questo scopo, creare uno stile *implicito* o *esplicito* per la vista di destinazione in un [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) e impostare la proprietà `ControlTemplate` della vista di destinazione nell'istanza di [`Style`](xref:Xamarin.Forms.Style). L'esempio di codice seguente illustra uno stile *implicito* aggiunto a [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) a livello di applicazione:

```xaml
<Style TargetType="ContentView">
    <Setter Property="ControlTemplate" Value="{StaticResource TealTemplate}" />
</Style>
```

Poiché è *implicita*, l'istanza di [`Style`](xref:Xamarin.Forms.Style) verrà applicata a tutte le istanze `ContentView` dell'applicazione. Pertanto, non è più necessario impostare la proprietà [`ContentView.ControlTemplate`](xref:Xamarin.Forms.TemplatedView.ControlTemplate), come illustrato nell'esempio di codice seguente:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms" xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml" x:Class="SimpleTheme.HomePage">
    <ContentView x:Name="contentView" Padding="0,20,0,0">
      ...
    </ContentView>
</ContentPage>
```

Per altre informazioni sugli stili, vedere [Stili](~/xamarin-forms/user-interface/styles/index.md).

### <a name="creating-a-controltemplate-at-page-level"></a>Creazione di un ControlTemplate a livello di pagina

Oltre alla creazione di istanze di [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) a livello di applicazione, è possibile creare istanze anche a livello di pagina, come illustrato nell'esempio di codice seguente:

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

Quando si aggiunge un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) a livello di pagina, un elemento [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) viene aggiunto a [`ContentPage`](xref:Xamarin.Forms.ContentPage), quindi le istanze di `ControlTemplate` sono incluse in `ResourceDictionary`.

## <a name="creating-a-controltemplate-in-c35"></a>Creazione di un ControlTemplate in C&#35;

Per definire un [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) a livello di applicazione, è necessario creare un elemento `class` che rappresenti il `ControlTemplate`. La classe deve derivare dal [layout](~/xamarin-forms/user-interface/layouts/index.md) in uso per il modello, come illustrato nell'esempio di codice seguente:

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

La classe `AquaTemplate` è identica alla classe `TealTemplate`, ad eccezione del fatto che per le proprietà [`BoxView.Color`](xref:Xamarin.Forms.BoxView.Color) e [`Label.TextColor`](xref:Xamarin.Forms.Label.TextColor) vengono usati colori diversi.

L'esempio di codice seguente mostra un elemento [`ContentPage`](xref:Xamarin.Forms.ContentPage) che applica `TealTemplate` a [`ContentView`](xref:Xamarin.Forms.ContentView):

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

Le istanze [`ControlTemplate`](xref:Xamarin.Forms.ControlTemplate) vengono create specificando il tipo delle classi che definiscono i modelli di controllo, nel costruttore `ControlTemplate`.

La proprietà [`ContentView.Content`](xref:Xamarin.Forms.ContentView.Content) è impostata su un valore [`StackLayout`](xref:Xamarin.Forms.StackLayout) che definisce il contenuto da visualizzare in [`ContentPage`](xref:Xamarin.Forms.ContentPage). Questo contenuto verrà visualizzato dall'elemento [`ContentPresenter`](xref:Xamarin.Forms.ContentPresenter) contenuto nel `TealTemplate`. Lo stesso meccanismo descritto in precedenza viene usato per modificare il tema in fase di esecuzione impostando `AquaTheme`.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come creare e utilizzare i modelli del controllo. È possibile definire i modelli del controllo a livello di applicazione o a livello di pagina.


## <a name="related-links"></a>Collegamenti correlati

- [Stili](~/xamarin-forms/user-interface/styles/index.md)
- [Simple Theme (esempio)](https://developer.xamarin.com/samples/xamarin-forms/Templates/ControlTemplates/SimpleTheme/) (Tema semplice)
- [ControlTemplate](xref:Xamarin.Forms.ControlTemplate)
- [ContentPresenter](xref:Xamarin.Forms.ContentPresenter)
- [ContentView](xref:Xamarin.Forms.ContentView)
- [ResourceDictionary](xref:Xamarin.Forms.ResourceDictionary)
