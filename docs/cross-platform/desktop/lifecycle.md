---
ms.assetid: 7C132A7C-4973-4B2D-98DC-3661C08EA33F
title: Confronto tra WPF e Ciclo di vita delle app Xamarin.Forms
description: In questo documento vengono confrontate le analogie e le differenze tra il ciclo di vita dell'applicazione per Novell. Forms e le applicazioni WPF. Esamina anche la struttura ad albero visuale, la grafica, le risorse e gli stili.
author: conceptdev
ms.author: crdun
ms.date: 04/26/2017
ms.openlocfilehash: 1d1c207672baf37c91d39fca1d9f628d85971577
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765899"
---
# <a name="wpf-vs-xamarinforms-app-lifecycle"></a>Confronto tra WPF e Ciclo di vita delle app Xamarin.Forms

Novell. Forms richiede un numero elevato di linee guida di progettazione dai Framework basati su XAML precedenti, in particolare WPF. Tuttavia, in altri modi si devia significativamente, che può essere un punto permanente per le persone che tentano di eseguire la migrazione. Questo documento tenta di identificare alcuni di questi problemi e fornisce informazioni aggiuntive laddove possibile per colmare le informazioni WPF a Novell. Forms.

## <a name="app-lifecycle"></a>Ciclo di vita dell'app

Il ciclo di vita dell'applicazione tra WPF e Novell. Forms è simile. Entrambi avviano il codice esterno (piattaforma) e avviano l'interfaccia utente tramite una chiamata al metodo. La differenza è che Novell. Forms viene sempre avviato in un assembly specifico della piattaforma che quindi Inizializza e crea l'interfaccia utente per l'app.

**WPF**

- `Main method > App > MainWindow`

> [!NOTE]
> Il `Main` metodo è, per impostazione predefinita, generato automaticamente e non è visibile nel codice.

**Xamarin.Forms**

- **iOS** &ndash; `Main method > AppDelegate > App > ContentPage`
- **Android** &ndash; `MainActivity > App > ContentPage`
- **UWP** &ndash; `Main method > App(UWP) > MainPage(UWP) > App > ContentPage`

### <a name="application-class"></a>Classe Application

WPF e Novell. Forms hanno una `Application` classe che viene creata come singleton. Nella maggior parte dei casi, le app derivano da questa classe per fornire un'applicazione personalizzata, anche se non è strettamente necessaria in WPF. Entrambe espongono `Application.Current` una proprietà per individuare il singleton creato.

### <a name="global-properties--persistence"></a>Proprietà globali + persistenza

Per WPF e Novell. Forms è `Application.Properties` disponibile un dizionario in cui è possibile archiviare gli oggetti globali a livello di app accessibili in qualsiasi punto dell'applicazione. La differenza principale è che Novell. Forms _manterranno tutti i tipi_ primitivi archiviati nella raccolta quando l'app viene sospesa e li ricarica quando viene riavviata. WPF non supporta automaticamente tale comportamento, ma la maggior parte degli sviluppatori si basa su spazio di memorizzazione isolato o utilizza il `Settings` supporto incorporato.

## <a name="defining-pages-and-the-visual-tree"></a>Definizione di pagine e struttura ad albero visuale

WPF USA `Window` come elemento radice per qualsiasi elemento visivo di primo livello. Definisce un HWND nel mondo Windows per visualizzare le informazioni. È possibile creare e visualizzare il numero di finestre simultaneamente che si desidera in WPF.

In Novell. Forms, l'oggetto visivo di primo livello è sempre definito dalla piattaforma, ad esempio in iOS, è un `UIWindow`. Novell. Forms esegue il rendering del contenuto in queste rappresentazioni di piattaforma `Page` native usando una classe. Ogni `Page` in Novell. Forms rappresenta una "pagina" univoca nell'applicazione, dove ne è visibile solo una alla volta.

Sia WPFs `Window` che Novell. Forms `Page` includono `Title` una proprietà per influenzare `Icon` il titolo visualizzato ed entrambi hanno una proprietà per visualizzare un'icona specifica per la pagina (**si noti** che il titolo e l'icona non sono sempre visibile in Novell. Forms). Inoltre, è possibile modificare le proprietà di oggetti visivi comuni su entrambi, ad esempio il colore di sfondo o l'immagine.

È tecnicamente possibile eseguire il rendering su due viste della piattaforma separate (ad esempio, `UIWindow` definire due oggetti e fare in modo che il secondo esegua il rendering su uno schermo esterno o AirPlay), richiede codice specifico per la piattaforma e non è una funzionalità supportata direttamente di Novell. Forms.

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

Novell. Forms è orientato principalmente agli scenari per dispositivi mobili. Di conseguenza, le applicazioni vengono _attivate_, _sospese_e _riattivate_ quando l'utente interagisce con loro. Questo comportamento è simile a quello di `Window` un'applicazione WPF ed è disponibile un set di metodi ed eventi corrispondenti di cui è possibile eseguire l'override o l'hook per monitorare questo comportamento.

| Scopo | Metodo WPF | Metodo Novell. Forms |
|--- |--- |--- |
|Attivazione iniziale|ctor + Window. OnLoaded|ctor + page. OnStart|
|Illustrato|Window.IsVisibleChanged|Pagina. visualizzazione|
|Hidden|Window.IsVisibleChanged|Pagina. scompare|
|Sospendere/perdere lo stato attivo|Window.OnDeactivated|Pagina. onsleep|
|Attivato/ottenuto lo stato attivo|Window.OnActivated|Page.OnResume|
|Chiuso|Finestra. OnClosing + Window. OnClosed|n/d|

Entrambi supportano anche l'occultamento/visualizzazione dei controlli figlio, in WPF si tratta di una proprietà `IsVisible` a tre stati (visibile, nascosta e compressa). In Novell. Forms, è semplicemente visibile o nascosto tramite la `IsVisible` proprietà.

### <a name="layout"></a>Layout

Il layout di pagina si verifica nello stesso passaggio a 2 (misura/disposizione) che si verifica in WPF. È possibile eseguire l'hook nel layout della pagina eseguendo l'override dei metodi seguenti nella classe Novell `Page` . Forms:

| Metodo | Scopo |
|--- |--- |
|OnChildMeasureInvalidated|La dimensione preferita di un elemento figlio è cambiata.|
|OnSizeAllocated|Alla pagina è stata assegnata una larghezza/altezza.|
|Evento LayoutChanged|Il layout/dimensioni della pagina è stato modificato.|

Non esiste alcun evento di layout globale che viene chiamato oggi, né un evento globale `CompositionTarget.Rendering` come trovato in WPF.

#### <a name="common-layout-properties"></a>Proprietà di layout comuni

WPF e Novell. Forms supportano `Margin` entrambi per controllare la spaziatura intorno a `Padding` un elemento e per controllare la spaziatura _all'interno_ di un elemento. Inoltre, la maggior parte delle visualizzazioni di layout Novell. Forms include proprietà che consentono di controllare la spaziatura, ad esempio riga o colonna.

Inoltre, la maggior parte degli elementi dispone di proprietà per influenzare il modo in cui vengono posizionati nel contenitore padre:

| WPF | Xamarin.Forms | Scopo |
|--- |--- |--- |
|HorizontalAlignment|HorizontalOptions|Opzioni left/Center/Right/stretch|
|VerticalAlignment|VerticalOptions|Opzioni top/Center/Bottom/stretch|

> [!NOTE]
> L'interpretazione effettiva di queste proprietà dipende dal contenitore padre.

#### <a name="layout-views"></a>Visualizzazioni layout

WPF e Novell. Forms usano entrambi i controlli di layout per posizionare gli elementi figlio. Nella maggior parte dei casi, questi sono molto vicini tra loro in termini di funzionalità.

| WPF | Xamarin.Forms | Stile layout |
|--- |--- |--- |
|StackPanel|StackLayout|Stacking infinito da sinistra a destra o dall'alto verso il basso|
|Grid|Grid|Formato tabulare (righe e colonne)|
|DockPanel|n/d|Ancora ai bordi della finestra|
|Canvas|AbsoluteLayout|Posizionamento di pixel/Coordinate|
|WrapPanel|n/d|Wrapping dello stack|
|n/d|RelativeLayout|Posizionamento basato su regole relative|

> [!NOTE]
> Novell. Forms non supporta un `GridSplitter`.

Entrambe le piattaforme utilizzano _proprietà associate_ per ottimizzare gli elementi figlio.

### <a name="rendering"></a>Rendering

I meccanismi di rendering per WPF e Novell. Forms sono radicalmente diversi. In WPF, i controlli creati eseguono direttamente il rendering del contenuto sui pixel sullo schermo. WPF gestisce due oggetti grafici (_alberi_) per rappresentare questo. l' _albero logico_ rappresenta i controlli definiti nel codice o in XAML e la _struttura ad albero visuale_ rappresenta il rendering effettivo che si verifica sullo schermo che viene eseguito direttamente dall'elemento visivo (tramite un metodo di estrazione virtuale) o tramite un oggetto definito `ControlTemplate` da XAML, che può essere sostituito o personalizzato. In genere, la struttura ad albero visuale è più complessa, in quanto include elementi come bordi intorno ai controlli, etichette per contenuto implicito e così via. WPF include un set di API (`LogicalTreeHelper` e `VisualTreeHelper`) per esaminare questi due oggetti grafici.

In Novell. Forms, i controlli definiti in `Page` sono solo oggetti dati semplici. Sono simili alla rappresentazione dell'albero logico, ma non eseguono mai il rendering del contenuto autonomamente. Sono invece il modello di _dati_ che influenza il rendering degli elementi. Il rendering effettivo viene eseguito da un [set separato di _renderer visivi_ di cui è stato eseguito il mapping a ogni tipo di controllo](~/xamarin-forms/app-fundamentals/custom-renderer/index.md). Questi renderer sono registrati in ogni progetto specifico della piattaforma da assembly Novell. Forms specifici della piattaforma. È possibile visualizzare [un elenco.](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md) Oltre a sostituire o estendere il renderer, Novell. Forms dispone anche del supporto per [gli effetti](~/xamarin-forms/app-fundamentals/effects/index.md) che possono essere usati per influenzare il rendering nativo in base alla piattaforma.

#### <a name="the-logicalvisual-tree"></a>Struttura ad albero visuale/logica

Non è presente alcuna API esposta per esaminare l'albero logico in Novell. Forms, ma è possibile usare la reflection per ottenere le stesse informazioni. Ecco, ad esempio, [un metodo che può enumerare gli elementi figlio logici](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Extensions/ElementExtensions.cs#L108) con Reflection.

## <a name="graphics"></a>Grafica

Novell. Forms non include un sistema grafico per le primitive oltre un rettangolo semplice`BoxView`(). È possibile includere librerie di terze parti, ad esempio [SkiaSharp](~/graphics-games/skiasharp/index.md) , per ottenere il disegno 2D multipiattaforma oppure [UrhoSharp](~/graphics-games/urhosharp/index.md) per 3D.

## <a name="resources"></a>Risorse

WPF e Novell. Forms hanno il concetto di risorse e dizionari risorse. È possibile inserire qualsiasi tipo di oggetto in `ResourceDictionary` un oggetto con una chiave e quindi cercarlo `{StaticResource}` con per gli elementi che non cambiano o `{DynamicResource}` per elementi che possono essere modificati nel dizionario in fase di esecuzione. L'utilizzo e i meccanismi sono uguali con una differenza: Per Novell. Forms è necessario definire `ResourceDictionary` l'oggetto da assegnare `Resources` alla proprietà, mentre WPF ne crea una prima e la assegna automaticamente.

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

Se non si definisce `ResourceDictionary`, viene generato un errore di Runtime.

## <a name="styles"></a>Stili

Gli stili sono anche completamente supportati in Novell. Forms e possono essere usati per eseguire il tema degli elementi Novell. Forms che compongono l'interfaccia utente. Supportano trigger (proprietà, evento e dati), ereditarietà tramite `BasedOn`e ricerche di risorse per i valori. Gli stili vengono applicati agli elementi in modo esplicito `Style` tramite la proprietà o in modo implicito non fornendo una chiave di risorsa, proprio come WPF.

### <a name="device-styles"></a>Stili di dispositivo

WPF dispone di un set di proprietà predefinite, archiviate come valori statici in un set di classi statiche come `SystemColors`, che determinano i colori di sistema, i tipi di carattere e le metriche sotto forma di valori e chiavi di risorsa. Novell. Forms è simile, ma definisce un set di [stili di dispositivo](~/xamarin-forms/user-interface/styles/device.md) per rappresentare gli stessi elementi. Questi stili sono forniti dal Framework e impostati sui valori in base all'ambiente di runtime, ad esempio l'accessibilità.

**WPF**

```xaml
<Label Text="Title" Foreground="{DynamicResource {x:Static SystemColors.DesktopBrushKey}}" />
```

**Xamarin.Forms**

```xaml
<Label Text="Title" Style="{DynamicResource TitleStyle}" />
```
