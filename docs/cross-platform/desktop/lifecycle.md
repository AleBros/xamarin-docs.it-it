---
ms.assetid: 7C132A7C-4973-4B2D-98DC-3661C08EA33F
title: Ciclo di vita dell'app WPF rispetto a Xamarin.Forms
description: In questo documento vengono confrontate le analogie e le differenze tra il ciclo di vita dell'applicazione per Xamarin.Forms e le applicazioni WPF. Esamina anche la struttura ad albero visuale, la grafica, le risorse e gli stili.
author: davidortinau
ms.author: daortin
ms.date: 04/26/2017
ms.openlocfilehash: 725af8aebb111ac620a7d1ca5eebf73f31ee4a5e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016479"
---
# <a name="wpf-vs-xamarinforms-app-lifecycle"></a>Ciclo di vita dell'app WPF rispetto a Xamarin.Forms

Xamarin.Forms richiede un numero elevato di linee guida di progettazione dai Framework basati su XAML precedenti, in particolare WPF. Tuttavia, in altri modi si devia significativamente, che può essere un punto permanente per le persone che tentano di eseguire la migrazione. Questo documento tenta di identificare alcuni di questi problemi e fornisce informazioni aggiuntive laddove possibile per colmare le informazioni WPF a Xamarin.Forms.

## <a name="app-lifecycle"></a>Ciclo di vita dell'app

Il ciclo di vita dell'applicazione tra WPF e Xamarin.Forms è simile. Entrambi avviano il codice esterno (piattaforma) e avviano l'interfaccia utente tramite una chiamata al metodo. La differenza è che Xamarin.Forms viene sempre avviato in un assembly specifico della piattaforma che quindi Inizializza e crea l'interfaccia utente per l'app.

**WPF**

- `Main method > App > MainWindow`

> [!NOTE]
> Il `Main` metodo è, per impostazione predefinita, generato automaticamente e non è visibile nel codice.

**Xamarin.Forms**

- `Main method > AppDelegate > App > ContentPage` &ndash; **iOS**
- **Android** &ndash; `MainActivity > App > ContentPage`
- **UWP** &ndash; `Main method > App(UWP) > MainPage(UWP) > App > ContentPage`

### <a name="application-class"></a>Classe Application

WPF e Xamarin.Forms hanno una classe `Application` creata come singleton. Nella maggior parte dei casi, le app derivano da questa classe per fornire un'applicazione personalizzata, anche se non è strettamente necessaria in WPF. Entrambe espongono una proprietà `Application.Current` per individuare il singleton creato.

### <a name="global-properties--persistence"></a>Proprietà globali + persistenza

Sia WPF che Xamarin.Forms dispongono di un dizionario `Application.Properties` disponibile, in cui è possibile archiviare oggetti globali a livello di app accessibili in qualsiasi punto dell'applicazione. La differenza principale è che Xamarin.Forms _manterranno tutti i tipi_ primitivi archiviati nella raccolta quando l'app viene sospesa e li ricarica quando viene riavviata. WPF non supporta automaticamente tale comportamento, ma la maggior parte degli sviluppatori si basa su spazio di memorizzazione isolato o utilizza il supporto `Settings` incorporato.

## <a name="defining-pages-and-the-visual-tree"></a>Definizione di pagine e struttura ad albero visuale

WPF usa il `Window` come elemento radice per qualsiasi elemento visivo di primo livello. Definisce un HWND nel mondo Windows per visualizzare le informazioni. È possibile creare e visualizzare il numero di finestre simultaneamente che si desidera in WPF.

In Xamarin.Forms, l'oggetto visivo di primo livello è sempre definito dalla piattaforma, ad esempio in iOS, è un `UIWindow`. Xamarin.Forms esegue il rendering del contenuto in queste rappresentazioni di piattaforma native usando una classe `Page`. Ogni `Page` in Xamarin.Forms rappresenta una "pagina" univoca nell'applicazione, dove ne è visibile solo una alla volta.

Sia WPFs `Window` che Xamarin.Forms `Page` includono una proprietà `Title` per influenzare il titolo visualizzato ed entrambi hanno una proprietà `Icon` per visualizzare un'icona specifica per la pagina (**si noti** che il titolo e l'icona non sono sempre visibili in Xamarin.Forms ). Inoltre, è possibile modificare le proprietà di oggetti visivi comuni su entrambi, ad esempio il colore di sfondo o l'immagine.

È tecnicamente possibile eseguire il rendering su due viste della piattaforma separate (ad esempio, definire due oggetti `UIWindow` e avere il secondo rendering su uno schermo esterno o AirPlay), richiede codice specifico per la piattaforma e non è una funzionalità supportata direttamente da Xamarin.Forms.

### <a name="views"></a>Visualizzazioni

La gerarchia visiva per entrambi i Framework è simile. WPF è un po' più approfondito a causa del suo supporto per i documenti WYSIWYG.

**WPF**

```
DependencyObject - base class for all bindable things
   Visual - rendering mechanics
      UIElement - common events + interactions
         FrameworkElement - adds layout
            Shape - 2D graphics
            Control - interactive controls
```

**Xamarin.Forms**

```
BindableObject - base class for all bindable things
   Element - basic parent/child support + resources + effects
      VisualElement - adds visual rendering properties (color, fonts, transforms, etc.)
         View - layout + gesture support
```

### <a name="view-lifecycle"></a>Ciclo di vita della visualizzazione

Xamarin.Forms è orientato principalmente agli scenari per dispositivi mobili. Di conseguenza, le applicazioni vengono _attivate_, _sospese_e _riattivate_ quando l'utente interagisce con loro. Questa operazione è simile a fare clic all'interno del `Window` in un'applicazione WPF ed è disponibile un set di metodi e gli eventi corrispondenti di cui è possibile eseguire l'override o l'hook per monitorare questo comportamento.

| Scopo | Metodo WPF | Metodo Xamarin.Forms |
|--- |--- |--- |
|Attivazione iniziale|ctor + Window. OnLoaded|ctor + page. OnStart|
|Illustrato|Finestra. IsVisibleChanged|Pagina. visualizzazione|
|Hidden|Finestra. IsVisibleChanged|Pagina. scompare|
|Sospendere/perdere lo stato attivo|Finestra. OnDeactivated|Pagina. onsleep|
|Attivato/ottenuto lo stato attivo|Finestra. OnActivated|Pagina. onResume|
|Closed|Finestra. OnClosing + Window. OnClosed|N/D|

Entrambi supportano anche l'occultamento/visualizzazione dei controlli figlio, in WPF si tratta di una proprietà a tre stati `IsVisible` (visibile, nascosta e compressa). In Xamarin.Forms, è semplicemente visibile o nascosto tramite la proprietà `IsVisible`.

### <a name="layout"></a>Layout

Il layout di pagina si verifica nello stesso passaggio a 2 (misura/disposizione) che si verifica in WPF. È possibile eseguire l'hook nel layout della pagina eseguendo l'override dei metodi seguenti nella classe `Page` Xamarin.Forms:

| Metodo | Scopo |
|--- |--- |
|OnChildMeasureInvalidated|La dimensione preferita di un elemento figlio è cambiata.|
|OnSizeAllocated|Alla pagina è stata assegnata una larghezza/altezza.|
|Evento LayoutChanged|Il layout/dimensioni della pagina è stato modificato.|

Non esiste alcun evento di layout globale che viene chiamato oggi, né un evento `CompositionTarget.Rendering` globale come trovato in WPF.

#### <a name="common-layout-properties"></a>Proprietà di layout comuni

WPF e Xamarin.Forms supportano entrambi `Margin` per controllare la spaziatura intorno a un elemento e `Padding` per controllare la spaziatura _all'interno_ di un elemento. Inoltre, la maggior parte delle visualizzazioni di layout Xamarin.Forms include proprietà che consentono di controllare la spaziatura, ad esempio riga o colonna.

Inoltre, la maggior parte degli elementi dispone di proprietà per influenzare il modo in cui vengono posizionati nel contenitore padre:

| WPF | Xamarin.Forms | Scopo |
|--- |--- |--- |
|HorizontalAlignment|HorizontalOptions|Opzioni left/Center/Right/stretch|
|VerticalAlignment|VerticalOptions|Opzioni top/Center/Bottom/stretch|

> [!NOTE]
> L'interpretazione effettiva di queste proprietà dipende dal contenitore padre.

#### <a name="layout-views"></a>Visualizzazioni layout

WPF e Xamarin.Forms usano entrambi i controlli di layout per posizionare gli elementi figlio. Nella maggior parte dei casi, questi sono molto vicini tra loro in termini di funzionalità.

| WPF | Xamarin.Forms | Stile layout |
|--- |--- |--- |
|StackPanel|StackLayout|Stacking infinito da sinistra a destra o dall'alto verso il basso|
|Grid|Grid|Formato tabulare (righe e colonne)|
|DockPanel|N/D|Ancora ai bordi della finestra|
|Canvas|AbsoluteLayout|Posizionamento di pixel/Coordinate|
|WrapPanel|N/D|Wrapping dello stack|
|N/D|RelativeLayout|Posizionamento basato su regole relative|

> [!NOTE]
> Xamarin.Forms non supporta un `GridSplitter`.

Entrambe le piattaforme utilizzano _proprietà associate_ per ottimizzare gli elementi figlio.

### <a name="rendering"></a>Rendering

I meccanismi di rendering per WPF e Xamarin.Forms sono radicalmente diversi. In WPF, i controlli creati eseguono direttamente il rendering del contenuto sui pixel sullo schermo. WPF gestisce due oggetti grafici (_alberi_) per rappresentare questo. l' _albero logico_ rappresenta i controlli definiti nel codice o in XAML e la _struttura ad albero visuale_ rappresenta il rendering effettivo che si verifica sullo schermo che viene eseguito direttamente dall'elemento visivo (tramite un metodo di estrazione virtuale) o tramite un `ControlTemplate` definito da XAML che può essere sostituito o personalizzato. In genere, la struttura ad albero visuale è più complessa, in quanto include elementi come bordi intorno ai controlli, etichette per contenuto implicito e così via. WPF include un set di API (`LogicalTreeHelper` e `VisualTreeHelper`) per esaminare questi due oggetti grafici.

In Xamarin.Forms, i controlli definiti in una `Page` sono solo oggetti dati semplici. Sono simili alla rappresentazione dell'albero logico, ma non eseguono mai il rendering del contenuto autonomamente. Sono invece il modello di _dati_ che influenza il rendering degli elementi. Il rendering effettivo viene eseguito da un [set separato di _renderer visivi_ di cui è stato eseguito il mapping a ogni tipo di controllo](~/xamarin-forms/app-fundamentals/custom-renderer/index.md). Questi renderer sono registrati in ogni progetto specifico della piattaforma da assembly Xamarin.Forms specifici della piattaforma. È possibile visualizzare [un elenco.](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md) Oltre a sostituire o estendere il renderer, Xamarin.Forms dispone anche del supporto per [gli effetti](~/xamarin-forms/app-fundamentals/effects/index.md) che possono essere usati per influenzare il rendering nativo in base alla piattaforma.

#### <a name="the-logicalvisual-tree"></a>Struttura ad albero visuale/logica

Non è presente alcuna API esposta per esaminare l'albero logico in Xamarin.Forms, ma è possibile usare la reflection per ottenere le stesse informazioni. Ecco, ad esempio, [un metodo che può enumerare gli elementi figlio logici](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Extensions/ElementExtensions.cs#L108) con Reflection.

## <a name="graphics"></a>Grafica

Xamarin.Forms non include un sistema grafico per le primitive oltre un rettangolo semplice (`BoxView`). È possibile includere librerie di terze parti, ad esempio [SkiaSharp](~/graphics-games/skiasharp/index.md) , per ottenere il disegno 2D multipiattaforma oppure [UrhoSharp](~/graphics-games/urhosharp/index.md) per 3D.

## <a name="resources"></a>Risorse

WPF e Xamarin.Forms hanno il concetto di risorse e dizionari risorse. È possibile inserire qualsiasi tipo di oggetto in una `ResourceDictionary` con una chiave e quindi cercarla con `{StaticResource}` per elementi che non cambiano o `{DynamicResource}` per elementi che possono cambiare nel dizionario in fase di esecuzione. L'utilizzo e i meccanismi sono uguali con una differenza: Xamarin.Forms richiede la definizione dell'`ResourceDictionary` da assegnare alla proprietà `Resources`, mentre WPF ne crea una prima e la assegna automaticamente.

Vedere ad esempio la definizione seguente:

**WPF**

```xaml
<Window.Resources>
   <Color x:Key="redColor">#ff0000</Color>
   ...
</Window.Resources>
```

**Xamarin.Forms**

```xaml
<ContentPage.Resources>
   <ResourceDictionary>
      <Color x:Key="redColor">#ff0000</Color>
      ...
   </ResourceDictionary>
</ContentPage.Resources>
```

Se non si definisce la `ResourceDictionary`, viene generato un errore di Runtime.

## <a name="styles"></a>Stili

Gli stili sono anche completamente supportati in Xamarin.Forms e possono essere usati per eseguire il tema degli elementi Xamarin.Forms che compongono l'interfaccia utente. Supportano trigger (proprietà, evento e dati), ereditarietà tramite `BasedOn`e ricerche di risorse per i valori. Gli stili vengono applicati agli elementi in modo esplicito tramite la proprietà `Style` o in modo implicito non fornendo una chiave di risorsa, proprio come WPF.

### <a name="device-styles"></a>Stili di dispositivo

WPF dispone di un set di proprietà predefinite (archiviate come valori statici in un set di classi statiche, ad esempio `SystemColors`), che determinano i colori di sistema, i tipi di carattere e le metriche sotto forma di valori e chiavi di risorsa. Xamarin.Forms è simile, ma definisce un set di [stili di dispositivo](~/xamarin-forms/user-interface/styles/device.md) per rappresentare gli stessi elementi. Questi stili sono forniti dal Framework e impostati sui valori in base all'ambiente di runtime, ad esempio l'accessibilità.

**WPF**

```xaml
<Label Text="Title" Foreground="{DynamicResource {x:Static SystemColors.DesktopBrushKey}}" />
```

**Xamarin.Forms**

```xaml
<Label Text="Title" Style="{DynamicResource TitleStyle}" />
```
