---
title: Utilizzo di tvOS navigazione e lo stato attivo in Xamarin
description: Questo articolo illustra il concetto di messa a fuoco e come viene usato per presentare e gestire la navigazione all'interno di un'app xamarin. tvos.
ms.prod: xamarin
ms.assetid: DD72E95F-AE9B-47D2-B132-5FA5FBD8026E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 3cb8d1c1d92146e70056c6cf562f2fa1cb028e7c
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61416450"
---
# <a name="working-with-tvos-navigation-and-focus-in-xamarin"></a>Utilizzo di tvOS navigazione e lo stato attivo in Xamarin

_Questo articolo illustra il concetto di messa a fuoco e come viene usato per presentare e gestire la navigazione all'interno di un'app xamarin. tvos._


Questo articolo illustra il concetto di [messa a fuoco](#Focus-and-Selection) e come viene usato per gestire [navigazione](#Navigation) nell'interfaccia utente di un'app xamarin. tvos. Verrà esaminato il tvOS incorporati controlli di spostamento come usare lo stato attivo, evidenziazione e la selezione per lo spostamento di interfaccia utente dell'app xamarin. tvos.

[![](navigation-focus-images/intro01.png "App tvOS navigazione dell'interfaccia utente")](navigation-focus-images/intro01.png#lightbox)

Successivamente, prenderemo in esame la modalità messa a fuoco può essere utilizzato con [parallasse](#Focus-and-Parallax) e *immagini sovrapposte* a forniscono indizi visivi per lo stato corrente di navigazione per l'utente finale.

Infine, si esamineranno lavora [messa a fuoco](#Working-with-Focus), [gli aggiornamenti dello stato attivo](#Working-with-Focus-Updates), [Guide messa a fuoco](#Working-with-Focus-Guides), [lo stato attivo nelle raccolte](#Working-with-Focus-in-Collections) e [ Abilitazione di parallasse](#enabling-parallax) nelle viste di immagine nelle App xamarin. tvos.

<a name="Navigation" />

## <a name="navigation"></a>Navigazione

Gli utenti dell'app xamarin. tvos verranno non interagisce con relativa interfaccia direttamente con iOS in cui vengono toccati immagini sullo schermo del dispositivo, ma è indirettamente da attraverso le chat room tramite il [remoti per Siri](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote). È necessario tenere a mente quando si progetta l'interfaccia utente dell'app in modo che naturalmente flussi, ma mantiene l'utente che si è immersi nell'esperienza di Apple TV.

Un'app tvOS riuscita implementa la navigazione in modo da supportare in modo uniforme lo scopo dell'app e la struttura dei dati in esso contenute senza chiamare attenzione al riquadro di spostamento se stesso. Progettare l'esplorazione del sito in modo che mi sembra naturale e familiare senza che dominano l'interfaccia utente o lo stato attivo dal contenuto e l'esperienza utente delle app di disegno.

[![](navigation-focus-images/nav01.png "Impostazioni app tvOS")](navigation-focus-images/nav01.png#lightbox)

Anche se utilizzano in genere un Apple TV, l'utente si sposta attraverso un set di barre in pila di schermate, ognuno presenta un determinato set di contenuto. A sua volta, ogni nuova schermata potrebbe causare uno o più schermi secondari dei contenuti usando i controlli dell'interfaccia utente standard, ad esempio [pulsanti](~/ios/tvos/user-interface/buttons.md), [barre schede](~/ios/tvos/user-interface/tab-bars.md), le tabelle [visualizzazioni raccolta](~/ios/tvos/user-interface/collection-views.md) o[ Doppia visualizzazione](~/ios/tvos/user-interface/split-views.md).

Con ogni nuova schermata dei dati, l'utente passa più approfondito in questo stack delle schermate. Tramite il **Menu** pulsante nel sistema remoto Siri, è possibile accedere con le versioni precedenti attraverso lo stack per restituire a una schermata precedente o dal Menu principale.

Tenendo presente quanto segue quando si progetta la navigazione per l'app tvOS suggerite da Apple:

- **Layout di navigazione per rendere la ricerca veloce contenuto e facile** -da utenti per accedere al contenuto all'interno dell'app nel minor numero di elementi di ritardo, fa clic e passa possibili. Semplifica la navigazione e organizza contenuti per i numeri minimo delle schermate.
- **Creare un'interfaccia fluidodinamica tramite tocco** -assicurarsi che un utente può spostarsi tra _gli elementi con stato attivabile_ con minimi problemi con il minor numero di possibili movimenti.
- **Progettazione con lo stato attivo presente** -poiché l'utente sta interagendo con contenuto tutta la stanza, necessario per passare lo stato attivo a un elemento di interfaccia utente prima di poter interagire con esso tramite il remoti per Siri. Gli utenti otterranno frustrati con l'app se richiede un numero eccessivo di movimenti per poter raggiungere i propri obiettivi.
- **Consentire la navigazione all'indietro tramite il pulsante di Menu** : per creare un'esperienza semplice e familiare, consentire agli utenti di spostarsi all'indietro usando la Siri Remote **Menu** pulsante. Premere il **Menu** pulsante deve sempre restituire alla schermata precedente o tornare al Menu principale dell'app. Al livello principale dell'app, premere il **Menu** pulsante deve tornare alla schermata Home Apple TV.
- **In genere si consiglia di evitare la visualizzazione di un pulsante Indietro** - perché premendo il **Menu** pulsante nel sistema remoto Siri Naviga indietro attraverso lo stack di schermata, evitare la visualizzazione di un controllo aggiuntivo che consente di duplicare questo comportamento. Un'eccezione a questa regola è per l'acquisto di schermate o schermate con azioni distruttive (ad esempio l'eliminazione del contenuto) in cui un **annullare** deve essere visualizzato anche il pulsante.
- **Mostra raccolte di grandi dimensioni in una singola schermata, invece di più** -The Siri remoto è stato progettato per spostarsi all'interno di un numero elevato di contenuti veloci e facile grazie a movimenti. Se l'app funziona con un'ampia raccolta di elementi con stato attivabile, provare a mantenerli in una singola schermata anziché suddividendole in molte schermate che richiedono una navigazione più parte dell'utente.
- **Utilizzare i controlli Standard per la navigazione** -anche in questo caso, per creare un'esperienza utente semplice e familiare, laddove possibile, utilizzare predefinito `UIKit` controlli, ad esempio i controlli di pagina, barre schede, controlli segmentati, le visualizzazioni di tabelle, visualizzazioni di raccolta e divisione Viste per la navigazione dell'app. Poiché l'utente ha già familiarità con questi elementi, in modo intuitivo saranno in grado di passare all'app.
- **Ottimizza per la navigazione nel contenuto orizzontale** -a causa della natura di Apple TV, scorrendo rapidamente da sinistra a destra nel sistema remoto Siri è più naturale che su e giù. Prendere in considerazione questa opzione quando si progettano i layout del contenuto per l'app.

<a name="Focus-and-Selection" />

## <a name="focus-and-selection"></a>Lo stato attivo e la selezione

Nella Apple TV, un'immagine, sul pulsante o altro elemento dell'interfaccia utente viene considerato _fuoco_ quando si tratta della destinazione della navigazione corrente.

[![](navigation-focus-images/focus01.png "Esempio di selezione e lo stato attivo")](navigation-focus-images/focus01.png#lightbox)

A differenza, i dispositivi iOS in cui l'utente sta interagendo direttamente con gli elementi sul touchscreen del dispositivo, gli utenti interagiscono con gli elementi di tvOS da attraversare la stanza usando il remoti per Siri. Per presentare e gestire questa interazione dell'utente, Apple TV Usa un' _messa a fuoco_ basato sul modello.

Usando i movimenti e pulsante preme il [remoti per Siri](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote), l'utente sposta lo stato attivo da un elemento dell'interfaccia utente a un altro. Dopo aver spostato lo stato attivo per l'elemento desiderato, l'utente fa clic per selezionare il contenuto o attivare l'azione rappresentata da quell'elemento.

In caso di modifica dello stato attivo, le animazioni sottili ed effetti (ad esempio, l'effetto di parallasse nelle immagini) vengono utilizzati per identificare chiaramente l'elemento di interfaccia utente che ha attualmente lo stato attivo.

Apple ha i seguenti suggerimenti per l'uso di selezione e lo stato attivo:

- **Usare i controlli dell'interfaccia utente predefiniti per gli effetti di movimento** - utilizzando `UIKit` e l'API dello stato attivo nell'interfaccia utente, il modello di stato attivo verrà applicato automaticamente il movimento predefiniti e gli effetti visivi a elementi dell'interfaccia utente. Questo rendendo l'app native e familiare agli utenti della piattaforma di Apple TV e consente lo spostamento fluido e intuitivo tra gli elementi con stato attivabile.
- **Spostare lo stato attivo in direzioni previsto** -in Apple TV, quasi tutti gli elementi Usa manipolazione indiretta. Ad esempio, l'utente utilizza il remoti per Siri per spostare lo stato attivo e il sistema scorre automaticamente l'interfaccia per mantenere visibile l'elemento correntemente attivo. Se l'app implementa questo tipo di interazione, assicurarsi che lo stato attivo si sposta nella direzione movimento dell'utente. Pertanto, se l'utente scorrere a destra nello stato attivo remoti per Siri deve spostare a destra (che potrebbe causare la schermata scorrere a sinistra). L'unica eccezione a questa regola è gli elementi a schermo intero usano manipolazione diretta (dove scorrendo rapidamente backup Sposta l'elemento verso l'alto).
- **Assicurarsi che l'elemento con stato attivo è Obvious** -poiché gli utenti interagiscono con elementi dell'interfaccia utente da lontano, è fondamentale che l'elemento attualmente attivato è l'acronimo. In genere questa operazione verrà gestita automaticamente mediante predefinito `UIKit` elementi. Per i controlli personalizzati, usare funzionalità quali shadow o dimensioni dell'elemento per visualizzare lo stato attivo.
- **Usare parallasse per rendere concentrato elementi reattiva** : piccola, movimenti circolari nel sistema remoto Siri comportare lo spostamento gentle e in tempo reale dell'elemento con lo stato attivo. Ciò [effetto di parallasse](#Focus-and-Parallax) è incorporata nel `UIKit` _immagini sovrapposte_ per consentire all'utente un senso di connessione per l'elemento con lo stato attivo.
- **Creare elementi con stato attivabile della dimensione appropriata** -elementi di grandi dimensioni con spaziatura molto più facili da selezione e lo spostamento di elementi più piccoli.
- **Progettare l'elemento dell'interfaccia utente per aspetto ottimale sia con stato attivo o Unfocused** -in genere Apple TV rappresenta l'elemento con stato attivo, aumentando le dimensioni. Assicurarsi che gli elementi dell'interfaccia utente dell'App aspetto accattivante con qualsiasi dimensione di presentazione e, se necessario, forniscono risorse per gli elementi con dimensioni maggiori.
- **Rappresentano lo stato attivo modifiche agevolmente** -usare animazione di dissolvenza in entrata in modo uniforme tra gli elementi di un **Focused** e **Unfocused** per mantenere le transizioni da being jarring.
- **Non visualizza un cursore** -gli utenti si aspettano passare l'interfaccia utente dell'app con lo stato attivo e non spostando un cursore attorno alla schermata. L'interfaccia utente deve usare sempre il modello di messa a fuoco per presentare un'esperienza utente coerente.

<a name="Working-with-Focus" />

### <a name="working-with-focus"></a>Funziona con lo stato attivo

Potrebbero esserci volte in cui si desidera creare un controllo personalizzato che può diventare un elemento con stato attivabile. Se pertanto eseguire l'override di `CanBecomeFocused` proprietà e restituire `true`, altrimenti restituire `false`. Ad esempio:

```csharp
public class myView : UIView
{
    public override bool CanBecomeFocused {
        get {return true;}
    }
}
```

In qualsiasi momento è possibile usare la `Focused` proprietà di un `UIKit` controllo per verificare se è l'elemento corrente. Se `true` l'elemento dell'interfaccia utente ha attualmente lo stato attivo, altrimenti non esiste. Ad esempio:

```csharp
// Is my view in focus?
if (myView.Focused) {
    // Do something
    ...
}
```

Anche se è possibile spostare direttamente lo stato attivo a un altro elemento dell'interfaccia utente tramite il codice, è possibile specificare quale elemento dell'interfaccia utente innanzitutto di ottenere lo stato attivo quando viene caricata una schermata impostando relativi `PreferredFocusedView` proprietà `true`. Ad esempio:

```csharp
// Make the play button the starting focus item
playButton.PreferredFocusedView = true;
```

<a name="Working-with-Focus-Updates" />

### <a name="working-with-focus-updates"></a>Lavorare con gli aggiornamenti dello stato attivo 

Quando l'utente fa sì che lo stato attivo da spostare da un elemento dell'interfaccia utente a un'altra (ad esempio, in risposta a un movimento nell'elemento remoto Siri) un _evento di aggiornamento dello stato attivo_ verranno inviati per l'elemento perde lo stato attivo e l'elemento ottenendo lo stato attivo.

Per gli elementi personalizzati da cui ereditare `UIView` o `UIViewController`, è possibile eseguire l'override di diversi metodi per l'interazione con l'evento di aggiornamento dello stato attivo:

- **DidUpdateFocus** -questo metodo verrà chiamato ogni volta che la vista acquisisce o perde lo stato attivo.
- **ShouldUpdateFocus** -usare questo metodo per definire in cui lo stato attivo è consentito spostare.

Per richiedere che il motore dello stato attivo si sposta lo stato attivo nuovamente il `PreferredFocusedView` elemento dell'interfaccia utente, chiamata di `SetNeedsUpdateFocus` metodo del Controller di visualizzazione.

> [!IMPORTANT]
> La chiamata `SetNeedsUpdateFocus` ha effetto solo se il Controller di visualizzazione viene richiamata in contiene la vista che attualmente ha lo stato attivo.




<a name="Working-with-Focus-Guides" />

### <a name="working-with-focus-guides"></a>Utilizzo di guide per lo stato attivo

Il motore di messa a fuoco integrate di tvOS è fantastica per la gestione degli spostamenti tra gli elementi che rientrano in una griglia orizzontale e verticale. In genere, è necessario non fare nulla più di aggiungere che elementi dell'interfaccia utente per la progettazione di interfaccia e il motore di messa a fuoco gestirà automaticamente lo spostamento tra gli elementi senza l'intervento per gli sviluppatori.

Tuttavia, potrebbero esserci casi, a causa di requisiti di progettazione dell'interfaccia utente, elementi dell'interfaccia utente non rientrano in una griglia orizzontale e verticale e potrebbero essere inaccessibili perché sono diagonale tra loro. Ciò accade perché il motore di messa a fuoco è stato progettato per la gestione semplice verso l'alto, verso il basso, lo spostamento di sinistra e destra tra solo gli elementi dell'interfaccia utente.

Eseguire il layout dell'interfaccia utente seguente per un esempio:

 [![](navigation-focus-images/guide01.png "Uso di esempio guide per lo stato attivo")](navigation-focus-images/guide01.png#lightbox)
 
Poiché il **More Info** pulsante non rientra in una griglia verticale e orizzontale con la **acquistare** pulsante non sarebbe accessibile all'utente. Tuttavia, ciò può essere facilmente corretto usando un _Guida per lo stato attivo_ fornire lo spostamento dei suggerimenti per il motore di messa a fuoco. 

Una Guida di messa a fuoco (`UIFocusGuide`) espone un'area della vista non visibili come Focusable al motore di messa a fuoco, consentendo in tal modo lo stato attivo vengano reindirizzati a un'altra visualizzazione.

Pertanto, per risolvere l'esempio sopra riportato, il codice seguente è stato possibile aggiungere al Controller di visualizzazione per creare una guida lo stato attivo tra il **More Info** e **acquistare** pulsanti:

```csharp
public UIFocusGuide FocusGuide = new UIFocusGuide ();
...

public override void ViewDidLoad ()
{
    base.ViewDidLoad ();

    // Add Focus Guide to layout
    View.AddLayoutGuide (FocusGuide);

    // Define Focus Guide that will allow the user to move
    // between the More Info and Buy buttons.
    FocusGuide.LeftAnchor.ConstraintEqualTo (BuyButton.LeftAnchor).Active = true;
    FocusGuide.TopAnchor.ConstraintEqualTo (MoreInfoButton.TopAnchor).Active = true;
    FocusGuide.WidthAnchor.ConstraintEqualTo (BuyButton.WidthAnchor).Active = true;
    FocusGuide.HeightAnchor.ConstraintEqualTo (MoreInfoButton.HeightAnchor).Active = true;
}
```

Per prima cosa, un nuovo `UIFocusGuide` viene creato e aggiunto alla raccolta di Guida per il Layout della visualizzazione usando la `AddLayoutGuide` (metodo).

Successivamente, la Guida di messa a fuoco Top, Left, larghezza e altezza ancoraggi vengono modificati rispetto al **More Info** e **acquistare** pulsanti da posizionarla tra di essi. Vedere:

[![](navigation-focus-images/guide02.png "Guida di esempio lo stato attivo")](navigation-focus-images/guide02.png#lightbox)

È anche importante notare che vengono attivati i vincoli di nuove man mano che vengono creati tramite l'impostazione loro `Active` proprietà `true`:

```csharp
FocusGuide.LeftAnchor.ConstraintEqualTo (...).Active = true;
```

Con la Guida lo stato attivo nuovo stabilita e aggiunto a, il Controller di visualizzazione della vista `DidUpdateFocus` può eseguire l'override di metodo e aggiungere il codice seguente per spostarsi tra il **More Info** e **acquistare** pulsanti:

```csharp
public override void DidUpdateFocus (UIFocusUpdateContext context, UIFocusAnimationCoordinator coordinator)
{
    base.DidUpdateFocus (context, coordinator);

    // Get next focusable item from context
    var nextFocusableItem = context.NextFocusedView;

    // Anything to process?
    if (nextFocusableItem == null) return;

    // Decide the next focusable item based on the current
    // item with focus
    if (nextFocusableItem == MoreInfoButton) {
        // Move from the More Info to Buy button
        FocusGuide.PreferredFocusedView = BuyButton;
    } else if (nextFocusableItem == BuyButton) {
        // Move from the Buy to the More Info button
        FocusGuide.PreferredFocusedView = MoreInfoButton;
    } else {
        // No valid move
        FocusGuide.PreferredFocusedView = null;
    }
}
```

Per prima cosa, questo get di codice le `NextFocusedView` dal `UIFocusUpdateContext` che è stata passata (`context`). Se questa visualizzazione è `null`, quindi non è necessaria alcuna elaborazione e il metodo è stato chiuso.

Successivamente, il `nextFocusableItem` viene valutata. Se corrisponde a uno il **More Info** oppure **acquistare** pulsanti, lo stato attivo viene inviato al pulsante opposto di utilizzare la Guida di messa a fuoco `PreferredFocusedView` proprietà. Ad esempio:

```csharp
// Move from the More Info to Buy button
FocusGuide.PreferredFocusedView = BuyButton;
```

Nel caso in cui nessun pulsante rappresenta l'origine dello spostamento dello stato attivo, il `PreferredFocusedView` proprietà è deselezionata:

```csharp
// No valid move
FocusGuide.PreferredFocusedView = null;
```

<a name="Working-with-Focus-in-Collections" />

### <a name="working-with-focus-in-collections"></a>Funziona con lo stato attivo nelle raccolte

Quando si decide se un singolo elemento può essere attivabile in un `UICollectionView` o un `UITableView`, viene eseguito l'override di metodi delle `UICollectionViewDelegate` o `UITableViewDelegate` rispettivamente. Ad esempio:

```csharp
public class CardHandDelegate : UICollectionViewDelegateFlowLayout
{
    ...
    public override bool CanFocusItem (UICollectionView collectionView, NSIndexPath indexPath)
    {
        if (indexPath == null) {
            return false;
        } else {
            var controller = collectionView as CardHandViewController;
            return !controller.Hand [indexPath.Row].IsFaceDown;
        }
    }
}
```

Il `CanFocusItem` restituzione del metodo `true` se l'elemento corrente può essere in stato attivo, in caso contrario restituisce `false`.

Se si desidera che un `UICollectionView` o un `UITableView` per ricordare e ripristinare lo stato attivo all'ultimo elemento quando perde e riacquisisce messa a fuoco, impostare il `RemembersLastFocusedIndexPath` proprietà `true`.

<a name="Focus-and-Parallax" />

## <a name="focus-and-parallax"></a>Lo stato attivo e parallasse

Come indicato in precedenza, un elemento dell'interfaccia utente viene considerato _fuoco_ quando è l'elemento corrente durante un evento di navigazione. In genere come un elemento viene messa a fuoco, della dimensione del leggermente è aumentata e che venga elevato visivamente con un'ombreggiatura. 

Se l'utente esegue un'operazione lenta, circolare nel sistema remoto Siri, l'elemento con stato attivo verrà sway in tempo reale in risposta a questo movimento. Non appena si verifica il sway, un brillantezza illuminato viene applicato per la propria immagine risulterà la superficie per gli sviluppatori. Dopo un determinato periodo di inattività, qualsiasi contenuto di fuori di fuoco attenua e l'elemento attivo aumenterà anche maggiore. 

Questi effetti interagiscono per fornire una connessione tra il contenuto della schermata TV e l'utente interagisce con Apple TV da divano mentale.

Nella Apple TV, questo effetto di parallasse viene utilizzato in tutto il sistema per trasmettere un certo senso profondità 3D e dynamics, fino a elementi messa a fuoco. tvOS utilizza una serie di trasparente [immagini sovrapposte](~/ios/tvos/app-fundamentals/icons-images.md#Layered-Images) che viene viene spostato e ridimensionato in modo dinamico in modo da creare questo effetto.

Le immagini sovrapposte sono necessarie per l'icona dell'app tvOS ed è supportata per il contenuto dinamico di evidenza. Sebbene non obbligatorio, fortemente suggerite da Apple implementazione immagini a più livelli in qualsiasi altro elemento attivabile e nell'interfaccia utente dell'app.

### <a name="enabling-parallax"></a>Abilitazione di parallasse

Il `UIImageView` controllo (o qualsiasi controllo che eredita da `UIImageView`) supporta automaticamente l'effetto di parallasse. Per impostazione predefinita, questo supporto è disabilitato, abilitarlo, usare il codice seguente:

```csharp
myImageView.AdjustsImageWhenAncestorFocused = true;
```

Con questa proprietà è impostata su `true`, la visualizzazione di immagini otterranno automaticamente l'effetto di parallasse quando viene selezionato dall'utente e lo stato attivo.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato il concetto di messa a fuoco e come viene usato per gestire la navigazione nell'interfaccia utente di un'app xamarin. tvos. Esso esaminare il tvOS incorporati controlli di spostamento come usare lo stato attivo, evidenziazione e la selezione per la navigazione. Successivamente, esaminato la modalità messa a fuoco utilizzabile con immagini a più livelli e parallasse per forniscono indizi visivi per lo stato corrente di navigazione per l'utente finale. Infine, esaminato funzionante con lo stato attivo, gli aggiornamenti dello stato attivo, lo stato attivo in raccolte e abilitazione di parallasse.




## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [le guide dell'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
