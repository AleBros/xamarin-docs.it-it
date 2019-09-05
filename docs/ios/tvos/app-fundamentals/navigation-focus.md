---
title: Uso della navigazione tvOS e messa a fuoco in Novell
description: Questo articolo illustra il concetto di messa a fuoco e il modo in cui viene usato per presentare e gestire la navigazione all'interno di un'app Novell. tvOS.
ms.prod: xamarin
ms.assetid: DD72E95F-AE9B-47D2-B132-5FA5FBD8026E
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/16/2017
ms.openlocfilehash: fdf675e37bec07ef03576313befbaa9309ab9349
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291580"
---
# <a name="working-with-tvos-navigation-and-focus-in-xamarin"></a>Uso della navigazione tvOS e messa a fuoco in Novell

_Questo articolo illustra il concetto di messa a fuoco e il modo in cui viene usato per presentare e gestire la navigazione all'interno di un'app Novell. tvOS._


Questo articolo illustra il concetto di [messa a fuoco](#Focus-and-Selection) e il modo in cui viene usato per gestire la [navigazione](#Navigation) nell'interfaccia utente di un'app Novell. tvOS. Si esaminerà il modo in cui i controlli di navigazione tvOS incorporati usano lo stato attivo, l'evidenziazione e la selezione per fornire la navigazione dell'interfaccia utente dell'app Novell. tvOS.

[![](navigation-focus-images/intro01.png "Esplorazione dell'interfaccia utente delle app tvOS")](navigation-focus-images/intro01.png#lightbox)

Si esaminerà ora il modo in cui lo stato attivo può essere usato con [Parallax](#Focus-and-Parallax) e *immagini sovrapposte* per fornire agli utenti finali indizi visivi per lo stato di navigazione corrente.

Infine, si esamineranno [gli aggiornamenti](#Working-with-Focus), [gli aggiornamenti](#Working-with-Focus-Updates), [le guide di messa a fuoco](#Working-with-Focus-Guides), [le raccolte](#Working-with-Focus-in-Collections) e l'[abilitazione di parallasse](#enabling-parallax) sulle visualizzazioni di immagini nelle app Novell. tvOS.

<a name="Navigation" />

## <a name="navigation"></a>Navigazione

Gli utenti dell'app Novell. tvOS non interagisce direttamente con l'interfaccia, come con iOS, dove toccano immagini sullo schermo del dispositivo, ma indirettamente da tutta la stanza usando [Siri Remote](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote). È necessario tenere presente questo aspetto durante la progettazione dell'interfaccia utente dell'app in modo che fluisca naturalmente, ma mantiene l'utente immerso nell'esperienza Apple TV.

Un'app tvOS riuscita implementa la navigazione in modo da supportare agevolmente lo scopo dell'app e la struttura dei dati presentati senza richiamare l'attenzione sulla navigazione. Progettare la navigazione in modo che risulti naturale e familiare senza dominare l'interfaccia utente o disegnare lo stato attivo dal contenuto e dall'esperienza utente delle app.

[![](navigation-focus-images/nav01.png "App Impostazioni tvOS")](navigation-focus-images/nav01.png#lightbox)

Quando si usa un Apple TV, l'utente si sposta in genere in un set di schermate in pila, ciascuna delle quali presenta un determinato set di contenuti. A sua volta, ogni nuova schermata può portare a una o più schermate secondarie del contenuto mediante controlli dell'interfaccia utente standard, ad esempio [pulsanti](~/ios/tvos/user-interface/buttons.md), [barre delle schede](~/ios/tvos/user-interface/tab-bars.md), tabelle, [visualizzazioni di raccolta](~/ios/tvos/user-interface/collection-views.md) o [visualizzazioni suddivise](~/ios/tvos/user-interface/split-views.md).

Con ogni nuova schermata di dati, l'utente si sposta più a fondo in questo stack di schermate. Utilizzando il pulsante di **menu** sul Siri remoto, è possibile spostarsi all'indietro dello stack per tornare a una schermata precedente o a un menu principale.

Quando si progetta la navigazione per l'app tvOS, Apple suggerisce di tenere presente quanto segue:

- **Layout della navigazione per rendere la ricerca di contenuti veloce e semplice** : gli utenti vogliono accedere al contenuto all'interno dell'app con il minor numero di tocchi, clic e scorrimenti nel minor tempo possibile. Semplifica la navigazione e organizza il contenuto con un numero minimo di schermate.
- **Creare un'interfaccia fluida usando il tocco** : assicurarsi che un utente possa spostarsi tra _gli elementi attivabili_ con un attrito minimo usando il minor numero di movimenti possibili.
- **Progettazione con attenzione** : poiché l'utente interagisce con il contenuto in tutta la stanza, è necessario spostare lo stato attivo su un elemento dell'interfaccia utente prima di interagire con esso utilizzando Siri remote. Gli utenti vengono frustrati con l'app se sono necessari troppi movimenti per raggiungere i propri obiettivi.
- **Fornire la navigazione all'indietro tramite il pulsante di menu** : per creare un'esperienza semplice e familiare, consentire agli utenti di spostarsi all'indietro usando il pulsante di **menu** di Siri remote. Premere il pulsante di **menu** per tornare sempre alla schermata precedente oppure tornare al menu principale dell'app. Al livello principale dell'app, premere il pulsante di **menu** per tornare alla schermata iniziale di Apple TV.
- **In genere è consigliabile evitare di visualizzare un pulsante indietro** , perché premendo il pulsante di **menu** nella finestra di Siri remote si sposta indietro sullo stack dello schermo, evitando di visualizzare un controllo aggiuntivo che Duplica questo comportamento. Un'eccezione a questa regola è per l'acquisto di schermate o schermate con azioni distruttive, ad esempio l'eliminazione di contenuto, in cui deve essere visualizzato un pulsante **Annulla** .
- **Visualizzazione di raccolte di grandi dimensioni in un'unica schermata, anziché molti** -il Siri Remote è stato progettato per semplificare e velocizzare l'uso di un ampio insieme di contenuti. Se l'app funziona con una raccolta di grandi dimensioni di elementi attivabili, è consigliabile tenerli in un'unica schermata anziché suddividerli in molte schermate che richiedono una maggiore navigazione sulla parte dell'utente.
- **Usare i controlli standard per la navigazione** , per creare un'esperienza utente semplice e intuitiva, laddove possibile, usare `UIKit` controlli incorporati, ad esempio controlli pagina, barre delle schede, controlli segmentati, viste tabella, visualizzazioni raccolta e visualizzazioni suddivise per esplorazione dell'app. Poiché l'utente ha già familiarità con questi elementi, sarà intuitivamente in grado di spostarsi nell'app.
- **Favorisce la navigazione orizzontale del contenuto** : a causa della natura della TV Apple, l'scorrimento verso sinistra verso destra sul lato Siri remoto è più naturale che in alto o in basso. Prendere in considerazione questa opzione quando si progettano i layout del contenuto per l'app.

<a name="Focus-and-Selection" />

## <a name="focus-and-selection"></a>Messa a fuoco e selezione

In Apple TV, un'immagine, un pulsante o un altro elemento dell'interfaccia utente viene considerato _attivo_ quando è la destinazione della navigazione corrente.

[![](navigation-focus-images/focus01.png "Esempio di stato attivo e selezione")](navigation-focus-images/focus01.png#lightbox)

A differenza dei dispositivi iOS in cui l'utente interagisce direttamente con gli elementi nel touchscreen del dispositivo, gli utenti interagiscono con gli elementi tvOS da tutta la stanza usando Siri remote. Per presentare e gestire questa interazione con l'utente, Apple TV usa un modello basato _sullo stato attivo_ .

Utilizzando i movimenti e le pressioni dei pulsanti sul [Siri remoto](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote), l'utente sposta lo stato attivo da un elemento dell'interfaccia utente a un altro. Quando lo stato attivo viene spostato sull'elemento desiderato, l'utente fa clic per selezionare il contenuto o attivare l'azione rappresentata dall'elemento.

Quando si modifica lo stato attivo, vengono usate animazioni e effetti delicati, ad esempio l'effetto di parallasse sulle immagini, per identificare chiaramente l'elemento dell'interfaccia utente che attualmente ha lo stato attivo.

Apple presenta i suggerimenti seguenti per lavorare con lo stato attivo e la selezione:

- **Usare i controlli dell'interfaccia utente incorporati per gli effetti** di `UIKit` movimento: usando e l'API messa a fuoco nell'interfaccia utente, il modello di messa a fuoco applica automaticamente gli effetti visivi e di movimento predefiniti agli elementi dell'interfaccia utente. In questo modo l'app si rende nativa e familiare agli utenti della piattaforma Apple TV e consente un movimento fluido e intuitivo tra gli elementi attivabili.
- **Spostare lo stato attivo nelle direzioni previste** : su Apple TV, quasi ogni elemento usa la manipolazione indiretta. Ad esempio, l'utente usa Siri Remote per spostare lo stato attivo e il sistema scorre automaticamente l'interfaccia per rendere visibile l'elemento attualmente attivo. Se l'app implementa questo tipo di interazione, assicurarsi che lo stato attivo si sposti nella direzione del movimento dell'utente. Quindi, se l'utente scorre a destra sullo stato attivo Siri, si sposta a destra (che potrebbe far scorrere lo schermo a sinistra). L'unica eccezione a questa regola è costituita dagli elementi a schermo intero che usano la manipolazione diretta, in cui lo scorrimento verso l'alto sposta l'elemento verso l'alto.
- **Verificare che l'elemento con lo stato attivo sia ovvio** , perché gli utenti interagiscono con gli elementi dell'interfaccia utente da Afar, è fondamentale che l'elemento attualmente attivo si trovi. Questa operazione viene in genere gestita automaticamente da `UIKit` elementi predefiniti. Per i controlli personalizzati, usare funzionalità come la dimensione dell'elemento o l'ombreggiatura per mostrare lo stato attivo.
- **Usare Parallax per fare in modo che gli elementi mirati rispondano** a piccoli movimenti circolari su Siri Remote result in un movimento leggero e in tempo reale dell'elemento con lo stato attivo. Questo [effetto di parallasse](#Focus-and-Parallax) è incorporato `UIKit` in _immagini sovrapposte_ per fornire all'utente un senso di connessione all'elemento con lo stato attivo.
- **Crea elementi attivabili delle dimensioni appropriate** : gli elementi di grandi dimensioni con spaziatura ampia sono più semplici da selezionare e spostarsi rispetto a elementi più piccoli.
- **Progettare l'elemento dell'interfaccia utente in modo che risulti valido o con lo stato attivo** , in genere Apple TV rappresenta l'elemento con lo stato attivo aumentando le dimensioni. Assicurarsi che gli elementi dell'interfaccia utente dell'app risultino ottimali in qualsiasi dimensione di presentazione e, se necessario, forniscano risorse per gli elementi di dimensioni maggiori.
- **Rappresenta le modifiche** dello stato attivo: usare l'animazione per eseguire una dissolvenza uniforme **tra gli elementi con stato attivo e** non **attivo** per evitare che le transizioni siano stonate.
- **Non visualizzare un cursore** : gli utenti si aspettano di spostarsi nell'interfaccia utente dell'app usando lo stato attivo e non spostando un cursore sullo schermo. L'interfaccia utente deve sempre usare il modello di messa a fuoco per presentare un'esperienza utente coerente.

<a name="Working-with-Focus" />

### <a name="working-with-focus"></a>Utilizzo dello stato attivo

In alcuni casi potrebbe essere necessario creare un controllo personalizzato che può diventare un elemento attivabile. In tal caso, `CanBecomeFocused` eseguire l'override `true`della proprietà e `false`restituire, altrimenti restituisce. Ad esempio:

```csharp
public class myView : UIView
{
    public override bool CanBecomeFocused {
        get {return true;}
    }
}
```

In qualsiasi momento è possibile utilizzare la `Focused` proprietà di un `UIKit` controllo per verificare se è l'elemento corrente. Se `true` l'elemento dell'interfaccia utente ha attualmente lo stato attivo, in caso contrario. Ad esempio:

```csharp
// Is my view in focus?
if (myView.Focused) {
    // Do something
    ...
}
```

Sebbene non sia possibile spostare direttamente lo stato attivo su un altro elemento dell'interfaccia utente tramite codice, è possibile specificare quale elemento dell'interfaccia utente ottiene lo stato `PreferredFocusedView` attivo per `true`primo quando viene caricata una schermata impostando la relativa proprietà su. Ad esempio:

```csharp
// Make the play button the starting focus item
playButton.PreferredFocusedView = true;
```

<a name="Working-with-Focus-Updates" />

### <a name="working-with-focus-updates"></a>Utilizzo degli aggiornamenti dello stato attivo 

Quando l'utente fa in modo che lo stato attivo venga spostato da un elemento dell'interfaccia utente a un altro (ad esempio, in risposta a un movimento sul Siri remoto), un _evento di aggiornamento dello stato attivo_ verrà inviato all'elemento che perde lo stato attivo e l'elemento ottiene lo stato attivo.

Per gli elementi personalizzati che ereditano `UIViewController`da `UIView` o, è possibile eseguire l'override di diversi metodi per lavorare con l'evento di aggiornamento dello stato attivo:

- **DidUpdateFocus** -questo metodo verrà chiamato ogni volta che la visualizzazione Ottiene o perde lo stato attivo.
- **ShouldUpdateFocus** : usare questo metodo per definire il punto in cui lo stato attivo può essere spostato.

Per richiedere che lo stato attivo venga spostato di nuovo sull' `PreferredFocusedView` elemento dell'interfaccia utente, `SetNeedsUpdateFocus` chiamare il metodo del controller di visualizzazione.

> [!IMPORTANT]
> La `SetNeedsUpdateFocus` chiamata a ha effetto solo se il controller di visualizzazione in cui viene chiamato contiene la vista che attualmente ha lo stato attivo.




<a name="Working-with-Focus-Guides" />

### <a name="working-with-focus-guides"></a>Utilizzo delle guide per lo stato attivo

Il motore di messa a fuoco integrato in tvOS è ideale per gestire i movimenti tra gli elementi che rientrano in una griglia orizzontale e verticale. In genere, non è necessario eseguire alcuna operazione oltre ad aggiungere gli elementi dell'interfaccia utente alla progettazione dell'interfaccia e il motore di messa a fuoco gestirà automaticamente lo spostamento tra gli elementi senza l'intervento dello sviluppatore.

Tuttavia, in alcuni casi, a causa della necessità della progettazione dell'interfaccia utente, gli elementi dell'interfaccia utente non rientrano in una griglia orizzontale e verticale e potrebbero essere inaccessibili perché sono diagonali tra loro. Questo problema si verifica perché il motore di messa a fuoco è stato progettato per gestire un semplice spostamento verso l'alto, verso il basso, verso sinistra e verso destra tra gli elementi

Per un esempio, adottare il layout dell'interfaccia utente seguente:

 [![](navigation-focus-images/guide01.png "Esempio di utilizzo delle guide di messa a fuoco")](navigation-focus-images/guide01.png#lightbox)
 
Poiché il pulsante **altre informazioni** non rientra in una griglia orizzontale e verticale con il pulsante **Acquista** , l'utente non sarà accessibile. Tuttavia, questa operazione può essere facilmente corretta utilizzando una _guida messa a fuoco_ per fornire suggerimenti di spostamento al motore di messa a fuoco. 

Una guida messa a`UIFocusGuide`fuoco () espone un'area non visibile della visualizzazione come attivabile per il motore attivo, consentendo così il reindirizzamento dello stato attivo a un'altra visualizzazione.

Quindi, per risolvere l'esempio precedente, è possibile aggiungere al controller di visualizzazione il codice seguente per creare una guida per lo stato attivo tra le **altre informazioni** e i pulsanti di **acquisto** :

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

Per prima cosa, `UIFocusGuide` viene creato un nuovo oggetto che viene aggiunto alla raccolta di guide di `AddLayoutGuide` layout della visualizzazione usando il metodo.

Successivamente, gli ancoraggi superiore, sinistro, larghezza e altezza della Guida di messa a fuoco vengono regolati in relazione alle **altre informazioni** e ai pulsanti **Acquista** per posizionarli tra di essi. Vedere:

[![](navigation-focus-images/guide02.png "Guida messa a fuoco di esempio")](navigation-focus-images/guide02.png#lightbox)

È anche importante notare che i nuovi vincoli vengono attivati Man mano che vengono creati impostando la relativa `Active` proprietà su `true`:

```csharp
FocusGuide.LeftAnchor.ConstraintEqualTo (...).Active = true;
```

Con la nuova guida messa a fuoco stabilita e aggiunta alla vista, è `DidUpdateFocus` possibile eseguire l'override del metodo del controller di visualizzazione e aggiungere il codice seguente per spostarsi tra i pulsanti **ulteriori informazioni** e **Acquista** :

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

In primo luogo, questo codice ottiene `NextFocusedView` `UIFocusUpdateContext` da che è stato passato (`context`). Se questa vista è `null`, non è necessaria alcuna elaborazione e il metodo è terminato.

`nextFocusableItem` Viene quindi valutato. Se corrisponde ai pulsanti **ulteriori informazioni** o **Acquista** , lo stato attivo viene inviato al pulsante opposto usando la `PreferredFocusedView` proprietà della guida messa a fuoco. Ad esempio:

```csharp
// Move from the More Info to Buy button
FocusGuide.PreferredFocusedView = BuyButton;
```

Nel caso in cui nessuno dei due pulsanti sia l'origine dello spostamento dello stato `PreferredFocusedView` attivo, la proprietà viene deselezionata:

```csharp
// No valid move
FocusGuide.PreferredFocusedView = null;
```

<a name="Working-with-Focus-in-Collections" />

### <a name="working-with-focus-in-collections"></a>Utilizzo dello stato attivo nelle raccolte

Quando si decide se un singolo elemento può `UICollectionView` essere attivo in un `UITableView`o in, si eseguirà l'override rispettivamente dei metodi `UICollectionViewDelegate` di `UITableViewDelegate` o. Ad esempio:

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

Il `CanFocusItem` metodo restituisce `true` se l'elemento corrente può trovarsi nello stato attivo; in `false`caso contrario, restituisce.

Se si desidera che `UICollectionView` un oggetto `UITableView` o un oggetto memorizzi e ripristini lo stato attivo sull'ultimo elemento quando perde e recupera lo stato attivo `RemembersLastFocusedIndexPath` , impostare `true`la proprietà su.

<a name="Focus-and-Parallax" />

## <a name="focus-and-parallax"></a>Stato attivo e parallasse

Come indicato in precedenza, un elemento dell'interfaccia utente viene considerato _attivo_ quando è l'elemento corrente durante un evento di navigazione. In genere, quando un elemento diventa attivo, le dimensioni sono leggermente aumentate e vengono elevate visivamente utilizzando un'ombreggiatura. 

Se l'utente esegue un movimento lento e circolare sul Siri remoto, l'elemento con lo stato attivo verrà influenzato in tempo reale in risposta a questo movimento. Quando si verifica il Sway, viene applicata una lucentezza illuminata all'immagine che rende visibile la superficie. Dopo un determinato periodo di inattività, qualsiasi contenuto fuori dallo stato di inattività viene offuscato e l'elemento con lo stato attivo aumenta ulteriormente. 

Questi effetti interagiscono tra loro per fornire una connessione mentale tra il contenuto sullo schermo TV e l'interazione dell'utente con Apple TV dal divano.

In Apple TV questo effetto di parallasse viene usato in tutto il sistema per fornire un senso di profondità e dinamiche 3D a elementi concentrati. tvOS usa una serie di immagini trasparenti e [sovrapposte](~/ios/tvos/app-fundamentals/icons-images.md#Layered-Images) che vengono spostate e ridimensionate dinamicamente per creare questo effetto.

Le immagini sovrapposte sono necessarie per l'icona dell'app tvOS ed è supportata per il contenuto dinamico top shelf. Sebbene non sia obbligatorio, Apple consiglia vivamente di implementare immagini a più livelli in qualsiasi altro elemento attivabile nell'interfaccia utente dell'app.

### <a name="enabling-parallax"></a>Abilitazione di parallasse

Il `UIImageView` controllo (o qualsiasi controllo che eredita da `UIImageView`) supporta automaticamente l'effetto parallasse. Per impostazione predefinita, questo supporto è disabilitato, per abilitarlo, usare il codice seguente:

```csharp
myImageView.AdjustsImageWhenAncestorFocused = true;
```

Se questa proprietà è impostata `true`su, la visualizzazione immagine otterrà automaticamente l'effetto di parallasse quando viene selezionato dall'utente e nello stato attivo.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato il concetto di messa a fuoco e il modo in cui viene usato per gestire la navigazione nell'interfaccia utente di un'app Novell. tvOS. Viene esaminato il modo in cui i controlli di navigazione tvOS incorporati utilizzano lo stato attivo, l'evidenziazione e la selezione per fornire la navigazione. Successivamente, è stato esaminato il modo in cui è possibile utilizzare lo stato attivo con Parallax e le immagini sovrapposte per fornire agli utenti finali indizi visivi per lo stato di navigazione corrente. Infine, ha esaminato il lavoro con lo stato attivo, gli aggiornamenti, lo stato attivo nelle raccolte e l'abilitazione di parallasse.




## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guide all'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione delle app per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
