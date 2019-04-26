---
title: Panoramica dell'interfaccia utente di iOS 7
description: iOS 7 introduce una vasta gamma di modifiche all'interfaccia utente. Questo articolo evidenzia alcune delle modifiche più grandi, l'aspetto visivo dei controlli sia nelle API che supportano la nuova progettazione.
ms.prod: xamarin
ms.assetid: FADCEA7C-8968-42A1-9E9E-F4BBAB7BCF2C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 132265c27e1d1ba3b8f3fc8db10d7b3cfa746197
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61039129"
---
# <a name="ios-7-user-interface-overview"></a>Panoramica dell'interfaccia utente di iOS 7

_iOS 7 introduce una vasta gamma di modifiche all'interfaccia utente. Questo articolo evidenzia alcune delle modifiche più grandi, l'aspetto visivo dei controlli sia nelle API che supportano la nuova progettazione._

iOS 7 è incentrato sui contenuti su chrome. Elementi dell'interfaccia utente iOS 7 allontanare l'attenzione chrome rimuovendo gli attributi, ad esempio bordi estranei, barre di stato e le barre di navigazione, che riducono la quantità di spazio su schermo utilizzato nelle visualizzazioni di contenuto. In iOS 7, il contenuto è progettato per usare l'intero schermo.

iOS 7 introduce molte altre modifiche: colore viene usato per distinguere gli elementi dell'interfaccia utente, al posto di attributi, ad esempio i bordi di pulsante. Molti elementi, ad esempio le barre di spostamento e le barre di stato, ora sono sfocate e semitrasparenti o trasparenti, con visualizzazioni contenute prendendo l'area di sotto di essi. Il rendering di queste visualizzazioni del contenuto tramite le barre sfocate, comunica un senso profondità nell'interfaccia utente.

Questo articolo illustra alcune delle modifiche per gli elementi dell'interfaccia utente in iOS che 7, nonché varie API correlate per la nuova progettazione dell'interfaccia utente.

## <a name="view-and-control-changes"></a>Visualizzazione e le modifiche del controllo

Tutte le viste in UIKit rispettare il nuovo aspetto di iOS 7. Questa sezione evidenzia alcune delle modifiche a queste visualizzazioni, nonché le API correlate che sono stati modificati per supportare la nuova interfaccia utente.

### <a name="uibutton"></a>UIButton

I pulsanti creati dal `UIButton` classe sono ora di bordi, e non in background per impostazione predefinita, come illustrato di seguito:

 ![](ios7-ui-images/button.png "Esempio UIButton")

Il `UIButtonType.RoundedRect` stile di visualizzazione è stata deprecata. Se usato in iOS 7 `UIButtonType.RoundedRect` comporterà `UIButtonType.System` usate, con la conseguente lo stile del pulsante predefinito senza in background o bordi visibili, come illustrato in precedenza.

### <a name="uibarbuttonitem"></a>UIBarButtonItem

Simile a `UIButton`, pulsanti della barra della sono anche senza bordi, verrà utilizzato il nuovo `UIBarButtonItemStyle.Plain` stile illustrato di seguito:

 ![](ios7-ui-images/barbuttonplain.png "Esempio UIBarButtonItem")

Inoltre, il `UIBarButtonItemStyle.Bordered` stile di visualizzazione è stata deprecata. L'impostazione `UIBarButtonItemStyle.Bordered` in iOS 7 comporterà il `UIBarButtonItemStyle.Plain` applicare uno stile in uso.

Il `UIBarButtonItemStyle.Done` stile non è stato deprecato. Tuttavia, verrà creata anche un pulsante senza bordo, solo con uno stile di testo in grassetto, come illustrato:

 ![](ios7-ui-images/barbuttondone.png "Esempio UIBarButtonItem nello stile Done")

### <a name="uialertview"></a>UIAlertView

Oltre alla modifica apportata allo stile per l'aspetto iOS 7 nuovo, non è più viste avvisi supportano la personalizzazione tramite visualizzazione secondaria. Sebbene `UIAlertView` eredita da `UIView`, la chiamata `AddSubview` su un `UIAlertView` non ha alcun effetto. Si consideri il codice di esempio seguente:

```csharp
UIBarButtonItem button = new UIBarButtonItem ("Bar Button", UIBarButtonItemStyle.Plain, (s,e) =>
{
    UIAlertView alert = new UIAlertView ("Title", "Message", null, "Cancel", "OK");

    alert.AddSubview (new UIView () {
        Frame = new CGRect(50, 50,100, 100),
        BackgroundColor = UIColor.Green
    });

    alert.Show ();
});
```

Questo codice produce una visualizzazione avvisi standard, con la visualizzazione secondaria viene ignorata, come illustrato di seguito:

 ![](ios7-ui-images/alert.png "Esempio UIAlertView")
 
 Nota: UIAlertView è stata deprecata in iOS 8. Visualizza i [Controller di avvisi](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller) recipe sull'uso di una visualizzazione avvisi in iOS 8 e versioni successive.

### <a name="uisegmentedcontrol"></a>UISegmentedControl

Controlli segmentati in iOS 7 sono transparent e supportano colore tinta. Il colore tonalità viene usato per il colore del bordo e testo. Quando viene selezionato un segmento, il colore viene scambiato tra lo sfondo e il testo, con il colore tonalità utilizzato per evidenziare il segmento selezionato, come illustrato di seguito:

 ![](ios7-ui-images/segmentedcontrol.png "Esempio UISegmentedControl")

Inoltre, il `UISegmentedControlStyle` è stata deprecata in iOS 7.

### <a name="picker-views"></a>Selezione viste

L'API per le visualizzazioni di selezione è in gran parte invariato; Tuttavia, iOS 7 linee sullo stato a questo punto le viste di selezione devono essere presentate inline anziché come visualizzazioni di input animata da parte inferiore della schermata o tramite un nuovo controller il push nello stack del controller di spostamento, come in iOS precedenti versioni. Può essere visto nell'app del calendario di notifica:

 ![](ios7-ui-images/inlinepicker.png "Può essere visto nell'app del calendario di notifica")

### <a name="uisearchdisplaycontroller"></a>UISearchDisplayController

La barra di ricerca a questo punto è indicata tra parentesi la navigazione barra quando il `UISearchDisplayController.DisplaysSearchBarInNavigationBar` viene impostata su true. Se impostato su false: il valore predefinito: barra di spostamento viene nascosto quando viene visualizzato il controller di ricerca.

Lo screenshot seguente mostra la barra di ricerca all'interno di un `UISearchDisplayController`:

 ![](ios7-ui-images/searchbar.png "Esempio UISearchDisplayController")

### <a name="uitableview"></a>UITableView

Le API intorno `UITableView` sono principalmente non modificato; tuttavia, lo stile è drasticamente evoluto per rispettare la nuova progettazione dell'interfaccia utente. La gerarchia di visualizzazione interna è inoltre leggermente diversa. Questa modifica non riguarda la maggior parte delle App, ma si tratta di qualcosa da tenere presenti.

#### <a name="grouped-table-style"></a>Stile tabella raggruppata

Lo stile raggruppato modificato è aggiornata, con il contenuto a questo punto si estende ai bordi della schermata come mostrato di seguito:

 ![](ios7-ui-images/table1.png "Stile di esempio tabella raggruppata")

#### <a name="separatorinset"></a>SeparatorInset

Separatori di riga possono a questo punto essere rientrati impostando il `UITableVIewCell.SeparatorInset` proprietà. Ad esempio, il codice seguente sarebbe consente di impostare un rientro celle comprese tra il bordo sinistro:

```csharp
cell.SeparatorInset = new UIEdgeInsets (0, 50, 0, 0);
```

Questo codice produce nella visualizzazione della tabella con le celle con rientro, come illustrato di seguito:

 ![](ios7-ui-images/separatorinset.png "Esempio UITableView SeparatorInset")

#### <a name="table-button-styles"></a>Stili dei pulsanti di tabella

I vari pulsanti utilizzati nelle visualizzazioni di tabella sono stati tutti modificati. Lo screenshot seguente presenta una visualizzazione tabella in modalità di modifica:

 ![](ios7-ui-images/table2.png "Questa schermata si presenta una visualizzazione tabella in modalità di modifica")

### <a name="additional-control-changes"></a>Modifiche aggiuntive del controllo

Altri controlli UIKit sono stati modificati, inclusi i dispositivi di scorrimento, commutatori e i gestori di istruzioni. Queste modifiche sono esclusivamente visual. Per altre informazioni, fare riferimento a Apple [Guida alla transizione dell'interfaccia utente di iOS 7](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/TransitionGuide/index.html).

## <a name="general-user-interface-changes"></a>Modifiche generali dell'interfaccia utente

Oltre alle modifiche in UIKit, iOS 7 introduce un'ampia gamma di modifiche visive per l'interfaccia utente, tra cui:

-  Contenuto a schermo intero
-  Aspetto barra
-  Colore tinta

<a name="fullscreen" />

### <a name="full-screen-content"></a>Contenuto a schermo intero

iOS 7 è progettato per consentire alle applicazioni di sfruttare l'intera schermata. I controller di visualizzazione vengono ora visualizzati sovrapposti da una barra di stato e barra di spostamento, se presente, anziché comparire sotto le barre di stato e la navigazione.

Durante la preparazione dell'applicazione per iOS 7, è possibile un Riallineamento con le visualizzazioni secondarie usando visivamente *Interface Builder* o nella *Xamarin iOS Designer*. È anche possibile usare una delle nuove API per agevolare la gestione a livello di codice il contenuto a schermo intero. Queste API sono state introdotte sotto.

#### <a name="toplayoutguide-and-bottomlayoutguide"></a>TopLayoutGuide e BottomLayoutGuide

 `TopLayoutGuide` e `BottomLayoutGuide` fungono da riferimento per cui le visualizzazioni devono iniziare o terminare, in modo che il contenuto non si sovrapposta da un semitrasparente `UIKit` barra, come nell'esempio seguente:

 [![](ios7-ui-images/clipped.png "Contenuto di esempio non sovrapposto con una barra UIKit semitrasparente")](ios7-ui-images/clipped.png#lightbox)

Queste API possono essere utilizzate per calcolare lo spostamento della visualizzazione dalla parte superiore o inferiore della schermata e modificare di conseguenza il posizionamento del contenuto:

```csharp
public override void ViewDidLayoutSubviews ()
{
    base.ViewDidLayoutSubviews ();

    if (UIDevice.CurrentDevice.CheckSystemVersion (7, 0)) { 
        nfloat displacement_y = this.TopLayoutGuide.Length;

        //load subviews with displacement
    }

}
```

È possibile usare il valore calcolato in precedenza per impostare il `ImageView`di spostamento nella parte superiore della schermata, in modo che l'intera immagine è visibile:

 [![](ios7-ui-images/good2.png "Spostamento di esempio ImageViews dalla parte superiore della schermata")](ios7-ui-images/good2.png#lightbox)

Vedere le [ImageViewer](https://developer.xamarin.com/samples/mobile/iOS7-ui-updates) per un esempio funzionante.

Il valore di spostamento viene generato in modo dinamico dopo la visualizzazione è stato aggiunto alla gerarchia, pertanto il tentativo di leggere `TopLayoutGuide` e `BottomLayoutGuide` i valori in `ViewDidLoad` restituirà 0. Calcola il valore dopo la visualizzazione non è caricato - ad esempio, nel `ViewDidLayoutSubviews`.

> [!IMPORTANT]
> `TopLayoutGuide` e `BottomLayoutGuide` sono deprecati in iOS 11 con il nuovo layout area sicura. Apple hanno indicato che tramite l'area sicura sia compatibile con iOS versione precedente a iOS 11. Per altre informazioni, vedere la [aggiornamento dell'app per iOS 11](~/ios/platform/introduction-to-ios11/updating-your-app/visual-design.md#fullscreen) Guida.

#### <a name="edgesforextendedlayout"></a>EdgesForExtendedLayout

Questa API consente di specificare quali bordi di una vista devono essere esteso a schermo intero, indipendentemente da barra traslucidità. In iOS 7, le barre di spostamento e le barre degli strumenti vengono visualizzati disposti sopra visualizzazione del controller: a differenza di iOS precedenti versioni, in cui non hanno lo stesso spazio. L'applicazione di foto di iOS 7 viene illustrata l'impostazione predefinita `UIViewController.EdgesForExtendedLayout` valore, `UIRectEdge.All`. Questa impostazione vengono compilati quattro contorni nella visualizzazione contenuto, creare l'effetto sovrapposto e a schermo intero:

 [![](ios7-ui-images/photos.png "Esempio EdgesForExtendedLayout")](ios7-ui-images/photos.png#lightbox)

Se si tocca l'immagine di rimuove le barre e Mostra l'immagine a schermo:

 [![](ios7-ui-images/photos2.png "EdgesForExtendedLayout con le barre rimosso")](ios7-ui-images/photos2.png#lightbox)

Poiché il contenuto a schermo intero è l'impostazione predefinita, le applicazioni configurate per iOS 6 avrà parte della vista ritagliata, come illustrato nello screenshot seguente:

 [![](ios7-ui-images/clipped.png "Le app configurate per iOS 6 avrà parte della vista ritagliata, come in questo screenshot")](ios7-ui-images/clipped.png#lightbox)

Modifica il `UIViewController.EdgesForExtendedLayout` proprietà viene modificata per questo comportamento. È possibile specificare che la vista non compilare tutti i bordi, in modo che la vista eviterà di visualizzare il contenuto nello spazio occupato dall'esplorazione o barre degli strumenti (a qualsiasi orientamento):

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (7, 0)) { 
    this.EdgesForExtendedLayout = UIRectEdge.None;
}
```

Nell'app, si vedrà la vista viene riposizionata anche in questo caso, in modo che l'intera immagine è visibile:

 [![](ios7-ui-images/good.png "Esempio con l'intera immagine visibile")](ios7-ui-images/good.png#lightbox)

Si noti che mentre gli effetti del `TopLayoutGuide/BottomLayoutGuide` e `EdgesForExtendedLayout` API sono simili, sono utilizzati per riempire diversi obiettivi. Modifica il `EdgesForExtendedLayout` impostazione dal valore predefinito potrebbe essere risolto viste ritagliate in applicazioni progettate per iOS 6, ma una progettazione ottimale iOS 7 deve rispettare l'estetica a schermo intero e fornire a schermo intero esperienza di visualizzazione, basarsi su `TopLayoutGuide` e`BottomLayoutGuide`per posizionare correttamente il contenuto destinato a essere modificati in una posizione pratica per l'utente.

Vedere le [ImageViewer](https://developer.xamarin.com/samples/mobile/iOS7-ui-updates) per un esempio funzionante.

### <a name="status-and-navigation-bars"></a>Lo stato e le barre di spostamento

La barra di stato e le barre di spostamento vengano eseguito il rendering con trasparenza. Barre di stato sono trasparenti, mentre le barre degli strumenti e le barre di spostamento sono semitrasparente e sfocato per indicare la sensazione di profondità nell'interfaccia utente. Lo screenshot seguente mostra questa Sfocatura e trasparenza, in cui viene illustrato il colore di sfondo blu della visualizzazione di raccolta tramite le barre di navigazione sia stato, fornendo loro un aspetto blu chiaro:

 ![](ios7-ui-images/transparent-navbar.png "Esempio di stato e navigazione della barra di sfocatura")

#### <a name="status-bar-styles"></a>Stili barra di stato

Oltre a Sfocatura e trasparenza, il primo piano di una barra di stato può essere chiaro o scuro (scura è l'impostazione predefinita). Lo stile della barra di stato può essere impostato dal controller di visualizzazione. Un controller di visualizzazione può inoltre specificare se la barra di stato è nascosta o visualizzata.

Ad esempio, il codice seguente esegue l'override di `PreferredStatusBarStyle` metodo di un controller di visualizzazione per visualizzare un chiaro in primo piano la barra di stato:

```csharp
public override UIStatusBarStyle PreferredStatusBarStyle ()
{
    return UIStatusBarStyle.LightContent;
}
```

In questo modo, la barra di stato venga visualizzato come indicato di seguito:

 ![](ios7-ui-images/light-status-bar.png "Barra di stato di esempio")

Per nascondere la barra di stato dal codice del controller di visualizzazione, eseguire l'override `PrefersStatusBarHidden`, come illustrato di seguito:

```csharp
public override bool PrefersStatusBarHidden ()
{
    return true;
}
```

Ciò consente di nascondere la barra di stato:

 ![](ios7-ui-images/status-bar-hidden.png "Barra di stato nascosta")

### <a name="tint-color"></a>Colore tinta

I pulsanti sono ora visualizzati come testo senza chrome. Il colore del testo può essere controllato usando le nuove `TintColor` proprietà `UIView`. L'impostazione di `TintColor` il colore viene applicato alla gerarchia di intera vista per la visualizzazione che lo imposta. Per applicare una `TintColor`all'interno di un'app impostata per il `Window`. È anche possibile rilevare quando il colore tonalità modificato tramite la `UIView.TintColorDidChange` (metodo).

Ad esempio, lo screenshot seguente mostra l'effetto della modifica il colore tonalità sulla vista del controller di spostamento a purple:

 ![](ios7-ui-images/tint-color.png "Colore viola su una vista di controller di spostamento")

Il colore tonalità è applicabile alle immagini anche quando la `RenderingMode` è impostata su `UIImageRenderingMode.AlwaysTemplate`.

> [!IMPORTANT]
> Colore tinta non può essere impostate tramite `UIAppearance`.


### <a name="dynamic-type"></a>Tipo dinamico

In iOS 7, l'utente può specificare dimensioni del testo nelle impostazioni di sistema. Con il tipo dinamico, il tipo di carattere viene regolata in modo dinamico per l'aspetto indipendentemente dalle dimensioni. `UIFont.PreferredFontForTextStyle` deve essere utilizzato per ottenere un tipo di carattere che è ottimizzato per la dimensione controllata dall'utente.

## <a name="summary"></a>Riepilogo

Questo articolo illustra le modifiche apportate a elementi dell'interfaccia utente iOS 7. Esamina alcune delle modifiche apportate alle visualizzazioni e ai controlli in UIKit, evidenziando sia l'oggetto visivo cambia, oltre alle modifiche alle API correlate. Infine, introduce nuove API per lavorare con contenuto a schermo intero, il nuovo supporto colore tinta e tipo dinamico.

## <a name="related-links"></a>Collegamenti correlati

- [ImageViewer (esempio)](https://developer.xamarin.com/samples/monotouch/iOS7-ui-updates)
