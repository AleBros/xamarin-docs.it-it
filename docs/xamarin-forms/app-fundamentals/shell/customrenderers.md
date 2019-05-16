---
title: Renderer personalizzati della shell Xamarin.Forms
description: Le applicazioni shell Xamarin.Forms sono estremamente personalizzabili tramite le proprietà e i metodi esposti dalle diverse classi della shell. Tuttavia, è anche possibile creare un renderer personalizzato della shell quando sono necessarie personalizzazioni specifiche della piattaforma più sofisticate.
ms.prod: xamarin
ms.assetid: 3B1A6AE8-1D1E-4C34-B9AB-48F4444FEF32
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/06/2019
ms.openlocfilehash: c1418e581e2896a77d0b83a46ecc8d3a0ce8c510
ms.sourcegitcommit: 9d90a26cbe13ebd106f55ba4a5445f28d9c18a1a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65054211"
---
# <a name="xamarinforms-shell-custom-renderers"></a>Renderer personalizzati della shell Xamarin.Forms

![](~/media/shared/preview.png "Quest'API è attualmente in versione non definitiva")

Uno dei vantaggi delle applicazioni shell Xamarin.Forms è l'elevata possibilità di personalizzarne l'aspetto e il comportamento tramite le proprietà e i metodi esposti dalle diverse classi della shell. Tuttavia, è anche possibile creare un renderer personalizzato della shell quando sono necessarie personalizzazioni specifiche della piattaforma più sofisticate. Come nel caso di altri renderer personalizzati, è possibile aggiungere un renderer personalizzato della shell a un progetto per una sola piattaforma per personalizzarne l'aspetto e il comportamento mantenendo il comportamento predefinito nell'altra piattaforma oppure è possibile aggiungere un renderer personalizzato della shell diverso al progetto di ogni piattaforma, per personalizzare l'aspetto e il comportamento sia in iOS che in Android.

Il rendering delle applicazioni shell viene eseguito usando la classe `ShellRenderer` in iOS e Android. In iOS la classe `ShellRenderer` è disponibile nello spazio dei nomi `Xamarin.Forms.Platform.iOS`. In Android la classe `ShellRenderer` è disponibile nello spazio dei nomi `Xamarin.Forms.Platform.Android`.

Di seguito è illustrato il processo di creazione di un renderer personalizzato della shell:

1. Creare sottoclassi della classe `Shell`. Questa operazione sarà già stata eseguita nell'applicazione shell.
1. Utilizzare la classe `Shell` sottoclassata. Questa operazione sarà già stata eseguita nell'applicazione shell.
1. Creare una classe renderer personalizzata che deriva dalla classe `ShellRenderer`, nelle piattaforme necessarie.

## <a name="create-a-custom-renderer-class"></a>Creare una classe renderer personalizzata

Di seguito è illustrato il processo di creazione di una classe renderer personalizzata della shell:

1. Creare una sottoclasse della classe `ShellRenderer`.
1. Eseguire l'override dei metodi necessari per la personalizzazione richiesta.
1. Aggiungere un oggetto `ExportRendererAttribute` alla sottoclasse `ShellRenderer` per specificare che verrà usata per eseguire il rendering dell'applicazione shell. L'attributo viene usato per registrare il renderer personalizzato in Xamarin.Forms.

> [!NOTE]
> È facoltativo fornire un renderer personalizzato della shell nel progetto di ogni piattaforma. Se non viene registrato un renderer personalizzato, verrà usata la classe `ShellRenderer` predefinita.

La classe `ShellRenderer` espone i metodi sottoponibili a override seguenti:

| iOS | Android |
| --- | --- |
| `SetElementSize`<br />`CreateFlyoutRenderer`<br />`CreateNavBarAppearanceTracker`<br />`CreatePageRendererTracker`<br />`CreateShellFlyoutContentRenderer`<br />`CreateShellItemRenderer`<br />`CreateShellItemTransition`<br />`CreateShellSearchResultsRenderer`<br />`CreateShellSectionRenderer`<br />`CreateTabBarAppearanceTracker`<br />`Dispose`<br />`OnCurrentItemChanged`<br />`OnElementPropertyChanged`<br />`OnElementSet`<br />`UpdateBackgroundColor` | `CreateFragmentForPage`<br />`CreateShellFlyoutContentRenderer`<br />`CreateShellFlyoutRenderer`<br />`CreateShellItemRenderer`<br />`CreateShellSectionRenderer`<br />`CreateTrackerForToolbar`<br />`CreateToolbarAppearanceTracker`<br />`CreateTabLayoutAppearanceTracker`<br />`CreateBottomNavViewAppearanceTracker`<br />`OnElementPropertyChanged`<br />`OnElementSet`<br />`SwitchFragment`<br />`Dispose` |

> [!IMPORTANT]
> Ci sono altre classi renderer della shell, ad esempio `ShellSectionRenderer` e `ShellItemRenderer`, sia in iOS che in Android. Tuttavia, queste classi renderer aggiuntive vengono create tramite override nella classe `ShellRenderer`. La personalizzazione del comportamento di queste classi renderer aggiuntive può quindi essere ottenuta tramite la creazione di loro sottoclassi e la creazione di un'istanza della sottoclasse nell'override appropriato nella classe `ShellRenderer` sottoclassata.

### <a name="ios-example"></a>Esempio iOS

L'esempio di codice seguente illustra un oggetto `ShellRenderer` sottoclassato, per iOS, che imposta un'immagine di sfondo nella barra di spostamento dell'applicazione shell:

```csharp
using UIKit;
using Xamarin.Forms;
using Xamarin.Forms.Platform.iOS;

[assembly: ExportRenderer(typeof(Xaminals.AppShell), typeof(Xaminals.iOS.MyShellRenderer))]
namespace Xaminals.iOS
{
    public class MyShellRenderer : ShellRenderer
    {
        protected override IShellSectionRenderer CreateShellSectionRenderer(ShellSection shellSection)
        {
            var renderer = base.CreateShellSectionRenderer(shellSection);
            if (renderer != null)
            {
                (renderer as ShellSectionRenderer).NavigationBar.SetBackgroundImage(UIImage.FromFile("monkey.png"), UIBarMetrics.Default);
            }
            return renderer;
        }
    }
}
```

La classe `MyShellRenderer` esegue l'override del metodo `CreateShellSectionRenderer` e recupera il renderer creato dalla classe base. Modifica quindi il renderer impostando un'immagine di sfondo nella barra di spostamento, prima di restituire il renderer.

### <a name="android-example"></a>Esempio Android

L'esempio di codice seguente mostra un oggetto `ShellRenderer` sottoclassato, per Android, che imposta un'immagine di sfondo nella barra di spostamento dell'applicazione shell:

```csharp
using Android.Content;
using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

[assembly: ExportRenderer(typeof(Xaminals.AppShell), typeof(Xaminals.Droid.MyShellRenderer))]
namespace Xaminals.Droid
{
    public class MyShellRenderer : ShellRenderer
    {
        public MyShellRenderer(Context context) : base(context)
        {
        }

        protected override IShellToolbarAppearanceTracker CreateToolbarAppearanceTracker()
        {
            return new MyShellToolbarAppearanceTracker(this);
        }
    }
}
```

La classe `MyShellRenderer` esegue l'override del metodo `CreateToolbarAppearanceTracker` e restituisce un'istanza della classe `MyShellToolbarAppearanceTracker`. La classe `MyShellToolbarAppearanceTracker`, che deriva dalla classe `ShellToolbarAppearanceTracker`, è illustrata nell'esempio seguente:

```csharp
using Android.Support.V7.Widget;
using Xamarin.Forms;
using Xamarin.Forms.Platform.Android;

namespace Xaminals.Droid
{
    public class MyShellToolbarAppearanceTracker : ShellToolbarAppearanceTracker
    {
        public MyShellToolbarAppearanceTracker(IShellContext context) : base(context)
        {
        }

        public override void SetAppearance(Toolbar toolbar, IShellToolbarTracker toolbarTracker, ShellAppearance appearance)
        {
            base.SetAppearance(toolbar, toolbarTracker, appearance);
            toolbar.SetBackgroundResource(Resource.Drawable.monkey);
        }
    }
}
```

La classe `MyShellToolbarAppearanceTracker` esegue l'override del metodo `SetAppearance` e modifica la barra degli strumenti impostando su di essa un'immagine di sfondo.

> [!IMPORTANT]
> È necessario solo aggiungere `ExportRendererAttribute` a un renderer personalizzato che deriva dalla classe `ShellRenderer`. Vengono create classi renderer della shell sottoclassate aggiuntive tramite la classe `ShellRenderer` sottoclassata.

## <a name="related-links"></a>Collegamenti correlati

- [Renderer personalizzati di Xamarin.Forms](~/xamarin-forms/app-fundamentals/custom-renderer/index.md)
