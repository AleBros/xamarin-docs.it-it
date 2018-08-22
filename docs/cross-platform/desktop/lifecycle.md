---
ms.assetid: 7C132A7C-4973-4B2D-98DC-3661C08EA33F
title: Visual Studio WPF. Ciclo di vita App xamarin. Forms
description: Questo documento vengono confrontate le analogie e differenze tra il ciclo di vita dell'applicazione per le applicazioni xamarin. Forms e WPF. Descrive anche la struttura ad albero visuale, grafica, risorse e stili.
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: a59f257d1e6285fa2d899271a1aae9778b04d985
ms.sourcegitcommit: 47709db4d115d221e97f18bc8111c95723f6cb9b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2018
ms.locfileid: "40251057"
---
# <a name="wpf-vs-xamarinforms-app-lifecycle"></a>Visual Studio WPF. Ciclo di vita App xamarin. Forms

Xamarin. Forms richiede molte indicazioni per la progettazione dal framework basati su XAML prima di essa, in particolare WPF. Tuttavia, in altri modi esso devia in modo significativo che può essere un punto permanente per gli utenti il tentativo di eseguire la migrazione tramite. Questo documento tenta di identificare alcuni di questi problemi e fornire indicazioni laddove possibile per bridge della Knowledge base WPF a xamarin. Forms.

## <a name="app-lifecycle"></a>Ciclo di vita App

Il ciclo di vita dell'applicazione tra WPF e xamarin. Forms è simile. Entrambi avviare nel codice esterno (piattaforma) e avviare l'interfaccia utente tramite una chiamata al metodo. La differenza è che xamarin. Forms viene sempre avviato in un assembly specifico della piattaforma che quindi inizializza e crea l'interfaccia utente per l'app.

**WPF**

- `Main method > App > MainWindow`

> [!NOTE]
> Il `Main` metodo è, per impostazione predefinita, automaticamente generata e non sono visibili nel codice.

**Xamarin.Forms**

- **iOS** &ndash; `Main method > AppDelegate > App > ContentPage`
- **Android** &ndash; `MainActivity > App > ContentPage`
- **PIATTAFORMA UWP** &ndash; `Main method > App(UWP) > MainPage(UWP) > App > ContentPage`

### <a name="application-class"></a>Classe dell'applicazione

Sia WPF e xamarin. Forms hanno una `Application` classe che viene creato come un singleton. Nella maggior parte dei casi, le app esegue la derivazione da questa classe per fornire un'applicazione personalizzata, anche se ciò non è strettamente necessario in WPF. Entrambi esporre un `Application.Current` proprietà per individuare il singleton creato.

### <a name="global-properties--persistence"></a>Proprietà globali e persistenza

Sia WPF e xamarin. Forms hanno una `Application.Properties` dizionario disponibile in cui è possibile archiviare gli oggetti globali a livello di app che sono accessibili ovunque nell'applicazione. La differenza principale è che sarà di xamarin. Forms _persist_ eventuali tipi primitivi archiviati nella raccolta quando l'app viene sospesa e ricaricarli quando si è riavviati e. WPF supportano automaticamente questo comportamento, invece, la maggior parte degli sviluppatori si basavano su spazio di memorizzazione isolato o utilizzarlo predefiniti `Settings` supportano.

## <a name="defining-pages-and-the-visual-tree"></a>Definizione di pagine e la struttura ad albero visuale

In WPF viene usato il `Window` come elemento radice per qualsiasi elemento visivo di primo livello. Definisce un oggetto HWND del mondo di Windows per visualizzare le informazioni. È possibile creare e visualizzare tutte le finestre contemporaneamente nel modo desiderato in WPF.

In xamarin. Forms, l'oggetto visivo di primo livello è sempre definita dalla piattaforma, ad esempio iOS, è un `UIWindow`. Esegue il rendering di xamarin. Forms è contenuto in queste rappresentazioni di piattaforma nativa usando un `Page` classe. Ogni `Page` in xamarin. Forms rappresenta una "pagina" univoca nell'applicazione, in cui solo uno è visibile alla volta.

Entrambi WPFs `Window` e xamarin. Forms `Page` includono un `Title` dispone di proprietà per influenzare il titolo visualizzato e sia un `Icon` proprietà per visualizzare un'icona specifica per la pagina (**nota** che la titolo e icona non sono sempre visibili in xamarin. Forms). Inoltre, è possibile modificare common proprietà visive su entrambi, ad esempio il colore di sfondo o l'immagine.

È tecnicamente possibile eseguire il rendering in due visualizzazioni separate della piattaforma (ad esempio, definire due `UIWindow` degli oggetti e che il secondo esegua il rendering a un display esterno o AirPlay), richiede codice specifico della piattaforma per eseguire questa operazione e non è una funzionalità supportata direttamente dal Xamarin. Forms stesso.

### <a name="views"></a>Visualizzazioni

La gerarchia visual per entrambi i Framework è simile. WPF è un po' più approfondito a causa di un relativo supporto per i documenti WYSIWYG.

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

Xamarin. Forms è orientato principalmente su scenari per dispositivi mobili. Di conseguenza, le applicazioni sono _attivati_, _sospesa_, e _riattivato_ quando l'utente interagisce con essi. È simile a fare clic sul allontanando il `Window` in un'applicazione WPF e sono presenti un set di metodi ed eventi corrispondenti, è possibile eseguire l'override o hook per monitorare questo comportamento.

| Scopo | WPF (metodo) | Xamarin. Forms (metodo) |
|--- |--- |--- |
|Attivazione iniziale|ctor + Window.OnLoaded|ctor + Page.OnStart|
|Illustrato|Window.IsVisibleChanged|Page.Appearing|
|Hidden|Window.IsVisibleChanged|Page.Disapearing|
|Sospendere/Lost messa a fuoco|Window.OnDeactivated|Page.OnSleep|
|Stato attivo/ottenuto attivato|Window.OnActivated|Page.OnResume|
|Chiuso|Window. OnClosing + Window.OnClosed|N/D|


Supporto sia per nascondere/visualizzare anche i controlli figlio, in WPF è una proprietà a tre stati `IsVisible` (visibile, nascosto e compresso). In xamarin. Forms, è sufficiente visibile o nascosto mediante il `IsVisible` proprietà.

### <a name="layout"></a>Layout

Layout di pagina si verifica nella stessa 2-sessione (misura/Disponi) che si verifica in WPF. È possibile connettere al layout di pagina per l'override dei metodi seguenti in xamarin. Forms `Page` classe:

| Metodo | Scopo |
|--- |--- |
|OnChildMeasureInvalidated|Dimensioni preferite dell'elemento figlio sono stata modificata.|
|OnSizeAllocated|Pagina è stata assegnata una larghezza/altezza.|
|Evento LayoutChanged|Layout/dimensioni della pagina è stata modificata.|

Non esiste alcun evento di layout globale che viene chiamato subito, né è disponibile una global `CompositionTarget.Rendering` evento, ad esempio disponibili in WPF.

#### <a name="common-layout-properties"></a>Proprietà di layout comuni

WPF e xamarin. Forms supportano entrambi `Margin` spaziatura controllo intorno a un elemento, e `Padding` controllo spaziatura _all'interno di_ un elemento. Inoltre, la maggior parte delle viste di layout di xamarin. Forms ha le proprietà per controllare la spaziatura (ad esempio, riga o colonna).

Inoltre, la maggior parte degli elementi dispongono di proprietà per influire sul modo in cui si trovano nel contenitore padre:

| WPF | Xamarin.Forms | Scopo |
|--- |--- |--- |
|HorizontalAlignment|HorizontalOptions|Opzioni di sinistra/Center/Right/Stretch|
|VerticalAlignment|VerticalOptions|Opzioni Top/Center o in basso/Stretch|

> [!NOTE]
> L'interpretazione di queste proprietà dipende dal contenitore padre.

#### <a name="layout-views"></a>Viste di layout

WPF e xamarin. Forms è possibile usare i controlli di layout per posizionare gli elementi figlio. Nella maggior parte dei casi, queste sono molto vicini in termini di funzionalità.

| WPF | Xamarin.Forms | Stile di layout |
|--- |--- |--- |
|StackPanel|StackLayout|Da sinistra a destra o alto verso il basso infinita di sovrapposizione|
|Grid|Grid|Formato tabulare (righe e colonne)|
|DockPanel|N/D|Ai bordi della finestra di ancoraggio|
|Canvas|AbsoluteLayout|Posizionamento/coordinata in pixel|
|WrapPanel|N/D|Stack di ritorno a capo|
|N/D|RelativeLayout|Basato su regole il posizionamento relativo|

> [!NOTE]
> Xamarin. Forms non supporta un `GridSplitter`.

Entrambe le piattaforme usano _le proprietà associate_ per ottimizzare gli elementi figlio.

### <a name="rendering"></a>Rendering

I meccanismi di rendering per WPF e xamarin. Forms sono completamente diversi. In WPF, i controlli che si crea direttamente il rendering del contenuto pixel sullo schermo. WPF gestisce due oggetti grafici (_alberi_) per rappresentare questa - il _albero logico_ rappresenta i controlli, come definito nel codice o XAML e il _struttura ad albero visuale_ rappresenta il il rendering effettivo che si verifica nella schermata di cui è eseguito direttamente tramite l'elemento visivo (tramite un metodo di disegno virtuale), o tramite un XAML definito `ControlTemplate` che possono essere sostituiti o personalizzati. In genere, la struttura visiva è più complessa perché questo include, ad esempio i bordi intorno a controlli, le etichette per contenuto implicito e così via. WPF include un set di API (`LogicalTreeHelper` e `VisualTreeHelper`) per esaminare questi due grafici di oggetti.

In xamarin. Forms, i controlli di definire in una `Page` sono oggetti di dati molto semplici. Questi sono simili alla rappresentazione dell'albero logico, ma mai il rendering del contenuto in modo indipendente. In questo caso sono le _modello di dati_ che influenza il rendering degli elementi. Il rendering effettivo avviene tramite un [separare set di _renderer visual_ che viene eseguito il mapping a ogni tipo di controllo](~/xamarin-forms/app-fundamentals/custom-renderer/index.md). Tali renderer vengono registrate in ogni progetto specifico della piattaforma da assembly specifici della piattaforma xamarin. Forms. È possibile visualizzare un elenco [qui](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md). Oltre a sostituire o estendere il renderer, xamarin. Forms include anche il supporto per [effetti](~/xamarin-forms/app-fundamentals/effects/index.md) usabili per influenzare il rendering in base a una per ogni piattaforma nativo.

#### <a name="the-logicalvisual-tree"></a>L'albero logico e Visual

Non è disponibile alcuna API esposte per esaminare l'albero logico in xamarin. Forms - ma è possibile usare la Reflection per ottenere le stesse informazioni. Ad esempio, [Ecco un metodo che può enumerare gli elementi figlio logici](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Extensions/ElementExtensions.cs#L108) tramite reflection.

## <a name="graphics"></a>Grafica

Xamarin. Forms non include un sistema di grafica per le primitive di oltre un semplice rettangolo (`BoxView`). È possibile includere, ad esempio librerie di terze parti 3rd [SkiaSharp](~/graphics-games/skiasharp/index.md) per ottenere il disegno 2D multipiattaforma, o [UrhoSharp](~/graphics-games/urhosharp/index.md) per 3D.

## <a name="resources"></a>Risorse

Xamarin. Forms e WPF prevedono il concetto di risorse e dizionari risorse. È possibile inserire qualsiasi tipo di oggetto in un `ResourceDictionary` con una chiave e quindi cercare con `{StaticResource}` per operazioni che non verranno modificata, o `{DynamicResource}` per operazioni che possono cambiare nel dizionario in fase di esecuzione. I meccanismi e utilizzo sono gli stessi con una differenza: xamarin. Forms che è necessario definire il `ResourceDictionary` per assegnare al `Resources` proprietà mentre WPF pre-crea uno e lo assegna automaticamente.

Ad esempio, vedere la definizione seguente:

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

Se non si definisce la `ResourceDictionary`, viene generato un errore di runtime.

## <a name="styles"></a>Stili

Gli stili sono completamente supportati in xamarin. Forms e può essere utilizzata al tema gli elementi di xamarin. Forms che costituiscono l'interfaccia utente. Supportano l'ereditarietà di proprietà, eventi e dati, i trigger tramite `BasedOn`e le ricerche di risorse per i valori. Gli stili vengono applicati agli elementi entrambe in modo esplicito tramite il `Style` proprietà o implicitamente a non fornire una chiave di risorsa - come WPF.

### <a name="device-styles"></a>Stili di dispositivo

WPF include un set di proprietà predefinite (archiviati come valori statici in un set di classi statiche, ad esempio `SystemColors`) che definiscono i colori di sistema, i tipi di carattere e le metriche in forma di valori e le chiavi di risorsa. Xamarin. Forms è simile, ma definisce un set di [stili di dispositivo](~/xamarin-forms/user-interface/styles/device.md) per rappresentare gli stessi elementi. Questi stili sono forniti dal Framework anche e impostati sui valori in base all'ambiente di runtime (ad esempio accessibilità).

**WPF**

```xaml
<Label Text="Title" Foreground="{DynamicResource {x:Static SystemColors.DesktopBrushKey}}" />
```

**Xamarin.Forms**

```xaml
<Label Text="Title" Style="{DynamicResource TitleStyle}" />
```
