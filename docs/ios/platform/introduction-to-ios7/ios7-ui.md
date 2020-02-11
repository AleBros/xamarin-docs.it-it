---
title: Panoramica dell'interfaccia utente di iOS 7
description: iOS 7 introduce una pletora di modifiche all'interfaccia utente. In questo articolo vengono evidenziate alcune delle modifiche più grandi, sia nell'aspetto visivo dei controlli che nelle API che supportano la nuova progettazione.
ms.prod: xamarin
ms.assetid: FADCEA7C-8968-42A1-9E9E-F4BBAB7BCF2C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/21/2017
ms.openlocfilehash: 4731be58c1fadae0bba6768570ecfd181b071dd2
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031856"
---
# <a name="ios-7-user-interface-overview"></a>Panoramica dell'interfaccia utente di iOS 7

_iOS 7 introduce una pletora di modifiche all'interfaccia utente. In questo articolo vengono evidenziate alcune delle modifiche più grandi, sia nell'aspetto visivo dei controlli che nelle API che supportano la nuova progettazione._

iOS 7 si concentra sul contenuto su Chrome. Gli elementi dell'interfaccia utente in iOS 7 deaccentuano il Chrome rimuovendo gli attributi, ad esempio bordi estranei, barre di stato e barre di spostamento, che riducono la quantità di spazio dello schermo usato dalle visualizzazioni contenuto. In iOS 7, il contenuto è progettato per l'uso dell'intera schermata.

iOS 7 introduce diverse altre modifiche: il colore viene usato per distinguere gli elementi dell'interfaccia utente, invece degli attributi, ad esempio i bordi dei pulsanti. Molti elementi, ad esempio barre di spostamento e barre di stato, sono ora sfocati e traslucidi o trasparenti, con visualizzazioni contenuto che occupano l'area sottostante. Queste visualizzazioni contenuto eseguono il rendering attraverso le barre sfocate, comunicando una sensazione di profondità nell'interfaccia utente.

Questo articolo illustra alcune delle modifiche apportate agli elementi dell'interfaccia utente in iOS 7, oltre a diverse API correlate alla nuova progettazione dell'interfaccia utente.

## <a name="view-and-control-changes"></a>Visualizzare e controllare le modifiche

Tutte le visualizzazioni in UIKit sono conformi al nuovo aspetto di iOS 7. In questa sezione vengono evidenziate alcune delle modifiche apportate a queste visualizzazioni, nonché le API correlate che sono state modificate per supportare la nuova interfaccia utente.

### <a name="uibutton"></a>UIButton

I pulsanti creati dalla classe `UIButton` sono ora senza bordi, senza sfondo per impostazione predefinita, come illustrato di seguito:

 ![](ios7-ui-images/button.png "Sample UIButton")

Lo stile del `UIButtonType.RoundedRect` è stato deprecato. Se usato in iOS 7, `UIButtonType.RoundedRect` comporterà l'uso di `UIButtonType.System`, che produce lo stile del pulsante predefinito senza bordi di sfondo o visibile, come illustrato in precedenza.

### <a name="uibarbuttonitem"></a>UIBarButtonItem

Analogamente a `UIButton`, i pulsanti della barra sono anche senza bordi, per impostazione predefinita il nuovo stile `UIBarButtonItemStyle.Plain` illustrato di seguito:

 ![](ios7-ui-images/barbuttonplain.png "Sample UIBarButtonItem")

Inoltre, lo stile del `UIBarButtonItemStyle.Bordered` è stato deprecato. L'impostazione `UIBarButtonItemStyle.Bordered` in iOS 7 comporterà l'uso dello stile `UIBarButtonItemStyle.Plain`.

Lo stile del `UIBarButtonItemStyle.Done` non è stato deprecato. Viene tuttavia creato anche un pulsante senza bordi, solo con uno stile di testo in grassetto, come illustrato:

 ![](ios7-ui-images/barbuttondone.png "Sample UIBarButtonItem in the Done style")

### <a name="uialertview"></a>UIAlertView

Oltre alla modifica dello stile per il nuovo aspetto di iOS 7, le visualizzazioni degli avvisi non supportano più la personalizzazione tramite la visualizzazione. Anche se `UIAlertView` eredita da `UIView`, la chiamata di `AddSubview` su un `UIAlertView` non ha alcun effetto. Si consideri il codice di esempio seguente:

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

In questo modo viene generata una visualizzazione avvisi standard, in cui la visualizzazione della Sottovisualizzazione viene ignorata, come illustrato di seguito:

 ![](ios7-ui-images/alert.png "Sample UIAlertView")

 Nota: UIAlertView è stato deprecato in iOS 8. Per visualizzare la ricetta del [controller avvisi](https://github.com/xamarin/recipes/tree/master/Recipes/ios/standard_controls/alertcontroller) , usare una visualizzazione avvisi in iOS 8 e versioni successive.

### <a name="uisegmentedcontrol"></a>UISegmentedControl

I controlli segmentati in iOS 7 sono trasparenti e supportano il colore tinta. Il colore della tinta viene utilizzato per il colore del testo e del bordo. Quando si seleziona un segmento, il colore viene scambiato tra lo sfondo e il testo, con il colore di tinta utilizzato per evidenziare il segmento selezionato, come illustrato di seguito:

 ![](ios7-ui-images/segmentedcontrol.png "Sample UISegmentedControl")

Inoltre, il `UISegmentedControlStyle` è stato deprecato in iOS 7.

### <a name="picker-views"></a>Selezione visualizzazioni

L'API per le visualizzazioni di selezione è sostanzialmente invariata. Tuttavia, le linee guida di progettazione di iOS 7 ora le viste di selezione stato devono essere presentate inline anziché come viste di input animate dalla parte inferiore dello schermo o tramite un nuovo controller inserito nello stack di un controller di spostamento, come nelle versioni precedenti di iOS. Questa operazione può essere visualizzata nell'app calendario di sistema:

 ![](ios7-ui-images/inlinepicker.png "This can be seen in the system calendar app")

### <a name="uisearchdisplaycontroller"></a>UISearchDisplayController

La barra di ricerca viene ora visualizzata all'interno della barra di spostamento quando la proprietà `UISearchDisplayController.DisplaysSearchBarInNavigationBar` è impostata su true. Se è impostato su false, l'impostazione predefinita è la barra di spostamento nascosta quando viene visualizzato il controller di ricerca.

Lo screenshot seguente mostra la barra di ricerca all'interno di un `UISearchDisplayController`:

 ![](ios7-ui-images/searchbar.png "Sample UISearchDisplayController")

### <a name="uitableview"></a>UITableView

Le API intorno `UITableView` sono principalmente invariate; Tuttavia, lo stile è stato modificato in modo significativo per essere conforme alla nuova progettazione dell'interfaccia utente. Anche la gerarchia di visualizzazione interna è leggermente diversa. Questa modifica non influirà sulla maggior parte delle app, ma è qualcosa da tenere presente.

#### <a name="grouped-table-style"></a>Stile tabella raggruppata

Lo stile raggruppato modificato è stato aggiornato e il contenuto è ora esteso ai bordi della schermata, come illustrato di seguito:

 ![](ios7-ui-images/table1.png "Sample Grouped Table Style")

#### <a name="separatorinset"></a>SeparatorInset

È ora possibile rientrare i separatori di riga impostando la proprietà `UITableVIewCell.SeparatorInset`. Il codice seguente, ad esempio, viene usato per rientrare le celle dal bordo sinistro:

```csharp
cell.SeparatorInset = new UIEdgeInsets (0, 50, 0, 0);
```

Questa operazione produce nella visualizzazione tabella con le celle rientrate come illustrato di seguito:

 ![](ios7-ui-images/separatorinset.png "Sample UITableView SeparatorInset")

#### <a name="table-button-styles"></a>Stili del pulsante tabella

I vari pulsanti usati nelle visualizzazioni di tabella sono stati modificati. Lo screenshot seguente presenta una visualizzazione tabella in modalità di modifica:

 ![](ios7-ui-images/table2.png "This screenshot presents a table view in editing mode")

### <a name="additional-control-changes"></a>Modifiche al controllo aggiuntive

Sono stati modificati anche altri controlli UIKit, inclusi i dispositivi di scorrimento, i commutatori e i stepper. Queste modifiche sono puramente visive. Per ulteriori informazioni, vedere la guida alla [transizione dell'interfaccia utente iOS 7](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/TransitionGuide/index.html)di Apple.

## <a name="general-user-interface-changes"></a>Modifiche generali dell'interfaccia utente

Oltre alle modifiche apportate in UIKit, iOS 7 introduce un'ampia gamma di modifiche visive all'interfaccia utente, tra cui:

- Contenuto a schermo intero
- Aspetto della barra
- Colore tinta

<a name="fullscreen" />

### <a name="full-screen-content"></a>Contenuto a schermo intero

iOS 7 è progettato per consentire alle applicazioni di sfruttare l'intero schermo. I controller di visualizzazione sono ora sovrapposti da una barra di stato e da una barra di navigazione, se presente, invece di essere visualizzati sotto le barre di spostamento e dello stato.

Quando si prepara l'applicazione per iOS 7, è possibile riallineare visivamente le sottoviste usando *Interface Builder* o *Novell iOS designer*. È anche possibile usare una delle nuove API per gestire il contenuto a schermo intero a livello di codice. Queste API sono illustrate di seguito.

#### <a name="toplayoutguide-and-bottomlayoutguide"></a>TopLayoutGuide e BottomLayoutGuide

 `TopLayoutGuide` e `BottomLayoutGuide` fungere da riferimento per la posizione in cui le visualizzazioni devono iniziare o terminare, in modo che il contenuto non sia sovrapposto a una barra di `UIKit` trasparente, come nell'esempio seguente:

 [![](ios7-ui-images/clipped.png "Sample content not overlapped by a translucent UIKit bar")](ios7-ui-images/clipped.png#lightbox)

Queste API possono essere usate per calcolare lo spostamento di una vista dalla parte superiore o inferiore dello schermo e modificare la posizione del contenuto di conseguenza:

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

È possibile usare il valore calcolato sopra per impostare lo spostamento del `ImageView`dalla parte superiore dello schermo, in modo che l'intera immagine sia visibile:

 [![](ios7-ui-images/good2.png "Example ImageViews displacement from the top of the screen")](ios7-ui-images/good2.png#lightbox)

Per un esempio funzionante, vedere [ImageViewer](https://docs.microsoft.com/samples/xamarin/ios-samples/ios7-ui-updates/) .

Il valore di spostamento viene generato in modo dinamico dopo che la vista è stata aggiunta alla gerarchia, quindi il tentativo di leggere `TopLayoutGuide` e `BottomLayoutGuide` valori in `ViewDidLoad` restituirà 0. Calcolare il valore dopo il caricamento della vista, ad esempio nell'`ViewDidLayoutSubviews`.

> [!IMPORTANT]
> `TopLayoutGuide` e `BottomLayoutGuide` sono deprecati in iOS 11 a favore del nuovo layout dell'area sicura. Apple ha dichiarato che l'uso dell'area sicura è compatibile con la versione iOS precedente a iOS 11. Per ulteriori informazioni, vedere la guida relativa all' [aggiornamento dell'app per iOS 11](~/ios/platform/introduction-to-ios11/updating-your-app/visual-design.md#fullscreen) .

#### <a name="edgesforextendedlayout"></a>EdgesForExtendedLayout

Questa API specifica quali bordi di una visualizzazione devono essere estesi a schermo intero, indipendentemente dalla translucidità della barra. In iOS 7, le barre di navigazione e le barre degli strumenti vengono visualizzate sovrapposte sopra la visualizzazione del controller, a differenza delle versioni precedenti di iOS, in cui non occupano lo stesso spazio. L'applicazione iOS 7 photos illustra il valore predefinito `UIViewController.EdgesForExtendedLayout` `UIRectEdge.All`. Questa impostazione riempie tutti e quattro i bordi della visualizzazione con il contenuto, creando l'effetto sovrapposto e a schermo intero:

 [![](ios7-ui-images/photos.png "Sample EdgesForExtendedLayout")](ios7-ui-images/photos.png#lightbox)

Toccando l'immagine, le barre vengono rimosse e viene visualizzata l'immagine a schermo intero:

 [![](ios7-ui-images/photos2.png "EdgesForExtendedLayout with the bars removed")](ios7-ui-images/photos2.png#lightbox)

Poiché il contenuto a schermo intero è l'impostazione predefinita, le applicazioni configurate per iOS 6 avranno parte della vista troncata, come nello screenshot seguente:

 [![](ios7-ui-images/clipped.png "Apps configured for iOS 6 will have part of the view clipped, as in this screenshot")](ios7-ui-images/clipped.png#lightbox)

La modifica della proprietà `UIViewController.EdgesForExtendedLayout` si adatta a questo comportamento. È possibile specificare che la vista non riempie i bordi, quindi la visualizzazione eviterà di visualizzare il contenuto nello spazio occupato dalla navigazione o dalle barre degli strumenti (a ogni orientamento):

```csharp
if (UIDevice.CurrentDevice.CheckSystemVersion (7, 0)) { 
    this.EdgesForExtendedLayout = UIRectEdge.None;
}
```

Nell'app si noterà che la visualizzazione è nuovamente riposizionata, quindi l'intera immagine è visibile:

 [![](ios7-ui-images/good.png "Example with whole image visible")](ios7-ui-images/good.png#lightbox)

Si noti che gli effetti delle API `TopLayoutGuide/BottomLayoutGuide` e `EdgesForExtendedLayout` sono simili, ma hanno lo scopo di colmare obiettivi diversi. La modifica dell'impostazione di `EdgesForExtendedLayout` dal valore predefinito può correggere le visualizzazioni ritagliate nelle applicazioni progettate per iOS 6, ma una progettazione iOS 7 valida deve rispettare l'estetica a schermo intero e offrire un'esperienza di visualizzazione a schermo intero, che si basa su `TopLayoutGuide` e `BottomLayoutGuide` per la corretta esecuzione Posizionare il contenuto che deve essere modificato in una posizione comoda per l'utente.

Per un esempio funzionante, vedere [ImageViewer](https://docs.microsoft.com/samples/xamarin/ios-samples/ios7-ui-updates/) .

### <a name="status-and-navigation-bars"></a>Stato e barre di spostamento

La barra di stato e le barre di navigazione vengono sottoposte a rendering con trasparenza. Le barre di stato sono trasparenti, mentre le barre degli strumenti e le barre di navigazione sono trasparenti e sfocate per fornire la sensazione di profondità nell'interfaccia utente. Lo screenshot seguente Mostra questa sfocatura e trasparenza, in cui il colore di sfondo blu della visualizzazione raccolta Mostra sia le barre di spostamento che lo stato, offrendo loro un aspetto blu chiaro:

 ![](ios7-ui-images/transparent-navbar.png "Sample Status and Navigation Bar blurring")

#### <a name="status-bar-styles"></a>Stili della barra di stato

Insieme alla sfocatura e alla trasparenza, il primo piano di una barra di stato può essere chiaro o scuro (scuro come impostazione predefinita). Lo stile della barra di stato può essere impostato dal controller di visualizzazione. Un controller di visualizzazione può inoltre impostare se la barra di stato è nascosta o visualizzata.

Il codice seguente, ad esempio, esegue l'override del metodo `PreferredStatusBarStyle` di un controller di visualizzazione per fare in modo che la barra di stato visualizzi un primo piano:

```csharp
public override UIStatusBarStyle PreferredStatusBarStyle ()
{
    return UIStatusBarStyle.LightContent;
}
```

In questo modo la barra di stato viene visualizzata come segue:

 ![](ios7-ui-images/light-status-bar.png "Sample Status Bar")

Per nascondere la barra di stato dal codice del controller di visualizzazione, eseguire l'override di `PrefersStatusBarHidden`, come illustrato di seguito:

```csharp
public override bool PrefersStatusBarHidden ()
{
    return true;
}
```

Viene nascosta la barra di stato:

 ![](ios7-ui-images/status-bar-hidden.png "Status Bar hidden")

### <a name="tint-color"></a>Colore tinta

I pulsanti sono ora visualizzati come testo senza Chrome. Il colore del testo può essere controllato usando la nuova proprietà `TintColor` `UIView`. Impostando il `TintColor` viene applicato il colore all'intera gerarchia di visualizzazione per la vista che la imposta. Per applicare un `TintColor`all'interno di un'app, impostarlo nell'`Window`. È anche possibile rilevare quando il colore della tinta viene modificato tramite il metodo `UIView.TintColorDidChange`.

Ad esempio, lo screenshot seguente mostra l'effetto della modifica del colore della tinta sulla visualizzazione del controller di spostamento in viola:

 ![](ios7-ui-images/tint-color.png "Purple tint color on a navigation controllers view")

Il colore della tinta può essere applicato anche alle immagini quando il `RenderingMode` è impostato su `UIImageRenderingMode.AlwaysTemplate`.

> [!IMPORTANT]
> Non è possibile impostare il colore tinta usando `UIAppearance`.

### <a name="dynamic-type"></a>Tipo dinamico

In iOS 7, l'utente può specificare le dimensioni del testo nelle impostazioni di sistema. Con il tipo dinamico, il tipo di carattere viene regolato dinamicamente in modo da risultare valido indipendentemente dalle dimensioni. `UIFont.PreferredFontForTextStyle` deve essere utilizzato per ottenere un tipo di carattere ottimizzato per le dimensioni controllate dall'utente.

## <a name="summary"></a>Riepilogo

Questo articolo illustra le modifiche apportate agli elementi dell'interfaccia utente in iOS 7. Esamina alcune delle modifiche apportate alle viste e ai controlli in UIKit, evidenziando sia le modifiche visive che le modifiche apportate alle API correlate. Infine, introduce nuove API per lavorare con il contenuto a schermo intero, il nuovo supporto dei colori tinta e il tipo dinamico.

## <a name="related-links"></a>Collegamenti correlati

- [ImageViewer (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios7-ui-updates)
