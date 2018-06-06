---
title: Utilizzo di tvOS navigazione e lo stato attivo in Xamarin
description: Questo articolo descrive il concetto di messa a fuoco e come viene utilizzato per presentare e gestire la navigazione all'interno di un'app Xamarin.tvOS.
ms.prod: xamarin
ms.assetid: DD72E95F-AE9B-47D2-B132-5FA5FBD8026E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 2af3306b605ee802b72b159d5f2759d71d292a64
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788732"
---
# <a name="working-with-tvos-navigation-and-focus-in-xamarin"></a>Utilizzo di tvOS navigazione e lo stato attivo in Xamarin

_Questo articolo descrive il concetto di messa a fuoco e come viene utilizzato per presentare e gestire la navigazione all'interno di un'app Xamarin.tvOS._


Questo articolo descrive il concetto di [messa a fuoco](#Focus-and-Selection) e come viene utilizzata per gestire [navigazione](#Navigation) nell'interfaccia utente dell'applicazione un Xamarin.tvOS. Verrà esaminato il tvOS predefinito dei controlli di spostamento come utilizzare lo stato attivo, evidenziazione e selezione per lo spostamento di interfaccia utente dell'applicazione Xamarin.tvOS.

[![](navigation-focus-images/intro01.png "App tvOS navigazione dell'interfaccia utente")](navigation-focus-images/intro01.png#lightbox)

Successivamente, si verrà esaminato un utilizzo con lo stato attivo [parallasse](#Focus-and-Parallax) e *immagini a più livelli* per fornire indizio visivo per lo stato di navigazione corrente per l'utente finale.

Infine, vedremo durante l'utilizzo di [lo stato attivo](#Working-with-Focus), [gli aggiornamenti dello stato attivo](#Working-with-Focus-Updates), [messa a fuoco Guide](#Working-with-Focus-Guides), [lo stato attivo in raccolte](#Working-with-Focus-in-Collections) e [ Abilitazione di parallasse](#Enabling-Parallax) nelle viste di immagine nelle App Xamarin.tvOS.

<a name="Navigation" />

## <a name="navigation"></a>Navigazione

Gli utenti dell'app Xamarin.tvOS verranno non essere interazione con relativa interfaccia direttamente come con iOS in cui vengono toccati immagini sullo schermo del dispositivo, ma indirettamente da tra la sala utilizzando il [Siri remoto](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote). È necessario tenere presenti quando si progetta l'interfaccia utente dell'applicazione in modo che naturalmente flussi, ma consente all'utente di immerso dell'esperienza di Apple TV.

Un'app tvOS ha esito positivo implementa navigazione in modo che supporti agevolmente lo scopo dell'app e la struttura dei dati che si presenta senza richiamare l'attenzione sulle stesso riquadro di spostamento. Progettare l'esplorazione del sito in modo che sembra naturale e familiare senza che dominano l'interfaccia utente o lo stato attivo dal contenuto e l'esperienza utente di App di disegno.

[![](navigation-focus-images/nav01.png "L'app impostazioni tvOS")](navigation-focus-images/nav01.png#lightbox)

Durante l'utilizzo in genere un Apple TV, l'utente si sposta attraverso una serie in pila di schermate, ogni presentazione di un determinato set di contenuto. A sua volta, ogni nuova schermata può causare uno o più schermi secondari del contenuto con i controlli dell'interfaccia utente standard, ad esempio [pulsanti](~/ios/tvos/user-interface/buttons.md), [scheda barre](~/ios/tvos/user-interface/tab-bars.md), tabelle, [viste di raccolta](~/ios/tvos/user-interface/collection-views.md) o [ Doppia visualizzazione](~/ios/tvos/user-interface/split-views.md).

Con ogni nuova schermata di dati, l'utente passa più approfondito in questo stack delle schermate. Tramite il **Menu** pulsante su Siri remoto, è possibile accedere con le versioni precedenti attraverso lo stack per tornare alla schermata precedente o un Menu principale.

Apple suggerisce tenendo presente quanto segue quando si progetta la navigazione per l'app tvOS:

- **Layout la navigazione per effettuare la ricerca rapida contenuto e facile** -da utenti di accedere al contenuto all'interno dell'app nel minor numero di elementi di ritardo, fa clic su e passa possibili. Semplificare la navigazione e organizzare il contenuto al numero minimo di schermate.
- **Creare un'interfaccia fluido tramite tocco** -verificare che un utente può spostarsi tra _elementi con stato attivabile_ con le forze di attrito minimo usando il minor numero di possibili movimenti.
- **Progettazione con lo stato attivo presente** -poiché l'utente interagisce con il contenuto attraverso la chat, necessarie per spostare lo stato attivo per un elemento dell'interfaccia utente prima di poter interagire con remoto Siri. Gli utenti riceveranno sempre con l'app se è necessario un numero eccessivo movimenti per poter raggiungere gli obiettivi prefissati.
- **Fornire la navigazione tramite il pulsante di Menu** : per creare un'esperienza semplice e intuitivo, consentire agli utenti di spostarsi all'indietro utilizzando il Siri Remote **Menu** pulsante. Premendo il **Menu** pulsante deve sempre restituire alla schermata precedente o tornare al Menu principale dell'applicazione. Al livello principale dell'app, premendo il **Menu** pulsante deve tornare alla schermata Home Apple TV.
- **In genere si consiglia di evitare la visualizzazione di un pulsante Indietro** - perché premendo il **Menu** pulsante remoto Siri si sposta all'indietro attraverso lo stack di schermata, evitare la visualizzazione di un controllo aggiuntivo che consente di duplicare questo comportamento. Un'eccezione a questa regola è per l'acquisto di schermate o schermate con le azioni di distruzione (ad esempio l'eliminazione del contenuto) in cui un **Annulla** pulsante deve essere visualizzato anche.
- **Visualizzare raccolte di grandi dimensioni in una singola schermata, anziché molti** -è stato progettato per spostarsi all'interno di un'ampia raccolta di contenuto rapido e semplice utilizzando i movimenti di Siri remoto. Se l'app funziona con un'ampia raccolta di elementi con stato attivabile, prendere in considerazione mantenendoli su una singola schermata anziché relativa interruzione in molte schermate che richiedono una navigazione più parte dell'utente.
- **Utilizzare i controlli Standard per la navigazione** -nuovamente, per creare un'esperienza utente semplice e intuitivo, laddove possibile, utilizzare incorporato `UIKit` controlli, ad esempio pagina controlli, le barre di scheda, segmentati, viste delle tabelle, viste di raccolta e divisione Viste per la navigazione dell'applicazione. Poiché l'utente ha già familiarità con questi elementi, in modo intuitivo saranno in grado di individuare l'app.
- **Ottimizza per la navigazione nel contenuto orizzontale** -a causa della natura della Apple TV, passaggio da sinistra a destra in remoto Siri è più naturale rispetto su e giù. Prendere in considerazione questa opzione quando si progetta il layout del contenuto per l'app.

<a name="Focus-and-Selection" />

## <a name="focus-and-selection"></a>Lo stato attivo e la selezione

In Apple TV, un'immagine, pulsante o altri elementi dell'interfaccia utente viene considerato _attivo_ quando è la destinazione della navigazione corrente.

[![](navigation-focus-images/focus01.png "Esempio di selezione e lo stato attivo")](navigation-focus-images/focus01.png#lightbox)

A differenza di, i dispositivi iOS in cui l'utente è interagire direttamente con gli elementi su touchscreen del dispositivo, gli utenti interagiscono con tvOS gli elementi da parte della stanza utilizzando remoto Siri. Per gestire questa interazione utente presenti, Apple TV utilizza un _messa a fuoco_ basato sul modello.

Utilizzando i movimenti e pulsante preme il [Siri remoto](~/ios/tvos/platform/remote-bluetooth.md#The-Siri-Remote), l'utente sposta lo stato attivo da un elemento dell'interfaccia utente a un altro. Dopo aver spostato lo stato attivo per l'elemento desiderato, l'utente fa clic per selezionare il contenuto o attivare l'azione rappresentato da tale elemento.

Quando cambia lo stato attivo, le animazioni complessi ed effetti (ad esempio, l'effetto di parallasse sulle immagini) vengono utilizzati per identificare chiaramente l'elemento dell'interfaccia utente che ha attualmente lo stato attivo.

Apple ha i seguenti suggerimenti per l'utilizzo di selezione e lo stato attivo:

- **Utilizzare i controlli dell'interfaccia utente predefinite per gli effetti di movimento** - tramite `UIKit` e l'API di stato attivo nell'interfaccia utente, il modello di stato attivo verrà applicato automaticamente il movimento predefinito ed effetti visivi di elementi dell'interfaccia utente. Questo rende l'app è nativo e familiari agli utenti della piattaforma Apple TV e per lo spostamento semplice e intuitivo tra gli elementi con stato attivabile.
- **Spostare lo stato attivo in direzioni previsto** -su Apple TV, quasi tutti gli elementi utilizza Modifica indiretta. Ad esempio, l'utente utilizza remoto Siri per spostare lo stato attivo e il sistema scorre automaticamente l'interfaccia per mantenere visibile l'elemento attualmente attivato. Se l'app implementa questo tipo di interazione, verificare che lo stato attivo si sposta nella direzione del movimento dell'utente. Pertanto, se l'utente scorrere verso destra nello stato attivo Siri remoto deve spostare a destra (che potrebbe causare la schermata per lo scorrimento a sinistra). L'unica eccezione a questa regola è a schermo intero elementi che utilizzano la manipolazione diretta (dove passaggio backup Sposta l'elemento verso l'alto).
- **Verificare che l'elemento con stato attivo è Obvious** -perché gli utenti sono l'interazione con elementi dell'interfaccia utente, è importante che l'elemento attivo è l'acronimo. In genere questa operazione verrà gestita automaticamente mediante predefinito `UIKit` elementi. Per i controlli personalizzati, usare funzionalità come dimensione dell'elemento o dell'ombreggiatura per mostrare lo stato attivo.
- **Utilizzare parallasse per rendere attivo elementi reattiva** : piccola, circolari movimenti su remoto Siri comportare presentato in tempo reale lo spostamento dell'elemento con lo stato attivo. Questo [effetto di parallasse](#Focus-and-Parallax) è incorporata in `UIKit` _immagini a più livelli_ per consentire all'utente un'idea della connessione per l'elemento con lo stato attivo.
- **Creare gli elementi con stato attivabile delle dimensioni Appropriate** -elementi di grandi dimensioni con spaziatura ampiamente sono più semplici da selezione e lo spostamento di elementi più piccoli.
- **Progettare l'elemento dell'interfaccia utente per l'aspetto ottimale sia con stato attivo o Unfocused** -in genere Apple TV rappresenta l'elemento con stato attivo mediante l'aumento delle dimensioni. Verificare che gli elementi dell'interfaccia utente dell'App Cerca ideale per qualsiasi dimensione di presentazione e, se necessario, forniscono risorse per gli elementi di dimensioni maggiori.
- **Rappresentano le modifiche dello stato attivo senza problemi** -usare l'animazione di dissolvenza in modo uniforme tra gli elementi di un **Focused** e **Unfocused** per mantenere le transizioni vengano jarring.
- **Non visualizzare un cursore** -gli utenti si aspettano passare l'interfaccia utente dell'applicazione utilizzando lo stato attivo e non tramite lo spostamento di un cursore sullo schermo. In un'interfaccia utente è consigliabile usare sempre il modello di stato attivo per offrire un'esperienza utente coerente.

<a name="Working-with-Focus" />

### <a name="working-with-focus"></a>Utilizzo con lo stato attivo

Talvolta potrebbe essere che si desidera creare un controllo personalizzato che può diventare un elemento con stato attivabile. Se pertanto eseguire l'override di `CanBecomeFocused` proprietà e restituire `true`, altrimenti restituire `false`. Ad esempio:

```csharp
public class myView : UIView
{
    public override bool CanBecomeFocused {
        get {return true;}
    }
}
```

In qualsiasi momento è possibile utilizzare il `Focused` proprietà di un `UIKit` controllo per verificare se è l'elemento corrente. Se `true` l'elemento dell'interfaccia utente ha attualmente lo stato attivo, in caso contrario non è presente. Ad esempio:

```csharp
// Is my view in focus?
if (myView.Focused) {
    // Do something
    ...
}
```

Mentre direttamente, è possibile spostare lo stato attivo a un altro elemento dell'interfaccia utente tramite codice, è possibile specificare quale elemento dell'interfaccia utente innanzitutto di ottenere lo stato attivo quando viene caricata una schermata impostando il relativo `PreferredFocusedView` proprietà `true`. Ad esempio:

```csharp
// Make the play button the starting focus item
playButton.PreferredFocusedView = true;
```

<a name="Working-with-Focus-Updates" />

### <a name="working-with-focus-updates"></a>Utilizzo con gli aggiornamenti dello stato attivo 

Quando l'utente fa sì che lo stato attivo passa da un elemento dell'interfaccia utente a un altro (ad esempio, in risposta a un'azione in remoto Siri) un _evento di aggiornamento dello stato attivo_ verranno inviati per l'elemento perde lo stato attivo e l'elemento ottenga lo stato attivo.

Per gli elementi personalizzati che ereditano da `UIView` o `UIViewController`, è possibile eseguire l'override di diversi metodi per lavorare con l'evento di aggiornamento dello stato attivo:

- **DidUpdateFocus** -questo metodo verrà chiamato ogni volta che la vista acquisisce o perde lo stato attivo.
- **ShouldUpdateFocus** -usare questo metodo per definire in cui lo stato attivo è consentito spostare.

Per richiedere che il motore lo stato attivo si sposta lo stato attivo per eseguire il backup per il `PreferredFocusedView` elemento dell'interfaccia utente, chiamata di `SetNeedsUpdateFocus` metodo del Controller di visualizzazione.

> [!IMPORTANT]
> La chiamata `SetNeedsUpdateFocus` ha effetto solo se il Controller di visualizzazione viene richiamata in contiene la vista che ha attualmente lo stato attivo.




<a name="Working-with-Focus-Guides" />

### <a name="working-with-focus-guides"></a>Utilizzo di guide lo stato attivo

Il motore di messa a fuoco incorporata tvOS è molto utile nella gestione dei movimenti tra gli elementi che rientrano in una griglia orizzontale e verticale. In genere, è necessario consentono di aggiungere che elementi dell'interfaccia utente per la progettazione dell'interfaccia e il motore di messa a fuoco gestirà automaticamente lo spostamento tra gli elementi senza l'intervento di uno sviluppatore.

Tuttavia, potrebbe esserci volte, a causa di qualsiasi cosa per il di progettazione dell'interfaccia utente, quando non rientrano in una griglia orizzontale e verticale di elementi dell'interfaccia utente e potrebbero essere inaccessibili perché sono diagonale tra loro. Ciò accade perché il motore lo stato attivo è stato progettato per gestire semplice verso l'alto, verso il basso, lo spostamento di sinistra e destra tra solo gli elementi dell'interfaccia utente.

Eseguire il layout dell'interfaccia utente di seguenti per un esempio:

 [![](navigation-focus-images/guide01.png "Utilizzo di esempio guide lo stato attivo")](navigation-focus-images/guide01.png#lightbox)
 
Poiché il **informazioni** pulsante non rientra in una griglia orizzontale e verticale con il **acquistare** pulsante non sarebbe accessibile all'utente. Tuttavia, questo può essere facilmente corretto utilizzando un _Guida messa a fuoco_ di fornire suggerimenti di spostamento al motore di stato attivo. 

Una Guida di messa a fuoco (`UIFocusGuide`) espone un'area non visibile della vista come Focusable al motore di messa a fuoco, consentendo in tal modo lo stato attivo per essere reindirizzati a un'altra visualizzazione.

In tal caso, per risolvere l'esempio sopra riportato, Impossibile aggiungere il codice seguente al Controller di visualizzazione per creare una Guida di messa a fuoco tra il **informazioni** e **acquistare** pulsanti:

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

Innanzitutto, un nuovo `UIFocusGuide` viene creato e aggiunto alla raccolta della visualizzazione della Guida tramite il `AddLayoutGuide` metodo.

Successivamente, la Guida di messa a fuoco Top, Left, larghezza e altezza ancoraggi vengono modificati rispetto al **informazioni** e **acquistare** pulsanti per posizionarla tra di essi. Vedere:

[![](navigation-focus-images/guide02.png "Guida di esempio lo stato attivo")](navigation-focus-images/guide02.png#lightbox)

È inoltre importante notare che i nuovi vincoli attivati quando vengono create impostando i relativi `Active` proprietà `true`:

```csharp
FocusGuide.LeftAnchor.ConstraintEqualTo (...).Active = true;
```

Con la nuova Guida di messa a fuoco stabilito e aggiunte alla vista, il Controller di visualizzazione `DidUpdateFocus` metodo può essere sottoposto a override e aggiungere il codice seguente per lo spostamento tra le **informazioni** e **acquistare** pulsanti:

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

Innanzitutto, questo get di codice il `NextFocusedView` dal `UIFocusUpdateContext` che sono state passate (`context`). In questa visualizzazione è `null`, non è necessaria alcuna elaborazione e il metodo è stato chiuso.

Successivamente, il `nextFocusableItem` viene valutata. Se corrisponde a uno di **informazioni** o **acquistare** pulsanti, lo stato attivo viene inviato al pulsante opposto di utilizzare la Guida di messa a fuoco `PreferredFocusedView` proprietà. Ad esempio:

```csharp
// Move from the More Info to Buy button
FocusGuide.PreferredFocusedView = BuyButton;
```

Nel caso in cui nessun pulsante rappresenta l'origine del turno, lo stato attivo di `PreferredFocusedView` proprietà è deselezionata:

```csharp
// No valid move
FocusGuide.PreferredFocusedView = null;
```

<a name="Working-with-Focus-in-Collections" />

### <a name="working-with-focus-in-collections"></a>Utilizzo con lo stato attivo in raccolte

Quando si decide se un singolo elemento può essere attivabile in un `UICollectionView` o `UITableView`, verranno eseguire l'override di metodi del `UICollectionViewDelegate` o `UITableViewDelegate` rispettivamente. Ad esempio:

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

Il `CanFocusItem` restituisce `true` se l'elemento corrente può essere attivo, altrimenti restituisce `false`.

Se si desidera un `UICollectionView` o `UITableView` per ricordare e ripristinare lo stato attivo all'ultimo elemento quando perde e riassume lo stato attivo, impostare il `RemembersLastFocusedIndexPath` proprietà `true`.

<a name="Focus-and-Parallax" />

## <a name="focus-and-parallax"></a>Lo stato attivo e parallasse

Come descritto in precedenza, un elemento dell'interfaccia utente viene considerato _attivo_ quando è l'elemento corrente durante un evento di navigazione. In genere, come un elemento viene messa a fuoco, la dimensione è leggermente aumentata e che venga elevato visivamente utilizzando un'ombreggiatura. 

Se l'utente esegue un movimento lento, circolare in remoto Siri, l'elemento con stato attivo verrà sway in tempo reale in risposta a questo movimento. Si verifica l'oscillazione, viene applicata un' brillantezza illuminato alla propria immagine visualizzando l'area da utilizzare. Dopo un determinato periodo di inattività, l'elemento Focused aumenteranno anche offusca qualsiasi contenuto out-di-lo stato attivo. 

Questi effetti interagiscono per fornire una connessione tra il contenuto sullo schermo TV e l'utente interagisce con Apple TV dal divano mentale.

In Apple TV, questo effetto di parallasse viene utilizzato in tutto il sistema per trasmettere un senso di profondità 3D e dynamics per gli elementi di messa a fuoco. tvOS Usa una serie di trasparente, [immagini a più livelli](~/ios/tvos/app-fundamentals/icons-images.md#Layered-Images) che sposta e si adatta dinamicamente per creare questo effetto.

Immagini a più livelli sono necessari per l'icona dell'app tvOS ed è supportata per il contenuto dinamico scaffale superiore. Sebbene non sia necessario, Apple suggerisce fortemente l'implementazione di immagini a più livelli in tutti gli elementi con stato attivabile interfaccia utente dell'applicazione.

### <a name="enabling-parallax"></a>Abilitazione di parallasse

Il `UIImageView` controllo (o qualsiasi controllo che eredita da `UIImageView`) supporta automaticamente l'effetto di parallasse. Per impostazione predefinita, questo supporto è disabilitato, abilitarlo, utilizzare il codice seguente:

```csharp
myImageView.AdjustsImageWhenAncestorFocused = true;
```

Con questa proprietà è impostata su `true`, la visualizzazione dell'immagine viene automaticamente visualizzato l'effetto di parallasse quando viene selezionato dall'utente e lo stato attivo.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è illustrati il concetto di messa a fuoco e come viene utilizzata per gestire la navigazione nell'interfaccia utente dell'applicazione un Xamarin.tvOS. Esso esaminare come il tvOS predefinito dei controlli di spostamento utilizzare lo stato attivo, evidenziazione e selezione di esplorazione. Successivamente, esaminato lo stato attivo come utilizzare con parallasse e immagini a più livelli per fornire indizio visivo per lo stato di navigazione corrente per l'utente finale. Infine, esaminato lavoro con lo stato attivo, gli aggiornamenti dello stato attivo, lo stato attivo in raccolte e abilitazione di parallasse.




## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Guide interfaccia umana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
