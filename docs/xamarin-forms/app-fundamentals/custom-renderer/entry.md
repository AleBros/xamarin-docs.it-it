---
title: Personalizzazione di una voce
description: Il controllo voce di xamarin. Forms consente una singola riga di testo da modificare. In questo articolo viene illustrato come creare un renderer personalizzato per il controllo voce, consentendo agli sviluppatori di eseguire l'override per il rendering predefinito nativo con le personalizzazioni specifiche della piattaforma.
ms.topic: article
ms.prod: xamarin
ms.assetid: 7B5DD10D-0411-424F-88D8-8A474DF16D8D
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 11/29/2017
ms.openlocfilehash: 0fe7aa2316abaae33da753e99a6f2eca4870d2d0
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="customizing-an-entry"></a>Personalizzazione di una voce

_Il controllo voce di xamarin. Forms consente una singola riga di testo da modificare. In questo articolo viene illustrato come creare un renderer personalizzato per il controllo voce, consentendo agli sviluppatori di eseguire l'override per il rendering predefinito nativo con le personalizzazioni specifiche della piattaforma._

Ogni controllo in xamarin. Forms è un renderer di accompagnamento per ogni piattaforma che consente di creare un'istanza di un controllo nativo. Quando un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) rendering del controllo da un'applicazione di xamarin. Forms, in iOS il `EntryRenderer` viene creata un'istanza di classe, che a sua volta crea un'istanza nativa `UITextField` controllo. Nella piattaforma Android, il `EntryRenderer` crea un'istanza di classe un `EditText` controllo. In Windows Phone e di Windows della piattaforma UWP (Universal), il `EntryRenderer` crea un'istanza di classe un `TextBox` controllo. Per ulteriori informazioni sulle classi di controllo nativo che eseguono il mapping per i controlli di xamarin. Forms e renderer, vedere [Renderer classi di Base e i controlli nativi](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Il diagramma seguente illustra la relazione tra il [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controllo e i corrispondenti controlli nativi che l'implementano:

![](entry-images/entry-classes.png "Relazione tra il controllo e l'implementazione di controlli nativi")

Il processo di rendering può essere sfruttato per implementare le personalizzazioni specifiche della piattaforma tramite la creazione di un renderer personalizzato per il [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controllo in ogni piattaforma. Il processo per eseguire questa operazione è come segue:

1. [Creare](#Creating_the_Custom_Entry_Control) un controllo personalizzato di xamarin. Forms.
1. [Utilizzare](#Consuming_the_Custom_Control) il controllo personalizzato da xamarin. Forms.
1. [Creare](#Creating_the_Custom_Renderer_on_each_Platform) il renderer personalizzato per il controllo in ogni piattaforma.

Ogni elemento verrà ora essere descritti in dettaglio, per implementare un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controllo con un colore di sfondo diversi per ogni piattaforma.

<a name="Creating_the_Custom_Entry_Control" />

## <a name="creating-the-custom-entry-control"></a>Creazione di un controllo personalizzato voce

Un oggetto personalizzato [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controllo può essere creato creando sottoclassi di `Entry` controllare, come illustrato nell'esempio di codice seguente:

```csharp
public class MyEntry : Entry
{
}
```

Il `MyEntry` controllo viene creato nel progetto di libreria (PCL) di classi portabile ed è semplicemente un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) controllo. Personalizzazione del controllo verrà effettuata nel renderer personalizzato, è necessario alcun implementazione aggiuntiva di `MyEntry` controllo.

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>Utilizzo del controllo personalizzato

Il `MyEntry` controllo può fare riferimento in XAML nel progetto libreria di classi Portabile dichiarare uno spazio dei nomi per la posizione e utilizzando il prefisso dello spazio dei nomi dell'elemento di controllo. Nell'esempio di codice riportato di seguito viene illustrato come la `MyEntry` controllo può essere utilizzato da una pagina XAML:

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <local:MyEntry Text="In Shared Code" />
    ...
</ContentPage>
```

Il `local` prefisso dello spazio dei nomi denominato nulla. Tuttavia, il `clr-namespace` e `assembly` i valori devono corrispondere i dettagli del controllo personalizzato. Una volta che lo spazio dei nomi è dichiarato che il prefisso viene utilizzato per fare riferimento al controllo personalizzato.

Nell'esempio di codice riportato di seguito viene illustrato come la `MyEntry` controllo può essere utilizzato da una pagina in c#:

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

Questo codice crea un nuovo [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/) oggetto che verrà visualizzato un [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) e `MyEntry` controllo centrato sia verticalmente e orizzontalmente nella pagina.

Un renderer personalizzato può ora essere aggiunto a ogni progetto di applicazione per personalizzare l'aspetto del controllo in ogni piattaforma.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Creazione di Renderer personalizzato a seconda della piattaforma

Il processo di creazione della classe renderer personalizzato è come segue:

1. Creare una sottoclasse di `EntryRenderer` classe che esegue il rendering del controllo nativo.
1. Eseguire l'override di `OnElementChanged` metodo che esegue il rendering la logica di controllo e di scrittura nativa per personalizzare il controllo. Questo metodo viene chiamato quando viene creato il controllo di xamarin. Forms corrispondente.
1. Aggiungere un `ExportRenderer` attributo alla classe renderer personalizzato per specificare che verrà usata per il rendering del controllo di xamarin. Forms. Questo attributo viene utilizzato per registrare il renderer personalizzato con xamarin. Forms.

> [!NOTE]
> È facoltativo per fornire un renderer personalizzato in ogni progetto della piattaforma. Se non è registrato un renderer personalizzato, verrà utilizzato il renderer predefinito per la classe di base del controllo.

Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, con le relazioni tra di essi:

![](entry-images/solution-structure.png "Responsabilità di progetto personalizzato MyEntry Renderer")

Il `MyEntry` controllo viene eseguito dal specifico della piattaforma `MyEntryRenderer` classi che derivano dalla `EntryRenderer` classe per ogni piattaforma. Di conseguenza, ogni `MyEntry` controllo viene eseguito il rendering con un colore di sfondo specifico della piattaforma, come illustrato nelle schermate seguenti:

![](entry-images/screenshots.png "Controllo MyEntry in ciascuna piattaforma")

Il `EntryRenderer` classe espone il `OnElementChanged` metodo, che viene chiamato quando viene creato il controllo di xamarin. Forms per eseguire il rendering del controllo nativo corrispondente. Questo metodo accetta un `ElementChangedEventArgs` parametro contenente `OldElement` e `NewElement` proprietà. Queste proprietà rappresentano l'elemento di xamarin. Forms che il renderer *è stata* collegata, l'elemento di xamarin. Forms e che il renderer *è* associata rispettivamente. Nell'applicazione di esempio di `OldElement` proprietà sarà `null` e `NewElement` proprietà contiene un riferimento al `MyEntry` controllo.

Una versione sottoposta a override del `OnElementChanged` metodo la `MyEntryRenderer` classe è il luogo per eseguire la personalizzazione del controllo nativo. Un riferimento tipizzato al controllo nativo utilizzato nella piattaforma sono accessibili tramite il `Control` proprietà. Inoltre, un riferimento al controllo di xamarin. Forms che viene eseguito il rendering può essere ottenuto tramite il `Element` proprietà, anche se non viene utilizzato nell'applicazione di esempio.

Ogni classe renderer personalizzato è decorato con un `ExportRenderer` attributo che registra il renderer con xamarin. Forms. L'attributo accetta due parametri: il nome del tipo del controllo xamarin. Forms viene eseguito il rendering e il nome del tipo di renderer personalizzato. Il `assembly` prefisso per l'attributo specifica che l'attributo si applica all'intero assembly.

Le sezioni seguenti illustrano l'implementazione di ogni specifico della piattaforma `MyEntryRenderer` classe renderer personalizzato.

### <a name="creating-the-custom-renderer-on-ios"></a>Creazione di Renderer personalizzato in iOS

Esempio di codice seguente viene illustrato il renderer personalizzato per la piattaforma iOS:

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

La chiamata per la classe base `OnElementChanged` metodo crea un'istanza di un iOS `UITextField` controllo con un riferimento al controllo viene assegnato per il renderer `Control` proprietà. Il colore di sfondo viene quindi impostato su viola chiaro con il `UIColor.FromRGB` metodo.

### <a name="creating-the-custom-renderer-on-android"></a>Creazione di Renderer personalizzato in Android

Esempio di codice seguente viene illustrato il renderer personalizzato per la piattaforma Android:

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

La chiamata per la classe base `OnElementChanged` metodo crea un'istanza di un Android `EditText` controllo con un riferimento al controllo viene assegnato per il renderer `Control` proprietà. Viene impostato il colore di sfondo verde chiaro con il `Control.SetBackgroundColor` metodo.

### <a name="creating-the-custom-renderer-on-windows-phone-and-uwp"></a>Creazione di Renderer personalizzato in Windows Phone e UWP

Esempio di codice seguente viene illustrato il renderer personalizzato per Windows Phone e UWP:

```csharp
[assembly: ExportRenderer(typeof(MyEntry), typeof(MyEntryRenderer))]
namespace CustomRenderer.WinPhone81
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

La chiamata per la classe base `OnElementChanged` metodo crea un `TextBox` controllo con un riferimento al controllo viene assegnato per il renderer `Control` proprietà. Il colore di sfondo viene quindi impostato su ciano creando un `SolidColorBrush` istanza.

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato come creare un renderer personalizzato per il xamarin. Forms [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) (controllo), consentendo agli sviluppatori di eseguire l'override per il rendering predefinito nativo con il proprio rendering specifico della piattaforma. Renderer personalizzati forniscono un approccio potente per la personalizzazione dell'aspetto dei controlli di xamarin. Forms. Possono essere utilizzati per lo stile piccole modifiche o sofisticati layout specifici della piattaforma e personalizzazione del comportamento.


## <a name="related-links"></a>Collegamenti correlati

- [CustomRendererEntry (esempio)](https://developer.xamarin.com/samples/xamarin-forms/customrenderers/entry/)
