---
ms.assetid: 7C132A7C-4973-4B2D-98DC-3661C08EA33F
title: Visual Studio WPF. Ciclo di vita App xamarin. Forms
description: Comprendere il processo di avvio delle app e si lavora con gli stati in background
author: asb3993
ms.author: amburns
ms.date: 04/26/2017
ms.openlocfilehash: b4f9aebbbcab48290d37c5732c69267897238272
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="wpf-vs-xamarinforms-app-lifecycle"></a>Visual Studio WPF. Ciclo di vita App xamarin. Forms

Xamarin. Forms diventa molto materiale sussidiario di progettazione da Framework basate su XAML prima di essa, in particolare WPF. Tuttavia, in altri modi esso devia in modo significativo che può essere un punto Sticky Note per gli utenti il tentativo di eseguire la migrazione tramite. Questo documento tenta di identificare alcuni di questi problemi e forniscono materiale sussidiario ove possibile Bridge knowledge WPF a xamarin. Forms.

## <a name="app-lifecycle"></a>Ciclo di vita App

Il ciclo di vita dell'applicazione tra WPF e xamarin. Forms è simile. Avviare nel codice esterno (piattaforma) sia avvia l'interfaccia utente tramite una chiamata al metodo. La differenza è che xamarin. Forms venga sempre avviato in un assembly specifico della piattaforma che consente di inizializzare e crea l'interfaccia utente per l'app.

**WPF**

- `Main method > App > MainWindow`

> [!NOTE]
> Il `Main` metodo è, per impostazione predefinita, automaticamente generato e non sono visibili nel codice.

**Xamarin.Forms**

- **iOS** &ndash; `Main method > AppDelegate > App > ContentPage`
- **Android** &ndash; `MainActivity > App > ContentPage`
- **PIATTAFORMA UWP** &ndash; `Main method > App(UWP) > MainPage(UWP) > App > ContentPage`

### <a name="application-class"></a>Classe dell'applicazione

WPF sia xamarin. Forms è presente un `Application` classe che viene creato come singleton. Nella maggior parte dei casi, le app esegue la derivazione da questa classe per fornire un'applicazione personalizzata, anche se ciò non è strettamente necessario in WPF. Entrambi esporre un `Application.Current` proprietà per individuare il singleton creato.

### <a name="global-properties--persistence"></a>Proprietà globali + persistenza

WPF sia xamarin. Forms è presente un `Application.Properties` dizionario disponibile in cui è possibile archiviare oggetti globali a livello di app che sono accessibili in qualsiasi punto nell'applicazione. La differenza principale è che verrà xamarin. Forms _persist_ qualsiasi archiviati nella raccolta quando l'app viene sospesa e ricaricarli quando si è riproposto i tipi primitivi. WPF non supporta automaticamente questo comportamento, invece, la maggior parte degli sviluppatori si basavano su isolato o utilizzarlo incorporati `Settings` supportano.

## <a name="defining-pages-and-the-visual-tree"></a>Definizione di pagine e la struttura ad albero visuale

In WPF viene usato il `Window` come l'elemento radice per qualsiasi elemento visivo di primo livello. Definisce un HWND nel mondo Windows per visualizzare le informazioni. È possibile creare e visualizzare le finestre tante contemporaneamente nel modo desiderato in WPF.

In xamarin. Forms, oggetto visivo di primo livello è sempre definito dalla piattaforma, ad esempio in iOS, è un `UIWindow`. Xamarin. Forms esegue il rendering è contenuto in queste rappresentazioni di piattaforma nativa utilizzando un `Page` classe. Ogni `Page` in xamarin. Forms rappresenta "pagina" univoca nell'applicazione, in cui solo uno è visibile alla volta.

Entrambi WPFs `Window` e xamarin. Forms `Page` includono un `Title` proprietà per influenzare il titolo visualizzato ed entrambi hanno un `Icon` proprietà per visualizzare un'icona specifica per la pagina (**nota** che il titolo e l'icona non sono sempre visibili in xamarin. Forms). Inoltre, è possibile modificare proprietà visual comuni su entrambi, ad esempio il colore di sfondo o l'immagine.

È tecnicamente possibile eseguire il rendering in due visualizzazioni separate piattaforma (ad esempio, definire due `UIWindow` oggetti e che il seconda esegua il rendering a un display esterno o AirPlay), richiede codice specifico della piattaforma a tale scopo e non è una funzionalità supportata direttamente Xamarin. Forms se stesso.

### <a name="views"></a>Visualizzazioni

La gerarchia per entrambe le versioni visual è simile. WPF è un po' più approfondita a causa del relativo supporto per i documenti WYSIWYG.

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

### <a name="view-lifcycle"></a>Visualizzazione Lifcycle

Xamarin. Forms è orientato principalmente su scenari per dispositivi mobili. Di conseguenza, le applicazioni sono _attivata_, _sospeso_, e _riattivato_ mentre l'utente interagisce con essi. Questa operazione è simile a fare clic sul lontani dal `Window` in un'applicazione WPF e vi sono un set di metodi ed eventi corrispondenti, è possibile eseguire l'override o hook per monitorare questo comportamento.

| Scopo | Metodo WPF | Xamarin. Forms (metodo) |
|--- |--- |--- |
|Attivazione iniziale|ctor + Window.OnLoaded|ctor + Page.OnStart|
|Visualizzato|Window.IsVisibleChanged|Page.Appearing|
|Hidden|Window.IsVisibleChanged|Page.Disapearing|
|Sospendere/perso lo stato attivo|Window.OnDeactivated|Page.OnSleep|
|Stato attivo/Taiwan attivato|Window.OnActivated|Page.OnResume|
|Chiuso|Window.OnClosing + Window.OnClosed|N/D|


Entrambi supporto nascondere o mostrare anche i controlli figlio, in WPF è una proprietà a tre stati `IsVisible` (visibile, nascosto e compresso). In xamarin. Forms, è sufficiente visibile o nascosto mediante il `IsVisible` proprietà.

### <a name="layout"></a>Layout

Layout di pagina si verifica nella stessa 2-sessione (misura/Disponi) che viene eseguito in WPF. È possibile effettuare l'hook del layout di pagina eseguendo l'override di metodi di seguito il xamarin. Forms `Page` classe:

| Metodo | Scopo |
|--- |--- |
|OnChildMeasureInvalidated|Dimensioni preferite dell'elemento figlio sono stato modificato.|
|OnSizeAllocated|Pagina è stata assegnata una larghezza/altezza.|
|Evento LayoutChanged|Layout/dimensioni della pagina è stato modificato.|

Non esiste alcun evento di layout globale che viene chiamato oggi stesso, né è disponibile una global `CompositionTarget.Rendering` trovare l'evento, ad esempio in WPF.

#### <a name="common-layout-properties"></a>Proprietà di layout comuni

WPF e xamarin. Forms supportano entrambi `Margin` spaziatura controllo intorno a un elemento e `Padding` controllo spaziatura _all'interno di_ un elemento. Inoltre, la maggior parte delle visualizzazioni layout xamarin. Forms dispongono di proprietà per controllare la spaziatura (ad esempio, riga o colonna).

Inoltre, la maggior parte degli elementi dispongono di proprietà per influire sul modo in cui si trovano nel contenitore padre:

| WPF | Xamarin.Forms | Scopo |
|--- |--- |--- |
|HorizontalAlignment|HorizontalOptions|Opzioni di sinistra/Center/destra/estensione|
|VerticalAlignment|VerticalOptions|Opzioni Top/Center/inferiore/estensione|

> [!NOTE]
> L'interpretazione di queste proprietà effettivo dipende dal contenitore padre.

#### <a name="layout-views"></a>Viste di layout

WPF e xamarin. Forms Usa controlli di layout per posizionare gli elementi figlio. Nella maggior parte dei casi, questi sono molto vicini in termini di funzionalità.

| WPF | Xamarin.Forms | Stile di layout |
|--- |--- |--- |
|StackPanel|StackLayout|Sovrapposizione infinito da sinistra a destra o alto in basso|
|Grid|Grid|Formato tabulare (righe e colonne)|
|DockPanel|N/D|Ancorato al bordo della finestra|
|Canvas|AbsoluteLayout|Pixel/Coordinate di posizionamento|
|WrapPanel|N/D|Stack di ritorno a capo|
|N/D|RelativeLayout|Basato su regole il posizionamento relativo|

> [!NOTE]
> Xamarin. Forms non supporta un `GridSplitter`.

Entrambe le piattaforme usano _le proprietà associate_ per ottimizzare gli elementi figlio.

### <a name="rendering"></a>Rendering

Il meccanismo per il rendering per WPF e xamarin. Forms è completamente diverso. In WPF, i controlli creati direttamente il rendering del contenuto in pixel sullo schermo. WPF gestisce due oggetti grafici (_alberi_) per rappresentare questo - i _albero logico_ rappresenta i controlli, come definito nel codice o XAML e il _struttura ad albero visuale_ rappresenta il rendering effettivo che si verifica nella schermata in cui viene eseguita direttamente tramite l'elemento visivo (tramite un metodo di disegno virtuale), o tramite un definite da XAML `ControlTemplate` che possono essere sostituiti o personalizzati. In genere, la struttura ad albero visuale è più complessa in quanto ciò comprende, ad esempio i bordi intorno a controlli, le etichette per contenuto implicito e così via. WPF include un set di API (`LogicalTreeHelper` e `VisualTreeHelper`) per esaminare questi due oggetti grafici.

In xamarin. Forms, i controlli è possibile definire una `Page` sono oggetti dati semplicemente semplice. Sono simili per la rappresentazione dell'albero logico, ma mai il rendering del contenuto in modo autonomo. Sono invece il _modello di dati_ che influenza il rendering degli elementi. Il rendering effettivo viene eseguito un [separare set di _renderer visual_ che viene eseguito il mapping per ogni tipo di controllo](~/xamarin-forms/app-fundamentals/custom-renderer/index.md). Tali renderer vengono registrate in ognuno dei progetti specifici della piattaforma da assembly xamarin. Forms specifico della piattaforma. È possibile visualizzare un elenco [qui](~/xamarin-forms/app-fundamentals/custom-renderer/renderers.md). Oltre a sostituire o estendere il renderer, xamarin. Forms offre inoltre supporto per [effetti](~/xamarin-forms/app-fundamentals/effects/index.md) che può essere usato per influenzare il rendering in base al plaform nativo.

#### <a name="the-logicalvisual-tree"></a>L'albero logico/Visual

Non vi è alcuna API esposta per esaminare l'albero logico in xamarin. Forms - ma è possibile usare la Reflection per ottenere le stesse informazioni. Ad esempio [Ecco un metodo che può enumerare elementi figlio logici](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Extensions/ElementExtensions.cs#L108) mediante reflection.

## <a name="graphics"></a>Grafica

Xamarin. Forms non include un sistema di grafica per le primitive di oltre un semplice rettangolo (`BoxView`). È possibile includere, ad esempio librerie di terze parti 3rd [SkiaSharp](~/graphics-games/skiasharp/index.md) per ottenere il disegno 2D multipiattaforma, o [UrhoSharp](~/graphics-games/urhosharp/index.md) per 3D.

## <a name="resources"></a>Risorse

WPF e xamarin. Forms dispongono entrambi il concetto di risorse e dizionari risorse. È possibile inserire qualsiasi tipo di oggetto in un `ResourceDictionary` con una chiave e quindi eseguire la ricerca con `{StaticResource}` per operazioni che non verranno modificata, o `{DynamicResource}` per operazioni che possono cambiare nel dizionario in fase di esecuzione. Le informazioni sull'utilizzo e meccanismi corrispondono a quelle con una differenza: xamarin. Forms che è necessario definire il `ResourceDictionary` da assegnare al `Resources` proprietà mentre WPF pre-creato uno e viene assegnato automaticamente.

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

Stili sono completamente supportati in xamarin. Forms e può essere utilizzato al tema gli elementi di xamarin. Forms che costituiscono l'interfaccia utente. Supportano l'ereditarietà di proprietà, eventi e dati, i trigger tramite `BasedOn`e le ricerche di risorse per i valori. Gli stili vengono applicati agli elementi un in modo esplicito un' tramite il `Style` proprietà o implicitamente dalla mancata specifica di una chiave di risorsa - esattamente come WPF.

### <a name="device-styles"></a>Stili di dispositivo

WPF offre un set di proprietà predefinite (archiviati come valori statici in un set di classi statiche, ad esempio `SystemColors`) che prevedono metriche sotto forma di valori e le chiavi di risorsa, i tipi di carattere e colori di sistema. Xamarin. Forms è simile, ma definisce un set di [dispositivo stili](~/xamarin-forms/user-interface/styles/device.md) per rappresentare le stesse operazioni. Questi stili sono forniti per il Framework anche e impostati sui valori in base all'ambiente di runtime (ad esempio accessibilità).

**WPF**

```xaml
<Label Text="Title" Foreground="{DynamicResource {x:Static SystemColors.DesktopBrushKey}}" />
```

**Xamarin.Forms**

```xaml
<Label Text="Title" Style="{DynamicResource TitleStyle}" />
```
