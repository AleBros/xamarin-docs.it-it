---
title: ''
description: Il Xamarin.Forms controllo Entry consente di modificare una singola riga di testo. Questo articolo illustra come creare un renderer personalizzato per il controllo Entry che consenta agli sviluppatori di eseguire l'override del rendering nativo predefinito usando la propria personalizzazione specifica della piattaforma.
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 61bd66fd25b7aea3e5be346f79e63d410164b002
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138983"
---
# <a name="customizing-an-entry"></a>Personalizzazione di una voce

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-entry)

_Il Xamarin.Forms controllo Entry consente di modificare una singola riga di testo. Questo articolo illustra come creare un renderer personalizzato per il controllo voce, consentendo agli sviluppatori di eseguire l'override del rendering nativo predefinito con una personalizzazione specifica della piattaforma._

Ogni Xamarin.Forms controllo dispone di un renderer associato per ogni piattaforma che crea un'istanza di un controllo nativo. Quando [`Entry`](xref:Xamarin.Forms.Entry) viene eseguito il rendering di un controllo da parte di un' Xamarin.Forms applicazione, in iOS `EntryRenderer` viene creata un'istanza della classe, che a sua volta crea un'istanza di un `UITextField` controllo nativo. Nella piattaforma Android la classe `EntryRenderer` crea un'istanza di un controllo `EditText`. Nella piattaforma UWP (Universal Windows Platform) la classe `EntryRenderer` crea un'istanza di un controllo `TextBox`. Per ulteriori informazioni sul renderer e sulle classi di controlli native che Xamarin.Forms controllano la mappa a, vedere [classi base renderer e controlli nativi](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md).

Il diagramma seguente illustra la relazione tra il [`Entry`](xref:Xamarin.Forms.Entry) controllo e i controlli nativi corrispondenti che lo implementano:

![](entry-images/entry-classes.png "Relationship Between Entry Control and Implementing Native Controls")

Il processo di rendering può trarre vantaggio da per implementare personalizzazioni specifiche della piattaforma creando un renderer personalizzato per il [`Entry`](xref:Xamarin.Forms.Entry) controllo su ogni piattaforma. Il processo per eseguire questa operazione è il seguente:

1. [Creare](#Creating_the_Custom_Entry_Control) un Xamarin.Forms controllo personalizzato.
1. [Utilizzare](#Consuming_the_Custom_Control) il controllo personalizzato da Xamarin.Forms .
1. [Creare](#Creating_the_Custom_Renderer_on_each_Platform) il renderer personalizzato per il controllo in ogni piattaforma.

Ogni elemento verrà ora discusso a sua volta per implementare un [`Entry`](xref:Xamarin.Forms.Entry) controllo con un colore di sfondo diverso in ogni piattaforma.

> [!IMPORTANT]
> Questo articolo illustra come creare un semplice renderer personalizzato. Non è tuttavia necessario creare un renderer personalizzato per implementare un controllo `Entry` con un colore di sfondo diverso in ogni piattaforma. Questa operazione può essere eseguita più facilmente usando la [`Device`](xref:Xamarin.Forms.Device) classe, o l' `OnPlatform` estensione di markup, per fornire valori specifici della piattaforma. Per altre informazioni, vedere [Providing Platform-Specific Values](~/xamarin-forms/platform/device.md#provide-platform-specific-values) (Specificare valori specifici della piattaforma) e [OnPlatform Markup Extension](~/xamarin-forms/xaml/markup-extensions/consuming.md#onplatform-markup-extension) (Estensione di markup OnPlatform).

<a name="Creating_the_Custom_Entry_Control" />

## <a name="creating-the-custom-entry-control"></a>Creazione di un controllo Entry personalizzato

[`Entry`](xref:Xamarin.Forms.Entry)È possibile creare un controllo personalizzato creando una sottoclasse del `Entry` controllo, come illustrato nell'esempio di codice seguente:

```csharp
public class MyEntry : Entry
{
}
```

Il `MyEntry` controllo viene creato nel progetto di libreria .NET standard ed è semplicemente un [`Entry`](xref:Xamarin.Forms.Entry) controllo. La personalizzazione del controllo avviene nel renderer personalizzato. Non è pertanto necessaria alcuna implementazione aggiuntiva nel controllo `MyEntry`.

<a name="Consuming_the_Custom_Control" />

## <a name="consuming-the-custom-control"></a>Uso del controllo personalizzato

Per fare riferimento al controllo `MyEntry` in XAML nel progetto di libreria .NET Standard, è possibile dichiarare uno spazio dei nomi per il percorso e usare il prefisso dello spazio dei nomi nell'elemento controllo. Nell'esempio di codice riportato di seguito viene illustrato come il controllo `MyEntry` può essere usato da una pagina XAML:

```xaml
<ContentPage ...
    xmlns:local="clr-namespace:CustomRenderer;assembly=CustomRenderer"
    ...>
    ...
    <local:MyEntry Text="In Shared Code" />
    ...
</ContentPage>
```

Il prefisso dello spazio dei nomi `local` può avere qualsiasi nome. I valori `clr-namespace` e `assembly` devono tuttavia corrispondere ai dettagli del controllo personalizzato. Dopo aver dichiarato lo spazio dei nomi, il prefisso viene usato per fare riferimento al controllo personalizzato.

Nell'esempio di codice riportato di seguito viene illustrato come il controllo `MyEntry` può essere usato da una pagina C#:

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

Questo codice crea un'istanza di un nuovo [`ContentPage`](xref:Xamarin.Forms.ContentPage) oggetto che visualizzerà un [`Label`](xref:Xamarin.Forms.Label) `MyEntry` controllo e centrato verticalmente e orizzontalmente nella pagina.

Un renderer personalizzato può essere ora aggiunto a ogni progetto di applicazione per personalizzare l'aspetto del controllo in ogni piattaforma.

<a name="Creating_the_Custom_Renderer_on_each_Platform" />

## <a name="creating-the-custom-renderer-on-each-platform"></a>Creazione del renderer personalizzato in ogni piattaforma

Il processo di creazione della classe di renderer personalizzato è il seguente:

1. Creare una sottoclasse della classe `EntryRenderer` che esegue il rendering del controllo personalizzato.
1. Eseguire l'override del metodo `OnElementChanged` che esegue il rendering del controllo nativo e scrivere la logica per personalizzare il controllo. Questo metodo viene chiamato quando Xamarin.Forms viene creato il controllo corrispondente.
1. Aggiungere un `ExportRenderer` attributo alla classe renderer personalizzata per specificare che verrà usato per eseguire il rendering del Xamarin.Forms controllo. Questo attributo viene usato per registrare il renderer personalizzato con Xamarin.Forms .

> [!NOTE]
> La specifica di un renderer personalizzato nel progetto di ogni piattaforma è facoltativa. Se un renderer personalizzato non è registrato, verrà usato il renderer predefinito per la classe di base del controllo.

Il diagramma seguente illustra le responsabilità di ogni progetto nell'applicazione di esempio, insieme alle relazioni tra di essi:

![](entry-images/solution-structure.png "MyEntry Custom Renderer Project Responsibilities")

Il rendering del controllo `MyEntry` viene eseguito dalle classi `EntryRenderer` specifiche della piattaforma che derivano dalla classe `MyEntryRenderer` per ogni piattaforma. Di conseguenza il rendering di ogni controllo `MyEntry` viene eseguito con un colore di sfondo specifico della piattaforma, come illustrato negli screenshot seguenti:

![](entry-images/screenshots.png "MyEntry Control on each Platform")

La `EntryRenderer` classe espone il `OnElementChanged` metodo, che viene chiamato quando il Xamarin.Forms controllo viene creato per eseguire il rendering del controllo nativo corrispondente. Questo metodo accetta un parametro `ElementChangedEventArgs` che contiene le proprietà `OldElement` e `NewElement`. Queste proprietà rappresentano l' Xamarin.Forms elemento a cui è *stato* collegato il renderer e l' Xamarin.Forms elemento a cui *è* collegato rispettivamente il renderer. Nell'applicazione di esempio la proprietà `OldElement` sarà `null` e la proprietà `NewElement` conterrà un riferimento al controllo `MyEntry`.

La personalizzazione del controllo nativo deve essere eseguita in una versione sostituita del metodo `OnElementChanged` nella classe `MyEntryRenderer`. Un riferimento tipizzato al controllo nativo usato nella piattaforma è accessibile attraverso la proprietà `Control`. Inoltre, un riferimento al controllo di Xamarin.Forms cui è in corso il rendering può essere ottenuto tramite la `Element` proprietà, anche se non viene utilizzato nell'applicazione di esempio.

Ogni classe renderer personalizzata è decorata con un `ExportRenderer` attributo che registra il renderer con Xamarin.Forms . L'attributo accetta due parametri: il nome del tipo del Xamarin.Forms controllo di cui viene eseguito il rendering e il nome del tipo del renderer personalizzato. Il prefisso `assembly` dell'attributo specifica che l'attributo viene applicato all'intero assembly.

Le sezioni seguenti illustrano l'implementazione della classe renderer personalizzato `MyEntryRenderer` specifica di ogni piattaforma.

### <a name="creating-the-custom-renderer-on-ios"></a>Creazione del renderer personalizzato in iOS

L'esempio di codice seguente illustra il renderer personalizzato per la piattaforma iOS:

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

La chiamata al metodo `OnElementChanged` della classe di base crea un'istanza del controllo `UITextField` iOS, con un riferimento al controllo assegnato alla proprietà `Control` del renderer. Il colore di sfondo viene quindi impostato su viola chiaro con il metodo `UIColor.FromRGB`.

### <a name="creating-the-custom-renderer-on-android"></a>Creazione del renderer personalizzato in Android

L'esempio di codice seguente illustra il renderer personalizzato per la piattaforma Android:

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

La chiamata al metodo `OnElementChanged` della classe di base crea un'istanza del controllo `EditText` in Android, con un riferimento al controllo assegnato alla proprietà `Control` del renderer. Il colore di sfondo viene quindi impostato su verde chiaro con il metodo `Control.SetBackgroundColor`.

### <a name="creating-the-custom-renderer-on-uwp"></a>Creazione del renderer personalizzato in UWP

L'esempio di codice seguente illustra il renderer personalizzato per la piattaforma UWP:

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

La chiamata al metodo `OnElementChanged` della classe di base crea un'istanza del controllo `TextBox`, con un riferimento al controllo assegnato alla proprietà `Control` del renderer. Il colore di sfondo viene quindi impostato su ciano creando un'istanza di `SolidColorBrush`.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come creare un renderer di controllo personalizzato per il Xamarin.Forms [`Entry`](xref:Xamarin.Forms.Entry) controllo, consentendo agli sviluppatori di eseguire l'override del rendering nativo predefinito con il proprio rendering specifico della piattaforma. I renderer personalizzati offrono un approccio efficace per personalizzare l'aspetto dei Xamarin.Forms controlli. Possono essere usati per apportare piccole modifiche allo stile o per la personalizzazione di layout e comportamenti sofisticati specifici di una piattaforma.

## <a name="related-links"></a>Collegamenti correlati

- [CustomRendererEntry (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/customrenderers-entry)
