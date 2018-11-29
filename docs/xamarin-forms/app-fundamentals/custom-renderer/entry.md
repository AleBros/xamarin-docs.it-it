---
title: Personalizzazione di una voce
description: Il controllo voce di xamarin. Forms consente una singola riga di testo che deve essere modificato. Questo articolo illustra come creare un renderer personalizzato per il controllo voce, consentendo agli sviluppatori di eseguire l'override del rendering nativo predefinito con le personalizzazioni specifiche della piattaforma.
ms.prod: xamarin
ms.assetid: 7B5DD10D-0411-424F-88D8-8A474DF16D8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/26/2018
ms.openlocfilehash: 7fea736b0a04a69fd64100ae1d6bcd42c244359f
ms.sourcegitcommit: 2f6a5c1abf90fbdb0475fd8a3ce6de3cd7c7d575
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52459850"
---
# <a name="customizing-an-entry"></a>Personalizzazione di una voce

_Il controllo voce di xamarin. Forms consente una singola riga di testo che deve essere modificato. Questo articolo illustra come creare un renderer personalizzato per il controllo voce, consentendo agli sviluppatori di eseguire l'override del rendering nativo predefinito con le personalizzazioni specifiche della piattaforma._

Ogni controllo di xamarin. Forms dispone di un renderer di accompagnamento per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Quando un [ `Entry` ](xref:Xamarin.Forms.Entry) rendering del controllo da un'applicazione xamarin. Forms, in iOS il `EntryRenderer` viene creata un'istanza di classe, che a sua volta crea un'istanza nativa `UITextField` controllo. La piattaforma Android, il `EntryRenderer` crea un'istanza di classe un `EditText` controllo. In Universal Windows Platform (UWP), il `EntryRenderer` crea un'istanza di classe un `TextBox` controllo. Per altre informazioni sulle classi di controllo nativo che eseguono il mapping per i controlli di xamarin. Forms e renderer, vedere [Renderer di classi di Base e controlli nativi](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Il diagramma seguente illustra la relazione tra il [ `Entry` ](xref:Xamarin.Forms.Entry) controllo e i corrispondenti controlli nativi che l'implementano:

![](entry-images/entry-classes.png "Relazione tra il controllo e implementare i controlli nativi")

Il processo di rendering possibile avvantaggiarsi di implementare le personalizzazioni specifiche della piattaforma tramite la creazione di un renderer personalizzato per il [ `Entry` ](xref:Xamarin.Forms.Entry) controllo in ogni piattaforma. Il processo per eseguire questa operazione è come segue:

1. [Creare](#Creating_the_Custom_Entry_Control) un controllo personalizzato di xamarin. Forms.
1. [Utilizzare](#Consuming_the_Custom_Control) il controllo personalizzato da xamarin. Forms.
1. [Creare](#Creating_the_Custom_Renderer_on_each_Platform) il renderer personalizzato per il controllo in ogni piattaforma.

Ogni elemento a questo punto discuteremo a sua volta, per implementare un' [ `Entry` ](xref:Xamarin.Forms.Entry) controllo dotato di un colore di sfondo diverso in ogni piattaforma.

> [!IMPORTANT]
> Questo articolo illustra come creare un renderer personalizzato semplice. Non è tuttavia necessario creare un renderer personalizzato per implementare un `Entry` che ha un colore di sfondo diverso in ogni piattaforma. Ciò può essere ottenuta più facilmente usando le [ `Device` ](xref:Xamarin.Forms.Device) (classe), o `OnPlatform` estensione di markup, per fornire i valori specifici della piattaforma. Per altre informazioni, vedere [fornendo valori specifici della piattaforma](~/xamarin-forms/platform/device.md#providing-platform-specific-values) e [estensione di Markup OnPlatform](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension).

<a name="Creating_the_Custom_Entry_Control" />

## <a name="creating-the-custom-entry-control"></a>Creazione di un controllo voce personalizzata

Una classe personalizzata [ `Entry` ](xref:Xamarin.Forms.Entry) controllo può essere creato dalla creazione di una sottoclasse di `Entry` controllare, come illustrato nell'esempio di codice seguente:

```csharp
public class MyEntry : Entry
{
}
```

Il `MyEntry` controllo viene creato nel progetto della libreria .NET Standard e viene semplicemente un' [ `Entry` ](xref:Xamarin.Forms.Entry) controllo. Personalizzazione del controllo verrà eseguita nel renderer personalizzati, pertanto non è necessario alcuna implementazione aggiuntiva di `MyEntry` controllo.

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>Utilizzo del controllo personalizzato

Il `MyEntry` controllo è reperibile in XAML nel progetto della libreria .NET Standard dichiara uno spazio dei nomi per il percorso e usando il prefisso dello spazio dei nomi dell'elemento di controllo. Nell'esempio di codice riportato di seguito viene illustrato come il `MyEntry` controllo può essere usato da una pagina XAML:

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <local:MyEntry Text="In Shared Code" />
    ...
</ContentPage>
```

Il `local` prefisso dello spazio dei nomi può essere denominata alcuna operazione. Tuttavia, il `clr-namespace` e `assembly` valori devono corrispondere i dettagli del controllo personalizzato. Una volta che lo spazio dei nomi è dichiarato che il prefisso viene usato per fare riferimento al controllo personalizzato.

Nell'esempio di codice riportato di seguito viene illustrato come il `MyEntry` controllo può essere usato da una pagina di c#:

```csharp
public class MainPage : ContentPage
{
  public MainPage ()
  {
    Content = new StackLayout {
      Children = {
        new Label {
          Text = "Hello, Custom Renderer !",
        },
        new MyEntry {
          Text = "In Shared Code",
        }
      },
      VerticalOptions = LayoutOptions.CenterAndExpand,
      HorizontalOptions = LayoutOptions.CenterAndExpand,
    };
  }
}
```

Questo codice crea un'istanza di una nuova [ `ContentPage` ](xref:Xamarin.Forms.ContentPage) oggetto che verrà visualizzato un [ `Label` ](xref:Xamarin.Forms.Label) e `MyEntry` controllo centrato sia verticalmente che orizzontalmente nella pagina.

Un renderer personalizzato può ora essere aggiunti a ogni progetto di applicazione per personalizzare l'aspetto del controllo in ogni piattaforma.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Creare il Renderer personalizzato a seconda della piattaforma

Il processo per la creazione della classe renderer personalizzati è come segue:

1. Creare una sottoclasse del `EntryRenderer` classe che esegue il rendering del controllo nativo.
1. Eseguire l'override di `OnElementChanged` metodo che esegue il rendering la logica di controllo e di scrittura nativa per personalizzare il controllo. Questo metodo viene chiamato quando viene creato il controllo di xamarin. Forms corrispondente.
1. Aggiungere un `ExportRenderer` attributo alla classe renderer personalizzato per specificare che verrà usata per il rendering del controllo di xamarin. Forms. Questo attributo viene utilizzato per registrare il renderer personalizzato con xamarin. Forms.

> [!NOTE]
> È facoltativo per fornire un renderer personalizzato in ogni progetto della piattaforma. Se non è registrato un renderer personalizzato, quindi verrà utilizzato il renderer predefinito per la classe di base del controllo.

Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, con le relazioni tra di essi:

![](entry-images/solution-structure.png "Responsabilità di progetto Renderer personalizzati MyEntry")

Il `MyEntry` rendering del controllo da specifiche della piattaforma `MyEntryRenderer` classi che derivano dal `EntryRenderer` classe per ogni piattaforma. Di conseguenza ogni `MyEntry` controllo viene eseguito il rendering con un colore di sfondo specifico della piattaforma, come illustrato negli screenshot seguenti:

![](entry-images/screenshots.png "Controllo MyEntry in ogni piattaforma")

Il `EntryRenderer` classe espone il `OnElementChanged` metodo, che viene chiamato quando viene creato il controllo di xamarin. Forms per eseguire il rendering del controllo nativo corrispondente. Questo metodo accetta un `ElementChangedEventArgs` parametro che contiene `OldElement` e `NewElement` proprietà. Queste proprietà rappresentano l'elemento di xamarin. Forms che il renderer *è stata* collegata, l'elemento di xamarin. Forms e che il renderer *è* collegati rispettivamente. Nell'applicazione di esempio il `OldElement` proprietà sarà `null` e il `NewElement` proprietà conterrà un riferimento al `MyEntry` controllo.

Una versione override del `OnElementChanged` nel metodo il `MyEntryRenderer` classe è la posizione in cui eseguire la personalizzazione del controllo nativo. Un riferimento tipizzato al controllo nativo utilizzato nella piattaforma sono accessibili tramite il `Control` proprietà. Inoltre, un riferimento al controllo di xamarin. Forms che viene eseguito il rendering può essere ottenuto tramite il `Element` proprietà, anche se non viene usato nell'applicazione di esempio.

Ogni classe renderer personalizzato è decorata con un `ExportRenderer` attributo che registra il renderer con xamarin. Forms. L'attributo accetta due parametri: il nome del tipo di controllo di xamarin. Forms viene eseguito il rendering e il nome del tipo di renderer personalizzato. Il `assembly` prefisso per l'attributo specifica che l'attributo viene applicato all'intero assembly.

Le sezioni seguenti illustrano l'implementazione di ogni specifico della piattaforma `MyEntryRenderer` classe renderer personalizzato.

### <a name="creating-the-custom-renderer-on-ios"></a>Creare il Renderer personalizzati in iOS

Esempio di codice seguente illustra il renderer personalizzato per la piattaforma iOS:

```csharp
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer (typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.iOS
{
    public class MyEntryRenderer : EntryRenderer
    {
        protected override void OnElementChanged (ElementChangedEventArgs<Entry> e)
        {
            base.OnElementChanged (e);

            if (Control != null) {
                // do whatever you want to the UITextField here!
                Control.BackgroundColor = UIColor.FromRGB (204, 153, 255);
                Control.BorderStyle = UITextBorderStyle.Line;
            }
        }
    }
}
```

La chiamata per la classe di base `OnElementChanged` metodo crea un'istanza di un iOS `UITextField` controllo, con un riferimento al controllo viene assegnato per il renderer `Control` proprietà. Il colore di sfondo viene quindi impostato su light viola con la `UIColor.FromRGB` (metodo).

### <a name="creating-the-custom-renderer-on-android"></a>Creare il Renderer personalizzati in Android

Esempio di codice seguente illustra il renderer personalizzato per la piattaforma Android:

```csharp
using Xamarin.Forms.Platform.Android;

[assembly: ExportRenderer(typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.Android
{
    class MyEntryRenderer : EntryRenderer
    {
        public MyEntryRenderer(Context context) : base(context)
        {
        }

        protected override void OnElementChanged(ElementChangedEventArgs<Entry> e)
        {
            base.OnElementChanged(e);

            if (Control != null)
            {
                Control.SetBackgroundColor(global::Android.Graphics.Color.LightGreen);
            }
        }
    }
}
```

La chiamata per la classe di base `OnElementChanged` metodo crea un'istanza di Android `EditText` controllo, con un riferimento al controllo viene assegnato per il renderer `Control` proprietà. Il colore di sfondo viene quindi impostato su verde chiaro con la `Control.SetBackgroundColor` (metodo).

### <a name="creating-the-custom-renderer-on-uwp"></a>Creare il Renderer personalizzato nella piattaforma UWP

Esempio di codice seguente illustra il renderer personalizzato per la piattaforma UWP:

```csharp
[assembly: ExportRenderer(typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.UWP
{
    public class MyEntryRenderer : EntryRenderer
    {
        protected override void OnElementChanged(ElementChangedEventArgs<Entry> e)
        {
            base.OnElementChanged(e);

            if (Control != null)
            {
                Control.Background = new SolidColorBrush(Colors.Cyan);
            }
        }
    }
}
```

La chiamata per la classe di base `OnElementChanged` metodo crea un'istanza di un `TextBox` controllo, con un riferimento al controllo viene assegnato per il renderer `Control` proprietà. Il colore di sfondo viene quindi impostato su ciano creando un `SolidColorBrush` istanza.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come creare un renderer del controllo personalizzato per xamarin. Forms [ `Entry` ](xref:Xamarin.Forms.Entry) (controllo), consentendo agli sviluppatori di eseguire l'override del rendering nativo predefinito con il proprio rendering specifico della piattaforma. Renderer personalizzati offrono un approccio potente per la personalizzazione dell'aspetto dei controlli di xamarin. Forms. Possono essere utilizzati per lo stile piccole modifiche o layout specifici della piattaforma sofisticati e personalizzazione del comportamento.


## <a name="related-links"></a>Collegamenti correlati

- [CustomRendererEntry (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/entry/)
