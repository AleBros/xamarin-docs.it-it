---
title: iOS 7 panoramica dell'interfaccia utente
description: iOS 7 introduce un insieme di modifiche all'interfaccia utente. In questo articolo sono evidenziate alcune delle modifiche più grande, l'aspetto visivo dei controlli sia nelle API che supportano la nuova progettazione.
ms.prod: xamarin
ms.assetid: FADCEA7C-8968-42A1-9E9E-F4BBAB7BCF2C
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/21/2017
ms.openlocfilehash: 3f5ea8abd41e718f9ac947c5acb290dffe400ddd
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="ios-7-user-interface-overview"></a>iOS 7 panoramica dell'interfaccia utente

_iOS 7 introduce un insieme di modifiche all'interfaccia utente. In questo articolo sono evidenziate alcune delle modifiche più grande, l'aspetto visivo dei controlli sia nelle API che supportano la nuova progettazione._

iOS 7 è incentrata sul contenuto su chrome. Elementi dell'interfaccia utente in iOS 7 rendere meno accentuato chrome rimuovendo gli attributi, ad esempio estranei bordi, le barre di stato e barre di navigazione, che riducono la quantità di spazio su schermo utilizzato dalle visualizzazioni di contenuto. In iOS 7, il contenuto è progettato per utilizzare l'intero schermo.

iOS 7 introduce diverse altre modifiche: colore utilizzato per distinguere gli elementi dell'interfaccia utente, anziché gli attributi, ad esempio i bordi di pulsante. Molti elementi, come barre di navigazione e barre di stato, sono ora sfocate e semitrasparenti o trasparenti, con visualizzazioni contenute richiede l'area di sotto di essi. Eseguire il rendering queste viste contenute tramite le barre sfocate, trasmettere un impressione di profondità nell'interfaccia utente.

Questo articolo vengono illustrate alcune delle modifiche agli elementi di interfaccia utente in iOS che 7, nonché varie API correlate per la nuova progettazione dell'interfaccia utente.

## <a name="view-and-control-changes"></a>Visualizzazione e le modifiche del controllo

Tutte le viste in UIKit rispettare il nuovo aspetto di iOS 7. In questa sezione sono evidenziate alcune delle modifiche a queste visualizzazioni, come le API correlate che sono stati modificati per supportare la nuova interfaccia utente.

### <a name="uibutton"></a>UIButton

Pulsanti creati dalla `UIButton` classe sono ora di bordi, e senza conoscenze di base per impostazione predefinita, come illustrato di seguito:

 ![](ios7-ui-images/button.png "Esempio UIButton")

Il `UIButtonType.RoundedRect` stile è stato deprecato. Se usato con iOS 7, `UIButtonType.RoundedRect` comporterà `UIButtonType.System` che viene utilizzato, produce lo stile del pulsante predefinito senza sfondo o bordi visibili, come illustrato in precedenza.

### <a name="uibarbuttonitem"></a>UIBarButtonItem

Simile a `UIButton`, pulsanti della barra sono anche senza bordi, verrà utilizzato il nuovo `UIBarButtonItemStyle.Plain` stile illustrato di seguito:

 ![](ios7-ui-images/barbuttonplain.png "Esempio UIBarButtonItem")

Inoltre, il `UIBarButtonItemStyle.Bordered` stile è stato deprecato. Impostazione `UIBarButtonItemStyle.Bordered` in iOS 7 comporta il `UIBarButtonItemStyle.Plain` stile in uso.

Il `UIBarButtonItemStyle.Done` stile non è stato deprecato. Tuttavia, creerà anche un pulsante senza bordo, solo con uno stile di testo in grassetto, come illustrato:

 ![](ios7-ui-images/barbuttondone.png "Esempio UIBarButtonItem nello stile completato")

### <a name="uialertview"></a>UIAlertView

Oltre alla modifica dello stile per il nuovo iOS 7 e l'aspetto, viste avvisi non supportano la personalizzazione tramite secondaria. Anche se `UIAlertView` eredita da `UIView`, la chiamata `AddSubview` su un `UIAlertView` non ha alcun effetto. Si consideri il codice di esempio seguente:

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

Ciò produce una visualizzazione avvisi standard, con la visualizzazione secondaria viene ignorata, come illustrato di seguito:

 ![](ios7-ui-images/alert.png "Esempio UIAlertView")
 
 Nota: UIAlertView è stata deprecata in iOS 8. Visualizzazione di [Controller avviso](https://developer.xamarin.com/recipes/ios/standard_controls/alertcontroller/) recipe sull'utilizzo di una vista degli avvisi in iOS 8 e versioni successive.

### <a name="uisegmentedcontrol"></a>UISegmentedControl

Controlli segmentati in iOS 7 sono transparent e supportano il colore tonalità. Il colore tinta viene usato per il colore del testo e il bordo. Quando un segmento è selezionato, il colore viene scambiato tra lo sfondo e il testo, con il colore tonalità usato per evidenziare il segmento selezionato, come illustrato di seguito:

 ![](ios7-ui-images/segmentedcontrol.png "Esempio UISegmentedControl")

Inoltre, il `UISegmentedControlStyle` è stato deprecato in iOS 7.

### <a name="picker-views"></a>Selezione viste

L'API per le visualizzazioni di selezione è sostanzialmente invariata; Tuttavia, linee guida iOS 7 ora stato viste di selezione devono essere presentate inline anziché come inpue viste animata da parte inferiore della schermata o tramite un nuovo controller di inserito nello stack di un controller navigazione, come iOS precedenti versioni. Può essere visto nell'app del calendario di sistema:

 ![](ios7-ui-images/inlinepicker.png "Può essere visto nell'app del calendario di sistema")

### <a name="uisearchdisplaycontroller"></a>UISearchDisplayController

La barra di ricerca viene visualizzata all'interno di spostamento barra quando il `UISearchDisplayController.DisplaysSearchBarInNavigationBar` proprietà è impostata su true. Se impostato su false, il valore predefinito: barra di spostamento viene nascosto quando viene visualizzato il controller di ricerca.

La schermata seguente mostra la barra di ricerca all'interno di un `UISearchDisplayController`:

 ![](ios7-ui-images/searchbar.png "Esempio UISearchDisplayController")

### <a name="uitableview"></a>UITableView

L'API `UITableView` sono essenzialmente invariata; tuttavia, lo stile è stato modificato in modo significativo per rispettare la nuova progettazione dell'interfaccia utente. La gerarchia della visualizzazione interno è leggermente diversa. Questa modifica non influiscono sulla maggior parte delle App, ma è opportuno tenere in considerazione.

#### <a name="grouped-table-style"></a>Stile tabella raggruppata

Lo stile raggruppato modificato è aggiornato, con il contenuto a questo punto l'estensione per i bordi della schermata, come illustrato di seguito:

 ![](ios7-ui-images/table1.png "Stile di esempio tabella raggruppata")

#### <a name="separatorinset"></a>SeparatorInset

Separatori di riga possono ora essere rientrati impostando il `UITableVIewCell.SeparatorInset` proprietà. Ad esempio, il codice seguente sarebbe utilizzabile per il rientro dal bordo sinistro celle:

```csharp
cell.SeparatorInset = new UIEdgeInsets (0, 50, 0, 0);
```

Nella visualizzazione della tabella con celle rientrate viene prodotto come illustrato di seguito:

 ![](ios7-ui-images/separatorinset.png "Esempio UITableView SeparatorInset")

#### <a name="table-button-styles"></a>Stili dei pulsanti di tabella

I vari pulsanti utilizzati nelle viste di tabella sono tutte modificate. Nella schermata seguente presenta una visualizzazione tabella in modalità di modifica:

 ![](ios7-ui-images/table2.png "Questa schermata Visualizza una tabella in modalità di modifica")

### <a name="additional-control-changes"></a>Modifiche aggiuntive per controllare

Altri controlli UIKit sono stati modificati, inclusi i dispositivi di scorrimento, commutatori e i gestori di istruzioni. Queste modifiche sono puramente visual. Per ulteriori informazioni, vedere Apple [Guida dell'interfaccia utente transizione iOS 7](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/TransitionGuide/index.html).

## <a name="general-user-interface-changes"></a>Modifiche generali dell'interfaccia utente

Oltre alle modifiche apportate UIKit, iOS 7 introduce diverse modifiche visive per l'interfaccia utente, tra cui:

-  Contenuto a schermo intero
-  Aspetto barra
-  Colore tonalità

<a name="fullscreen" />

### <a name="full-screen-content"></a>Contenuto a schermo intero

iOS 7 è progettato per consentire applicazioni di sfruttare l'intero schermo. Visualizzazione controller appariranno sovrapposte da una barra di stato e la barra di spostamento - se presente, anziché visualizzata sotto le barre di stato e la navigazione.

Durante la preparazione dell'applicazione per iOS 7, è possibile riallineare sottoviste in modo visivo mediante *interfaccia generatore* o *Xamarin iOS progettazione*. È possibile utilizzare una delle nuove API per agevolare la gestione a livello di codice contenuto a schermo intero. Queste API vengono illustrate di seguito.

#### <a name="toplayoutguide-and-bottomlayoutguide"></a>TopLayoutGuide e BottomLayoutGuide

 `TopLayoutGuide` e `BottomLayoutGuide` fungere da riferimento per cui viste devono iniziare o terminare, in modo che il contenuto non si sovrapposta da un semitrasparente `UIKit` barra, come nell'esempio seguente:

 [![](ios7-ui-images/clipped.png "Esempio di contenuto non sovrapposta da una barra UIKit semitrasparente")](ios7-ui-images/clipped.png#lightbox)

Queste API possono essere usate per calcolare lo spostamento di una visualizzazione dalla parte superiore o inferiore della schermata e modificare di conseguenza il posizionamento del contenuto:

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

È possibile utilizzare il valore calcolato in precedenza per impostare il nostro `ImageView`di spostamento dalla parte superiore dello schermo, per rendere visibile l'intera immagine:

 [![](ios7-ui-images/good2.png "Spostamento di esempio ImageViews dalla parte superiore dello schermo")](ios7-ui-images/good2.png#lightbox)

Consultare la [ImageViewer](https://developer.xamarin.com/samples/mobile/iOS7-ui-updates) per un esempio funzionante.

Il valore di spostamento viene generato in modo dinamico dopo la visualizzazione è stato aggiunto alla gerarchia, pertanto il tentativo di lettura `TopLayoutGuide` e `BottomLayoutGuide` valori `ViewDidLoad` restituirà 0. Calcolare il valore dopo la visualizzazione è caricate - ad esempio, il `ViewDidLayoutSubviews`.

> [!IMPORTANT]
> `TopLayoutGuide` e `BottomLayoutGuide` sono deprecati in iOS 11 a favore il nuovo layout area di sicurezza. Apple hanno indicato che l'utilizzo di area di sicurezza è compatibile con la versione di iOS precedenti a iOS 11. Per ulteriori informazioni, vedere il [l'aggiornamento dell'app per iOS 11](~/ios/platform/introduction-to-ios11/updating-your-app/visual-design.md#fullscreen) Guida.

#### <a name="edgesforextendedlayout"></a>EdgesForExtendedLayout

Questa API specifica quali bordi di una vista devono essere esteso a schermo intero, indipendentemente dal valore barra traslucidità. In iOS 7, barre di navigazione e barre degli strumenti vengono visualizzati disposti sopra la visualizzazione del controller: a differenza di iOS precedenti versioni, in cui non è stata occupano lo stesso spazio. L'applicazione di foto di iOS 7 è illustrato il valore predefinito `UIViewController.EdgesForExtendedLayout` valore `UIRectEdge.All`. Questa impostazione viene compilato tutti e quattro i bordi nella visualizzazione contenuto, creare l'effetto sovrapposto e schermo intero:

 [![](ios7-ui-images/photos.png "Esempio EdgesForExtendedLayout")](ios7-ui-images/photos.png#lightbox)

Toccare l'immagine rimuove le barre e Mostra l'immagine a schermo intero:

 [![](ios7-ui-images/photos2.png "EdgesForExtendedLayout con le barre rimosso")](ios7-ui-images/photos2.png#lightbox)

Poiché il contenuto a schermo intero è l'impostazione predefinita, le applicazioni configurate per iOS 6 avrà parte della vista troncata, come illustrato nella schermata seguente:

 [![](ios7-ui-images/clipped.png "Le applicazioni configurate per iOS 6 avrà una parte della vista troncata, come in questa schermata")](ios7-ui-images/clipped.png#lightbox)

Modifica il `UIViewController.EdgesForExtendedLayout` proprietà regola per questo comportamento. È possibile specificare che la vista non riempire tutti i bordi, la vista verrà evitare la visualizzazione di contenuto nello spazio occupato da navigazione o barre degli strumenti (in ogni posizione):

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (7, 0)) { 
    this.EdgesForExtendedLayout = UIRectEdge.None;
}
```

Nella nostra app vedremo che nuovamente la vista viene riposizionato in riferimento, per rendere visibile l'intera immagine:

 [![](ios7-ui-images/good.png "Esempio con l'intera immagine visibile")](ios7-ui-images/good.png#lightbox)

Si noti che mentre gli effetti del `TopLayoutGuide/BottomLayoutGuide` e `EdgesForExtendedLayout` API sono simili, sono disponibili solo per compilare i vari obiettivi. Modifica il `EdgesForExtendedLayout` impostazione il valore predefinito può risolvere viste ritagliate in applicazioni progettate per iOS 6, ma una progettazione ottimale iOS 7 è necessario rispettare l'estetici a schermo intero e fornire a schermo intero esperienza di visualizzazione, basarsi su `TopLayoutGuide` e `BottomLayoutGuide`per posizionare correttamente il contenuto è adatto per essere modificato in una posizione comoda per l'utente.

Consultare la [ImageViewer](https://developer.xamarin.com/samples/mobile/iOS7-ui-updates) per un esempio funzionante.

### <a name="status-and-navigation-bars"></a>Lo stato e barre di navigazione

Barra di stato e barre di navigazione rendering vengono eseguite con la trasparenza. Barre di stato sono trasparenti, mentre le barre degli strumenti e barre di navigazione sono semitrasparente e sfocato per trasmettere l'impressione di profondità nell'interfaccia utente. La schermata seguente mostra l'effetto sfumatura e trasparenza, in cui viene illustrato il colore di sfondo blu della visualizzazione di raccolta tramite barre lo stato e di navigazione, assegnare loro un aspetto blu chiaro:

 ![](ios7-ui-images/transparent-navbar.png "Esempio di stato e navigazione della barra di sfocatura")

#### <a name="status-bar-styles"></a>Stili barra di stato

Insieme di sfocatura e trasparenza, il primo piano della barra di stato può essere chiaro o scuro (scuro è il valore predefinito). Lo stile della barra di stato può essere impostato dal controller di visualizzazione. Un controller di visualizzazione può inoltre specificare se la barra di stato è nascosta o visualizzata.

Ad esempio, il codice seguente esegue l'override di `PreferredStatusBarStyle` metodo di un controller di visualizzazione per visualizzare la barra di stato con un leggero in primo piano:

```csharp
public override UIStatusBarStyle PreferredStatusBarStyle ()
{
    return UIStatusBarStyle.LightContent;
}
```

In questo modo, la barra di stato vengono visualizzati come indicato di seguito:

 ![](ios7-ui-images/light-status-bar.png "Barra di stato di esempio")

Per nascondere la barra di stato dal codice del controller di visualizzazione, eseguire l'override `PrefersStatusBarHidden`, come illustrato di seguito:

```csharp
public override bool PrefersStatusBarHidden ()
{
    return true;
}
```

Consente di nascondere la barra di stato:

 ![](ios7-ui-images/status-bar-hidden.png "Barra di stato nascosto")

### <a name="tint-color"></a>Colore tonalità

I pulsanti vengono ora visualizzati come testo senza chrome. Il colore del testo può essere controllato utilizzando il nuovo `TintColor` proprietà `UIView`. L'impostazione di `TintColor` applica la gerarchia della visualizzazione intero per la visualizzazione che imposta il colore. Per applicare un `TintColor`in un'app, impostata per il `Window`. È inoltre possibile rilevare quando cambia il colore tinta tramite il `UIView.TintColorDidChange` metodo.

Ad esempio, nella schermata seguente mostra l'effetto della modifica il colore tinta sulla vista del controller una navigazione su viola:

 ![](ios7-ui-images/tint-color.png "Colore viola una vista di navigazione controller")

Il colore tinta può essere applicato a immagini anche quando il `RenderingMode` è impostato su `UIImageRenderingMode.AlwaysTemplate`.

> [!IMPORTANT]
> Colore tonalità non può essere impostate tramite `UIAppearance`.


### <a name="dynamic-type"></a>Tipo dinamico

In iOS 7, l'utente può specificare le dimensioni di testo in impostazioni di sistema. Con il tipo dinamico, il tipo di carattere viene regolato in modo dinamico visualizzate correttamente indipendentemente dalle dimensioni. `UIFont.PreferredFontForTextStyle` deve essere utilizzato per ottenere un tipo di carattere che è ottimizzato per le dimensioni controllata dall'utente.

## <a name="summary"></a>Riepilogo

Questo articolo vengono illustrate le modifiche agli elementi di interfaccia utente in iOS 7. Esamina alcune delle modifiche apportate alle visualizzazioni e i controlli in UIKit, evidenziazione entrambe le modifiche di visual nonché modifiche per le API correlate. Infine, introduce nuove API per funzionare con contenuto a schermo intero, il nuovo supporto per colore tinta e tipo dinamico.

## <a name="related-links"></a>Collegamenti correlati

- [ImageViewer (esempio)](https://developer.xamarin.com/samples/monotouch/iOS7-ui-updates)
