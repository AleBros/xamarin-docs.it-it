---
title: Storyboard unificati
description: "Gli storyboard unificati consentono iOS allo sviluppatore di creare l'interfaccia utente con un unico storyboard, anziché più storyboard, per coprire l'intervallo di espansione delle dimensioni dello schermo di dispositivi. In questo articolo è progettato per offrire una panoramica più approfondita per l'esecuzione dello storyboard unificato all'interno di xamarin. IOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: F6F70374-FC2A-4401-A712-A16D0F9B340F
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/20/2017
ms.openlocfilehash: 60b2e6fa65226631fe2d2c847a56852ac9ae63d2
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2018
---
# <a name="unified-storyboards"></a>Storyboard unificati

_Gli storyboard unificati consentono iOS allo sviluppatore di creare l'interfaccia utente con un unico storyboard, anziché più storyboard, per coprire l'intervallo di espansione delle dimensioni dello schermo di dispositivi. In questo articolo è progettato per offrire una panoramica più approfondita per l'esecuzione dello storyboard unificato all'interno di xamarin. IOS._

iOS 8 include un meccanismo di nuovo e più semplice da utilizzare per la creazione dell'interfaccia utente, ovvero lo storyboard unificato. Con un unico storyboard per includere tutte le dimensioni dello schermo di hardware diverso, è possibile creare visualizzazioni veloce e reattive una "struttura-una volta, utilizzare-molti" stile.

Quando lo sviluppatore non sono più necessari creare uno storyboard separato e specifico per i dispositivi iPhone e iPad, hanno la possibilità di progettare le applicazioni con un'interfaccia comune e quindi personalizzare tale interfaccia per le classi di dimensioni diverse. In questo modo, un'applicazione può essere adattata a punti di forza di ogni fattore di forma e ogni interfaccia utente può essere ottimizzata per offrire la migliore esperienza.

<a name="size-classes" />

## <a name="size-classes"></a>Classi di dimensione

Prima di iOS 8, usato dallo sviluppatore `UIInterfaceOrientation` e `UIInterfaceIdiom` per distinguere tra modalità verticale e orizzontale e tra i dispositivi iPhone e iPad. In 8, dispositivo e l'orientamento viene determinato utilizzando *classi*.

I dispositivi sono definiti da classi di dimensione, negli assi orizzontali e verticali, e sono disponibili due tipi di classi di dimensioni in iOS 8:

-  **Regolare** : si tratta di una dimensione di schermi di grandi dimensioni (ad esempio un iPad) o un gadget che dà l'impressione di grandi dimensioni (ad esempio un `UIScrollView`
-  **Compact** : si tratta di dispositivi di piccole dimensioni (ad esempio un iPhone). Questa dimensione prende in considerazione l'orientamento del dispositivo.


Se i due concetti sono utilizzati insieme, il risultato è una 2x2 griglia che definisce le diverse dimensioni possibili che possono essere usate in entrambi gli orientamenti diversi, come illustrato nel diagramma seguente:

 [![](unified-storyboards-images/sizeclassgrid.png "Una 2x2 griglia che definisce le diverse dimensioni possibili che possono essere usate in orientamenti normali e di Compact")](unified-storyboards-images/sizeclassgrid.png#lightbox)

Lo sviluppatore può creare un Controller di visualizzazione che usa una qualsiasi delle quattro possibilità comporta diversi layout (come illustrato nell'immagine precedente).

### <a name="ipad-size-classes"></a>Classi di dimensione iPad

IPad, a causa della dimensione, ha un **regolare** dimensioni per entrambi gli orientamenti di classe.

 [![](unified-storyboards-images/image1.png "Classi di dimensione iPad")](unified-storyboards-images/image1.png#lightbox)


### <a name="iphone-size-classes"></a>Classi di dimensione iPhone

IPhone include classi di dimensioni diverse in base all'orientamento del dispositivo:

 [![](unified-storyboards-images/iphonesizeclasses.png "Classi di dimensione iPhone")](unified-storyboards-images/iphonesizeclasses.png#lightbox)

-  Quando il dispositivo è in modalità verticale, la schermata include un **compact** classe orizzontalmente e **regolare** verticalmente
-  Quando il dispositivo è in modalità orizzontale, le classi dello schermo vengono invertite dalla modalità verticale.

### <a name="iphone-6-plus-size-classes"></a>Classi di dimensione iPhone 6 Plus

Le dimensioni sono le stesse l'iPhone precedenti in orientamento verticale, ma è diversa in formato orizzontale:

[![](unified-storyboards-images/iphone6sizeclasses.png "Classi di dimensione iPhone 6 Plus")](unified-storyboards-images/iphone6sizeclasses.png#lightbox)

Poiché l'iPhone 6 Plus ha una schermata sufficientemente grande, è possibile disporre di una classe di dimensione regolare la larghezza in modalità orizzontale.

### <a name="support-for-a-new-screen-scale"></a>Supporto per la scala di una schermata nuova

IPhone 6 Plus Usa un nuovo display Retina HD con un fattore di scala schermata di 3.0 (tre volte l'originale iPhone risoluzione dello schermo). Per fornire la migliore esperienza possibile in questi dispositivi, includere un nuovo disegno progettato per questa scala della schermata. In Xcode 6 e versioni successive, asset cataloghi possono includere immagini 1 x, x 2 e 3 dimensioni x. è sufficiente aggiungere il nuovo Asset di immagini e iOS sceglierà l'asset corretto durante l'esecuzione in un iPhone 6 Plus.

L'immagine durante il caricamento di comportamento in iOS anche riconosce un `@3x` suffisso nel file di immagine. Ad esempio, se lo sviluppatore include un asset di immagine (con diverse risoluzioni) nel pacchetto dell'applicazione con i nomi di file seguenti: `MonkeyIcon.png`, `MonkeyIcon@2x.png`, e `MonkeyIcon@3x.png`. In iPhone 6 Plus il `MonkeyIcon@3x.png` immagine verrà utilizzata automaticamente se lo sviluppatore carica un'immagine utilizzando il codice seguente:

```csharp
UIImage icon = UIImage.FromFile("MonkeyImage.png");
```
O se si assegna l'immagine a un elemento dell'interfaccia utente utilizzando iOS della finestra di progettazione come `MonkeyIcon.png`, `MonkeyIcon@3x.png` verrà utilizzato, automaticamente, in iPhone 6 Plus.

<a name="dynamic-launch-screens" />

### <a name="dynamic-launch-screens"></a>Schermate di avvio dinamico

File della schermata di avvio viene visualizzato come una schermata durante l'avvio di un'applicazione iOS per fornire commenti e suggerimenti per l'utente che l'app è effettivamente avviamento. Prima di iOS 8, lo sviluppatore è più `Default.png` asset per ogni tipo, l'orientamento e schermata la risoluzione del dispositivo che l'applicazione verrebbe eseguito su immagine.

Nuovo in iOS 8, lo sviluppatore può creare un singolo atomico `.xib` file in Xcode che usa Layout automatico e le classi di dimensione per creare un *dinamico della schermata avvio* che funzionerà per tutti i dispositivi, risoluzione e l'orientamento. Questo non solo riduce la quantità di lavoro necessaria per lo sviluppatore di creare e gestire tutti gli asset di immagini necessarie, ma riduce le dimensioni del pacchetto installato dell'applicazione.

## <a name="traits"></a>Tratti

Tratti sono proprietà che può essere utilizzata per determinare come un layout cambia come modifiche apportate al relativo ambiente. Sono costituite da un set di proprietà (il `HorizontalSizeClass` e `VerticalSizeClass` in base a `UIUserInterfaceSizeClass`), nonché il linguaggio di interfaccia ( `UIUserInterfaceIdiom`) e la scala di visualizzazione.

Tutti gli stati sopra sono incapsulati in un contenitore che si intende Apple come una raccolta di tratto ( `UITraitCollection`), che contiene non solo le proprietà, ma anche i relativi valori.

## <a name="trait-environment"></a>Ambiente tratto

Ambienti tratto sono una nuova interfaccia in iOS 8 e sono in grado di restituire una raccolta di tratto per gli oggetti seguenti:

-  Schermate ( `UIScreens` ).
-  Windows ( `UIWindows` ).
-  Visualizzare i controller ( `UIViewController` ).
-  Viste ( `UIView` ).
-  Controller di presentazione ( `UIPresentationController` ).


Lo sviluppatore utilizza la raccolta di tratto restituito da un ambiente di tratto per determinare come deve essere disposto un'interfaccia utente.

Tutti gli ambienti tratto di creare una gerarchia, come illustrato nel diagramma seguente:

 [![](unified-storyboards-images/viewhierarchy.png "Il diagramma della gerarchia ambienti tratto")](unified-storyboards-images/viewhierarchy.png#lightbox)

La raccolta di tratto ognuno degli ambienti di tratto precedente con flusso, per impostazione predefinita, dall'elemento padre per l'ambiente figlio.

Oltre a ottenere la raccolta di tratto corrente, l'ambiente tratto dispone di un `TraitCollectionDidChange` metodo, che può essere sottoposto a override in sottoclassi di Controller di visualizzazione o una vista. Lo sviluppatore può utilizzare questo metodo per modificare uno degli elementi dell'interfaccia utente che dipendono da tratti quando tali caratteri sono stati modificati.

## <a name="typical-trait-collections"></a>Raccolte di tratto tipico

In questa sezione illustra i tipi di raccolte di tratto che si verifica quando si lavora con iOS 8 tipico.

Di seguito è una raccolta di tratto tipico che lo sviluppatore può vedere in un iPhone:

|Proprietà|Valore|
|--- |--- |
|`HorizontalSizeClass`|Compact|
|`VerticalSizeClass`|Regular|
|`UserInterfaceIdom`|Telefono|
|`DisplayScale`|2.0|

Il set precedente rappresenterebbe una completamente completo tratto di raccolta, perché contiene i valori per tutte le relative proprietà tratto.

È anche possibile disporre di una raccolta di tratto che mancano alcuni dei relativi valori (che si intende Apple come *Unspecified*):

|Proprietà|Valore|
|--- |--- |
|`HorizontalSizeClass`|Compact|
|`VerticalSizeClass`|Non specificato|
|`UserInterfaceIdom`|Non specificato|
|`DisplayScale`|Non specificato|

In genere, tuttavia, quando lo sviluppatore richiede il tratto di ambiente per la relativa raccolta tratto, verrà restituito un insieme completo come illustrato nell'esempio precedente.

Se un tratto di ambiente (ad esempio, una vista o View Controller) non è presente all'interno della gerarchia di visualizzazione corrente, lo sviluppatore potrebbe ottenere nuovamente i valori non specificati per uno o più delle proprietà del tratto.

Lo sviluppatore potranno inoltre ottenere una raccolta di tratto parzialmente qualificati se usano uno dei metodi di creazione forniti da Apple, ad esempio `UITraitCollection.FromHorizontalSizeClass`, per creare una nuova raccolta.

Un'operazione che può essere eseguita in più raccolte di tratto è confrontarli tra loro, che implica che richiede una raccolta di tratto se contiene un altro. Cosa si intende per *contenimento* è che, per qualsiasi tratto specificato nella seconda raccolta, il valore deve corrispondere esattamente con il valore nella prima raccolta.

Per eseguire il test utilizzano due tratti il `Contains` metodo il `UITraitCollection` passando il valore del tratto da testare.

Lo sviluppatore può eseguire i confronti manualmente nel codice per determinare come viste di layout o controller di visualizzazione. Tuttavia, `UIKit` utilizzato internamente questo metodo per fornire alcune delle proprie funzionalità, come il Proxy di aspetto, ad esempio.

## <a name="appearance-proxy"></a>Aspetto Proxy

Il Proxy di aspetto è stata introdotta in versioni precedenti di iOS per consentire agli sviluppatori di personalizzare le proprietà di osservazioni. IOS 8 per supportare le raccolte di tratto estesi.

Aspetto proxy includono ora un nuovo metodo `AppearanceForTraitCollection`, che restituisce un nuovo aspetto di Proxy per la raccolta specificata di tratto che è stato passato. Tutte le personalizzazioni che viene eseguita su tale Proxy aspetto diventeranno effettive solo nelle viste che è conforme alla raccolta di tratto specificati.

In genere passerà in una raccolta di tratto parzialmente specificato per lo sviluppatore di `AppearanceForTraitCollection` metodo, ad esempio quello appena specificato orizzontale dimensioni classe di compatta, in modo che sia possibile la personalizzazione di qualsiasi visualizzazione nell'applicazione è compact in orizzontale.

## <a name="uiimage"></a>UIImage

Un'altra classe che ha aggiunto Apple raccolta tratto è `UIImage`. In passato lo sviluppatore deve specificare un @1X e @2x versione di un asset grafico bitmap che avverrebbe da includere nell'applicazione (ad esempio, un'icona).

iOS 8 è stato espanso per consentire agli sviluppatori di includere più versioni di un'immagine in un catalogo di immagini basato su una raccolta di tratto. Ad esempio, lo sviluppatore può includere un'immagine più piccola per l'utilizzo di una classe tratto Compact e un'immagine di dimensione completa per qualsiasi altra raccolta.

Quando una delle immagini viene utilizzata all'interno di un `UIImageView` (classe), la visualizzazione di immagini visualizzano automaticamente la versione corretta dell'immagine per la relativa raccolta tratto. Se l'ambiente tratto cambia (ad esempio il cambio utente il dispositivo da verticale a orizzontale), la visualizzazione di immagini selezionerà automaticamente le nuove dimensioni dell'immagine per associare la nuova raccolta tratto e modificarne le dimensioni affinché corrisponda a quello della versione corrente di cui l'immagine da visualizzato.

## <a name="uiimageasset"></a>UIImageAsset

Apple ha aggiunto una nuova classe per iOS 8 denominata `UIImageAsset` per fornire lo sviluppatore di un maggiore controllo sulla selezione di immagini.

Un Asset immagine esegue il wrapping di tutte le versioni diverse di un'immagine e consente allo sviluppatore richiedere un'immagine specifica che corrisponde a una raccolta di tratto che è stato passato. Le immagini possono essere aggiunti o rimossi da una risorsa immagine, in tempo reale.

Per ulteriori informazioni sulle risorse di immagini, vedere Apple [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset) documentazione.

## <a name="combining-trait-collections"></a>Combinazione di raccolte di tratto

Un'altra funzione che uno sviluppatore può eseguire sulle raccolte tratto è per sommare due che produce l'insieme, in cui i valori da una raccolta non specificati vengono sostituiti dai valori specificati in un secondo. Questa operazione viene eseguita utilizzando il `FromTraitsFromCollections` metodo la `UITraitCollection` classe.

Come descritto in precedenza, se uno qualsiasi dei tratti non è specificato in una delle raccolte tratto e viene specificato in un altro, il valore verrà impostato per la versione specificata. Tuttavia, se sono presenti più versioni di un valore specificato, il valore dall'ultima raccolta tratto sarà il valore che viene utilizzato.

## <a name="adaptive-view-controllers"></a>Visualizzazione adattivo controller

In questa sezione illustra i dettagli della modalità iOS visualizzazione e controller di visualizzazione hanno adottato i concetti di tratti e classi di dimensione per essere automaticamente più adattiva in applicazioni dello sviluppatore.

### <a name="split-view-controller"></a>Divisione View Controller

Una delle classi di visualizzazione Controller che è stata modificata la maggior parte in iOS 8 è il `UISplitViewController` classe. In passato, lo sviluppatore utilizzerebbe spesso una divisione View Controller alla versione iPad dell'applicazione e quindi hanno fornire una versione completamente diversa della relativa gerarchia di visualizzazione per la versione di iPhone dell'app.

In iOS 8, la `UISplitViewController` classe è disponibile in entrambe le piattaforme (iPad e iPhone), che consente agli sviluppatori di creare una gerarchia di Controller di visualizzazione che verrà utilizzato per iPhone e iPad.

Quando un iPhone è in formato orizzontale, il Controller di visualizzazione divisa presenterà relative visualizzazioni side-by-side, esattamente come quando viene visualizzato in un iPad.

### <a name="overriding-traits"></a>Si esegue l'override di tratti

Ambienti tratto catena dal contenitore padre fino a contenitori figlio, come illustrato nell'immagine seguente che mostra un Controller di visualizzazione di divisione su un iPad in orizzontale:

 [![](unified-storyboards-images/cascadingclasses01.png "Un Controller di visualizzazione divisa in un iPad in orizzontale")](unified-storyboards-images/cascadingclasses01.png#lightbox)

Poiché iPad dispone di una classe della dimensione normale in entrambi gli orientamenti orizzontali e verticali, la visualizzazione suddivisa visualizzerà viste master e di dettaglio.

In un iPhone, in cui la classe di dimensione è compact in entrambi gli orientamenti, il Controller di visualizzazione divisa Visualizza solo la visualizzazione di dettaglio, come illustrato di seguito:

 [![](unified-storyboards-images/cascadingclasses02.png "Il Controller di visualizzazione di divisione consente di visualizzare solo la visualizzazione dettagli")](unified-storyboards-images/cascadingclasses02.png#lightbox)

In un'applicazione in cui lo sviluppatore desidera visualizzazione master e dettaglio in un iPhone in orizzontale, lo sviluppatore deve inserire un contenitore padre per il Controller di visualizzazione divisa ed eseguire l'override di raccolta tratto. Come illustrato nella figura seguente:

 [![](unified-storyboards-images/cascadingclasses03.png "Lo sviluppatore deve inserire un contenitore padre per il Controller di visualizzazione divisa ed eseguire l'override di raccolta tratto")](unified-storyboards-images/cascadingclasses03.png#lightbox)

A `UIView` è impostato come elemento padre del Controller di visualizzazione di divisione e `SetOverrideTraitCollection` metodo viene chiamato per la vista passando un nuovo insieme di tratto e il Controller di visualizzazione della divisione di destinazione. La nuova raccolta tratto esegue l'override di `HorizontalSizeClass`, impostarlo su `Regular`, in modo che il Controller di visualizzazione divisa visualizzerà viste master e di dettaglio in un iPhone in orizzontale.

Si noti che il `VerticalSizeClass` è stato impostato su `unspecified`, che consente la nuova raccolta tratto da aggiungere alla raccolta tratto sull'oggetto padre, determinando un `Compact VerticalSizeClass` per l'oggetto figlio Split View Controller.

### <a name="trait-changes"></a>Modifica tratto

In questa sezione verrà esaminati, in dettaglio, come le raccolte di tratto transizione quando cambia il tratto di ambiente. Ad esempio, quando il dispositivo viene ruotato da verticale a orizzontale.

 [![](unified-storyboards-images/traittransitions01.png "Il verticale a orizzontale Panoramica modifiche tratto")](unified-storyboards-images/traittransitions01.png#lightbox)

In primo luogo, iOS 8 esegue un programma di installazione per preparare per la transizione. Successivamente, il sistema aggiunge un'animazione lo stato di transizione. Infine, iOS 8 pulisce alcuno stato temporaneo necessario durante la transizione.

iOS 8 fornisce diversi callback che lo sviluppatore può utilizzare per partecipare la modifica tratto, come illustrato nella tabella seguente:

|Phase|Callback|Descrizione|
|--- |--- |--- |
|Configurazione|<ul><li>`WillTransitionToTraitCollection`</li><li>`TraitCollectionDidChange`</li></ul>|<ul><li>Questo metodo viene chiamato all'inizio di una modifica tratto prima di un tratto Ottiene set di raccolta per il nuovo valore.</li><li>Il metodo viene chiamato quando viene modificato il valore della raccolta tratto ma prima che qualsiasi animazione viene eseguita.</li></ul>|
|Animazione|`WillTransitionToTraitCollection`|Il coordinatore di transizione che viene passato a questo metodo è un `AnimateAlongside` proprietà che consente allo sviluppatore di aggiungere le animazioni che verranno eseguite con le animazioni predefinito.|
|Pulizia|`WillTransitionToTraitCollection`|Fornisce un metodo per gli sviluppatori di includere il proprio codice di pulizia dopo la transizione.|

Il `WillTransitionToTraitCollection` metodo è molto utile per l'animazione di controller di visualizzazione insieme di modifiche di raccolte di tratto. Il `WillTransitionToTraitCollection` metodo è disponibile solo nei controller di visualizzazione ( `UIViewController`) e non su altri ambienti di tratto, ad esempio `UIViews`.

Il `TraitCollectionDidChange` è molto utile per l'utilizzo di `UIView` (classe), in cui lo sviluppatore desidera aggiornare l'interfaccia utente, come i tratti siano modificando.

### <a name="collapsing-the-split-view-controllers"></a>Comprimere il visualizzazione controller di divisione

Ora si accettano informazioni dettagliate su cosa accade quando un Controller di visualizzazione divisa comprime in una visualizzazione di una colonna da una colonna di due. Come parte di questa modifica, sono disponibili due processi che è necessario eseguire:

-  Per impostazione predefinita, il Controller di visualizzazione divisa utilizzerà il controller di visualizzazione principale della vista dopo la compressione. Lo sviluppatore può eseguire l'override di questo comportamento eseguendo l'override di `GetPrimaryViewControllerForCollapsingSplitViewController` metodo il `UISplitViewControllerDelegate` e fornisce il Controller di qualsiasi visualizzazione che si desidera visualizzare nello stato compresso.
-  Visualizzazione Controller secondario deve vengono combinati in un Controller primario di visualizzazione. In genere lo sviluppatore non dovrà eseguire alcuna azione per questo passaggio. il Controller di visualizzazione Split include la gestione automatica di questa fase, in base al dispositivo hardware. Tuttavia, potrebbero esserci alcuni casi speciali in cui lo sviluppatore avranno necessità di interagire con questa modifica. La chiamata di `CollapseSecondViewController` metodo il `UISplitViewControllerDelegate` consente al controller di visualizzazione master da visualizzare quando si verifica la compressione, anziché la visualizzazione dei dettagli.


### <a name="expanding-the-split-view-controller"></a>Espandendo il Controller di visualizzazione divisa

Ora si intraprendere ulteriori informazioni su cosa avviene quando un Controller di visualizzazione di menu combinato viene espanso da uno stato compresso. In questo caso, sono disponibili due fasi che è necessario eseguire:

-  In primo luogo, definire il nuovo Controller di visualizzazione principale. Per impostazione predefinita, il Controller di visualizzazione divisa utilizzeranno automaticamente il Controller primario di visualizzazione da una visualizzazione compressa. Nuovamente, lo sviluppatore può eseguire l'override questo comportamento utilizzando la `GetPrimaryViewControllerForExpandingSplitViewController` metodo il `UISplitViewControllerDelegate` .
-  Una volta che è stato scelto il Controller primario di visualizzazione, è necessario ricreare il Controller secondario di visualizzazione. Nuovamente, il Controller di visualizzazione Split include la gestione automatica di questa fase, in base al dispositivo hardware. Lo sviluppatore può eseguire l'override di questo comportamento chiamando il `SeparateSecondaryViewController` metodo il `UISplitViewControllerDelegate` .


In un Controller di visualizzazione di divisione, al Controller primario di View riveste un ruolo sia l'espansione e compressione delle viste mediante l'implementazione di `CollapseSecondViewController` e `SeparateSecondaryViewController` metodi il `UISplitViewControllerDelegate`. `UINavigationController` implementa i metodi per push e pop il controller secondario visualizza automaticamente.

### <a name="showing-view-controllers"></a>Controller di visualizzazione che mostra

Un'altra modifica effettuati Apple iOS 8 è in modo che lo sviluppatore Mostra visualizzazione controller. In passato, se l'applicazione dispone di un Controller di visualizzazione foglia (ad esempio una tabella View Controller) e lo sviluppatore ha dimostrato uno diverso (ad esempio, in risposta a toccato l'utente in una cella), l'applicazione verrà visualizzati attraverso la gerarchia di controller per il Controller di vista di navigazione e chiamare il `PushViewController` metodo su di esso per visualizzare la nuova vista.

Questo comportamento presentava un molto strette tra il Controller di spostamento e l'ambiente che era in esecuzione in. In iOS 8, Apple ha disaccoppiati questo fornendo due nuovi metodi:

-  `ShowViewController` – Adatta per visualizzare il nuovo controller di visualizzazione in base al relativo ambiente. Ad esempio, in un `UINavigationController` inserisce semplicemente la nuova vista nello stack. In un Controller di visualizzazione di divisione, il nuovo Controller di visualizzazione verrà visualizzato sul lato sinistro come il nuovo Controller di visualizzazione principale. Se è presente alcun controller di visualizzazione di contenitore, la nuova visualizzazione verrà visualizzata come modale Controller di visualizzazione.
-  `ShowDetailViewController` – Funziona in modo simile ai `ShowViewController`, ma viene implementato in un Controller di visualizzazione divisa per sostituire la visualizzazione dei dettagli con il nuovo Controller di visualizzazione vengono passati in. Se il Controller di visualizzazione di divisione viene compresso (come può essere visualizzato in un iPhone applicazione), verrà reindirizzata alla chiamata di `ShowViewController` metodo e la nuova visualizzazione verrà visualizzato come il Controller primario di visualizzazione. Nuovamente, se è presente alcun controller di visualizzazione di contenitore, la nuova visualizzazione verrà visualizzata come modale Controller di visualizzazione.


Questi metodi funziona in base a partire da Controller di visualizzazione foglia e risale la gerarchia della visualizzazione fino a individuare il controller di visualizzazione contenitore destra per gestire la visualizzazione della nuova visualizzazione.

Gli sviluppatori possono implementare `ShowViewController` e `ShowDetailViewController` nei propri controller di visualizzazione personalizzato per ottenere lo stesso automatizzata funzionalità che `UINavigationController` e `UISplitViewController` fornisce.

### <a name="how-it-works"></a>Come funziona

In questa sezione è verranno analizzati in modalità di implementazione effettivamente questi metodi in iOS 8. Primo esaminiamo il nuovo `GetTargetForAction` metodo:

 [![](unified-storyboards-images/gettargetforaction.png "Il nuovo metodo GetTargetForAction")](unified-storyboards-images/gettargetforaction.png#lightbox)

Questo metodo illustra la procedura nella catena della gerarchia finché non viene trovato il controller di visualizzazione del contenitore appropriato. Ad esempio:

1.  Se un `ShowViewController` metodo viene chiamato, il primo Controller di visualizzazione nella catena che implementa questo metodo è il Controller di navigazione, pertanto è utilizzato come elemento padre della nuova visualizzazione.
1.  Se un `ShowDetailViewController` metodo è stato chiamato, il Controller di visualizzazione di divisione è il primo Controller di visualizzazione per l'implementazione, pertanto è utilizzato come elemento padre.


Il `GetTargetForAction` metodo funziona individuando un Controller di visualizzazione che implementa un'azione specificata e quindi chiedere tale Controller di visualizzazione se vuole ricevere tale azione. Poiché questo metodo è pubblico, gli sviluppatori possono creare i propri metodi personalizzati che funzionano come incorporato in `ShowViewController` e `ShowDetailViewController` metodi.

## <a name="adaptive-presentation"></a>Presentazione adattivo

In iOS 8, Apple ha presentazioni Popover ( `UIPopoverPresentationController`) adattivo anche. In modo da un Controller di visualizzazione presentazione Popover presenteranno automaticamente una visualizzazione Popover normale in una classe di dimensioni regolari, ma verrà visualizzato a schermo intero in una classe di dimensioni in senso orizzontale compact (ad esempio in un iPhone).

Per gestire le modifiche all'interno del sistema di storyboard unificata, un nuovo oggetto controller è stato creato per gestire i controller di visualizzazione presentato: `UIPresentationController`. Dal momento in cui che viene presentato il Controller di visualizzazione fino a quando non viene chiuso, viene creato questo controller. Perché è una classe di gestione, si possono essere considerato una super classe sul Controller di visualizzazione che risponda alle modifiche di dispositivo che riguardano il Controller di visualizzazione (ad esempio l'orientamento) che vengono quindi back note nel Controller di visualizzazione controlla il Controller di presentazione.

Quando lo sviluppatore visualizza un Controller di visualizzazione utilizzando il `PresentViewController` (metodo), la gestione del processo di presentazione viene passata a `UIKit`. UIKit gestisce (tra le altre cose) il controller corretto per lo stile viene creato, con l'eccezione solo quando un Controller di visualizzazione ha lo stile impostato su `UIModalPresentationCustom`. In questo caso, l'applicazione può fornire è proprio PresentationController anziché il `UIKit` controller.

### <a name="custom-presentation-styles"></a>Stili di presentazione personalizzato

Con uno stile di presentazione personalizzato, gli sviluppatori hanno l'opzione per utilizzare un Controller personalizzato di presentazione. Questo controller personalizzato consente di modificare l'aspetto e comportamento della vista a cui si è videomonitoraggio.

<a name="size-classes"/>

## <a name="working-with-size-classes"></a>Utilizzo delle classi di dimensioni

Il progetto Xamarin foto adattivo incluso in questo articolo fornisce un esempio funzionante di utilizzo di classi di dimensioni e adattivo visualizzazione controller in un'applicazione di iOS 8 interfaccia unificata.

Mentre l'applicazione crea la relativa interfaccia utente completamente dal codice, anziché mediante la finestra di progettazione IOS e la creazione di uno Storyboard unificata, applicano le stesse tecniche. Più avanti in questo articolo, vi mostreremo come utilizzare le classi di dimensioni con uno Storyboard unificata e la finestra di progettazione in un'applicazione di Xamarin iOS.

A questo punto è opportuno esaminare come il progetto foto adattivo implementa alcune delle funzionalità di classe di dimensione in iOS 8 per creare un'applicazione adattivo.

### <a name="adapting-to-trait-environment-changes"></a>Adattarsi alle modifiche di ambiente tratto

Quando si esegue l'applicazione adattivo foto in un iPhone, quando l'utente ruota il dispositivo da verticale a orizzontale, il Controller di visualizzazione divisa visualizzerà visualizzazione master e dettagli:

 [![](unified-storyboards-images/rotation.png "Il Controller di visualizzazione divisa visualizzerà entrambi master e come illustrato di seguito consente di visualizzare i dettagli")](unified-storyboards-images/rotation.png#lightbox)

Questa operazione viene eseguita ignorando il `UpdateConstraintsForTraitCollection` metodo del Controller visualizzazione e modifica di vincoli in base al valore del `VerticalSizeClass`. Ad esempio:

```csharp
public void UpdateConstraintsForTraitCollection (UITraitCollection collection)
{
    var views = NSDictionary.FromObjectsAndKeys (
        new object[] { TopLayoutGuide, ImageView, NameLabel, ConversationsLabel, PhotosLabel },
        new object[] { "topLayoutGuide", "imageView", "nameLabel", "conversationsLabel", "photosLabel" }
    );

    var newConstraints = new List<NSLayoutConstraint> ();
    if (collection.VerticalSizeClass == UIUserInterfaceSizeClass.Compact) {
        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|[imageView]-[nameLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("[imageView]-[conversationsLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("[imageView]-[photosLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:|[topLayoutGuide]-[nameLabel]-[conversationsLabel]-[photosLabel]",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:|[topLayoutGuide][imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.Add (NSLayoutConstraint.Create (ImageView, NSLayoutAttribute.Width, NSLayoutRelation.Equal,
            View, NSLayoutAttribute.Width, 0.5f, 0.0f));
    } else {
        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|[imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[nameLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[conversationsLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("|-[photosLabel]-|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));

        newConstraints.AddRange (NSLayoutConstraint.FromVisualFormat ("V:[topLayoutGuide]-[nameLabel]-[conversationsLabel]-[photosLabel]-20-[imageView]|",
            NSLayoutFormatOptions.DirectionLeadingToTrailing, null, views));
    }

    if (constraints != null)
        View.RemoveConstraints (constraints.ToArray ());

    constraints = newConstraints;
    View.AddConstraints (constraints.ToArray ());
}
```

### <a name="adding-transition-animations"></a>Aggiunta di animazioni di transizione

Quando il Controller di visualizzazione divisa in foto adattivo applicazione passa da compresso a espanso animazioni vengono aggiunti alle animazioni predefinito eseguendo l'override di `WillTransitionToTraitCollection` metodo del controller di visualizzazione. Ad esempio:

```csharp
public override void WillTransitionToTraitCollection (UITraitCollection traitCollection, IUIViewControllerTransitionCoordinator coordinator)
{
    base.WillTransitionToTraitCollection (traitCollection, coordinator);
    coordinator.AnimateAlongsideTransition ((UIViewControllerTransitionCoordinatorContext) => {
        UpdateConstraintsForTraitCollection (traitCollection);
        View.SetNeedsLayout ();
    }, (UIViewControllerTransitionCoordinatorContext) => {
    });
}
```

### <a name="overriding-the-trait-environment"></a>Si esegue l'override dell'ambiente di tratto

Come mostrato sopra, l'applicazione di foto adattivo forza il Controller di visualizzazione divisa per visualizzare i dettagli e le viste master quando il dispositivo iPhone è nella visualizzazione orizzontale.

Questa operazione è stata eseguita usando il codice seguente nel Controller di visualizzazione:

```csharp
private UITraitCollection forcedTraitCollection = new UITraitCollection ();
...

public UITraitCollection ForcedTraitCollection {
    get {
        return forcedTraitCollection;
    }

    set {
        if (value != forcedTraitCollection) {
            forcedTraitCollection = value;
            UpdateForcedTraitCollection ();
        }
    }
}
...

public override void ViewWillTransitionToSize (SizeF toSize, IUIViewControllerTransitionCoordinator coordinator)
{
    ForcedTraitCollection = toSize.Width > 320.0f ?
         UITraitCollection.FromHorizontalSizeClass (UIUserInterfaceSizeClass.Regular) :
         new UITraitCollection ();

    base.ViewWillTransitionToSize (toSize, coordinator);
}

public void UpdateForcedTraitCollection ()
{
    SetOverrideTraitCollection (forcedTraitCollection, viewController);
}
```

### <a name="expanding-and-collapsing-the-split-view-controller"></a>Espansione e compressione di Controller di visualizzazione di divisione

Avanti si esamineranno implementazione il comportamento di espansione e compressione del Controller di visualizzazione di suddivisione in Xamarin. Nel `AppDelegate`, quando viene creato il Controller di visualizzazione di divisione, il delegato viene assegnato a gestire queste modifiche:

```csharp
public class SplitViewControllerDelegate : UISplitViewControllerDelegate
{
    public override bool CollapseSecondViewController (UISplitViewController splitViewController,
        UIViewController secondaryViewController, UIViewController primaryViewController)
    {
        AAPLPhoto photo = ((CustomViewController)secondaryViewController).Aapl_containedPhoto (null);
        if (photo == null) {
            return true;
        }

        if (primaryViewController.GetType () == typeof(CustomNavigationController)) {
            var viewControllers = new List<UIViewController> ();
            foreach (var controller in ((UINavigationController)primaryViewController).ViewControllers) {
                var type = controller.GetType ();
                MethodInfo method = type.GetMethod ("Aapl_containsPhoto");

                if ((bool)method.Invoke (controller, new object[] { null })) {
                    viewControllers.Add (controller);
                }
            }

            ((UINavigationController)primaryViewController).ViewControllers = viewControllers.ToArray<UIViewController> ();
        }

        return false;
    }

    public override UIViewController SeparateSecondaryViewController (UISplitViewController splitViewController,
        UIViewController primaryViewController)
    {
        if (primaryViewController.GetType () == typeof(CustomNavigationController)) {
            foreach (var controller in ((CustomNavigationController)primaryViewController).ViewControllers) {
                var type = controller.GetType ();
                MethodInfo method = type.GetMethod ("Aapl_containedPhoto");

                if (method.Invoke (controller, new object[] { null }) != null) {
                    return null;
                }
            }
        }

        return new AAPLEmptyViewController ();
    }
}
```

Il `SeparateSecondaryViewController` metodo verifica se una foto viene visualizzata e intraprende l'azione in base a tale stato. Se nessuna foto viene visualizzato, comprime il Controller secondario di visualizzazione in modo che il Controller di visualizzazione Master viene visualizzato.

Il `CollapseSecondViewController` metodo viene utilizzato quando si espande il Controller di visualizzazione di divisione per verificare se è presente alcuna foto nello stack, se così compresso nuovamente per la visualizzazione.

### <a name="moving-between-view-controllers"></a>Lo spostamento tra i controller di visualizzazione

Successivamente, diamo un'occhiata a come l'applicazione di foto adattivo viene spostata tra i controller di visualizzazione. Nel `AAPLConversationViewController` classe quando l'utente seleziona una cella della tabella, la `ShowDetailViewController` metodo viene chiamato per visualizzare la visualizzazione dei dettagli:

```csharp
public override void RowSelected (UITableView tableView, NSIndexPath indexPath)
{
    var photo = PhotoForIndexPath (indexPath);
    var controller = new AAPLPhotoViewController ();
    controller.Photo = photo;

    int photoNumber = indexPath.Row + 1;
    int photoCount = (int)Conversation.Photos.Count;
    controller.Title = string.Format ("{0} of {1}", photoNumber, photoCount);
    ShowDetailViewController (controller, this);
}
```

### <a name="displaying-disclosure-indicators"></a>La visualizzazione degli indicatori di diffusione

Nell'applicazione foto adattivo esistono diverse posizioni in cui gli indicatori di diffusione sono nascosti o visualizzati in base alle modifiche apportate all'ambiente di tratto. Questa operazione viene gestita con il codice seguente:

```csharp
public bool Aapl_willShowingViewControllerPushWithSender ()
{
    var selector = new Selector ("Aapl_willShowingViewControllerPushWithSender");
    var target = this.GetTargetViewControllerForAction (selector, this);

    if (target != null) {
        var type = target.GetType ();
        MethodInfo method = type.GetMethod ("Aapl_willShowingDetailViewControllerPushWithSender");
        return (bool)method.Invoke (target, new object[] { });
    } else {
        return false;
    }
}

public bool Aapl_willShowingDetailViewControllerPushWithSender ()
{
    var selector = new Selector ("Aapl_willShowingDetailViewControllerPushWithSender");
    var target = this.GetTargetViewControllerForAction (selector, this);

    if (target != null) {
        var type = target.GetType ();
        MethodInfo method = type.GetMethod ("Aapl_willShowingDetailViewControllerPushWithSender");
        return (bool)method.Invoke (target, new object[] { });
    } else {
        return false;
    }
}
```

Questi vengono implementati utilizzando la `GetTargetViewControllerForAction` metodo descritto in dettaglio in precedenza.

Quando un Controller di vista della tabella vengono visualizzati i dati, vengono utilizzati i metodi implementati sopra per visualizzare o meno un push sta per verificarsi e visualizzare o nascondere l'indicatore la diffusione di conseguenza o meno:

```csharp
public override void WillDisplay (UITableView tableView, UITableViewCell cell, NSIndexPath indexPath)
{
    bool pushes = ShouldShowConversationViewForIndexPath (indexPath) ?
         Aapl_willShowingViewControllerPushWithSender () :
         Aapl_willShowingDetailViewControllerPushWithSender ();

    cell.Accessory = pushes ? UITableViewCellAccessory.DisclosureIndicator : UITableViewCellAccessory.None;
    var conversation = ConversationForIndexPath (indexPath);
    cell.TextLabel.Text = conversation.Name;
}
```

### <a name="new-showdetailtargetdidchangenotification-type"></a>Nuovo `ShowDetailTargetDidChangeNotification` tipo

Apple ha aggiunto un nuovo tipo di notifica per l'utilizzo di classi di dimensione e ambienti tratto da all'interno di un Controller di visualizzazione di divisione, `ShowDetailTargetDidChangeNotification`. Questa notifica viene inviata ogni volta che cambia la visualizzazione dettagli per un Controller di visualizzazione di divisione di destinazione, ad esempio quando il controller si espande o comprime.

L'applicazione di foto adattivo utilizza questa notifica per aggiornare lo stato dell'indicatore di diffusione quando cambia il Controller di visualizzazione di dettaglio:

```csharp
public override void ViewDidLoad ()
{
    base.ViewDidLoad ();
    TableView.RegisterClassForCellReuse (typeof(UITableViewCell), AAPLListTableViewControllerCellIdentifier);
    NSNotificationCenter.DefaultCenter.AddObserver (this, new Selector ("showDetailTargetDidChange:"),
        UIViewController.ShowDetailTargetDidChangeNotification, null);
    ClearsSelectionOnViewWillAppear = false;
}
```

Esamina l'applicazione di foto adattivo per visualizzare tutte le modalità che classi di dimensioni più vicino, tratto raccolte e i controller di visualizzazione adattivo consente di creare facilmente un'applicazione unificata in xamarin. IOS.

## <a name="unified-storyboards"></a>Storyboard unificati

Nuovo per iOS 8, Unified storyboard consente allo sviluppatore di crearne uno, unificato file storyboard che può essere visualizzati nei dispositivi iPhone e iPad includendo più classi di dimensioni. Tramite storyboard unificata, lo sviluppatore scrive meno codice specifico dell'interfaccia utente e ha progettazione solo un'interfaccia per creare e gestire.

I principali vantaggi di storyboard unificata sono:

-  Utilizzare lo stesso file di storyboard per iPhone e iPad.
-  Distribuire le versioni precedenti a iOS 6 e iOS 7.
-  Visualizzare in anteprima il layout per diversi dispositivi, gli orientamenti e le versioni del sistema operativo all'interno di progettazione iOS Xamarin.

Questa funzionalità è completamente supportata in Visual Studio per Mac

<a name="enabling-size-classes" />

### <a name="enabling-size-classes"></a>L'abilitazione delle classi di dimensione

Per impostazione predefinita, i nuovi progetti di xamarin. IOS utilizzerà le dimensioni di classi. Per utilizzare le classi di dimensioni e Segues adattivo all'interno di uno storyboard da un progetto precedente, è necessario innanzitutto convertirlo nel formato Xcode 6 unificata Storyboard all'interno di progettazione iOS.

Per eseguire questo aprire lo Storyboard da convertire in iOS, finestra di progettazione e selezionare il **utilizzare le classi di dimensioni** casella di controllo:

 [![](unified-storyboards-images/sizeclass01.png "La casella di controllo di utilizzare le classi di dimensioni")](unified-storyboards-images/sizeclass01.png#lightbox)

Finestra di progettazione iOS confermerà che lo sviluppatore desidera convertire il formato dello storyboard per usare le classi di dimensioni:

 [![](unified-storyboards-images/sizeclass02.png "L'utilizzo di avviso di classi di dimensione")](unified-storyboards-images/sizeclass02.png#lightbox)

> [!IMPORTANT]
> Layout automatico deve essere controllati anche per le classi di dimensioni funzionare correttamente.

### <a name="generic-device-types"></a>Tipi di dispositivo generico

Una volta convertito lo storyboard per l'utilizzo di classi di dimensione, verrà visualizzata nell'area di progettazione e **vista come** dispositivo sarà generico:

 [![](unified-storyboards-images/sizeclass03.png "Visualizzare come un tipo di dispositivo generico")](unified-storyboards-images/sizeclass03.png#lightbox)

Quando viene selezionato il tipo di dispositivo, tutti i controller di visualizzazione verrà ridimensionati un quadrato di 600 x 600. Questo quadrato rappresenta le dimensioni di qualsiasi larghezza e nessuna limitazione in altezza. Quando iOS progettazione è in questa modalità, tutte le modifiche verranno applicate a tutte le classi.

Inoltre, lo sviluppatore ha la possibilità di visualizzare l'area di progettazione come un iPhone:

 [![](unified-storyboards-images/sizeclass04.png "Visualizzazione nell'area di progettazione come un iPhone")](unified-storyboards-images/sizeclass04.png#lightbox)

O visualizzandolo come un iPad:

 [![](unified-storyboards-images/sizeclass05.png "Visualizzazione nell'area di progettazione come un iPad")](unified-storyboards-images/sizeclass05.png#lightbox)

### <a name="select-a-size-class"></a>Selezionare una classe di dimensione

Il pulsante del selettore di classe di dimensione è nell'angolo superiore sinistro dell'area di progettazione (vicino la visualizzazione come elenco a discesa). Consente allo sviluppatore di selezionare le classi di dimensioni in corso di modifica:

 [![](unified-storyboards-images/sizeclass06.png "Selezionare una classe di dimensione")](unified-storyboards-images/sizeclass06.png#lightbox)

Il selettore presenta la selezione di classe di dimensione come una 3x3 griglia. Ognuno dei quadrati nella griglia rappresenta una combinazione di una classe di larghezza e un'altezza di classe. Il quadrato center consente di selezionare la classe di qualsiasi dimensione di altezza Width/Any (che è la visualizzazione predefinita di uno Storyboard unificata). Quando questa è stata selezionata, lo sviluppatore sta modificando il layout predefinito, che viene ereditato da tutte le altre configurazioni.

Quadratino nell'angolo superiore sinistro della griglia rappresenta la classe di dimensioni di Compact Compact di larghezza/altezza:

 [![](unified-storyboards-images/sizeclass07.png "La classe di dimensioni di altezza Compact larghezza/Compact")](unified-storyboards-images/sizeclass07.png#lightbox)

Questa modalità corrisponde a un iPhone in orizzontale. Il quadrato nella parte inferiore della griglia a destra rappresenta la classe dimensioni di altezza larghezza/normale regolari, che rappresenta un iPad:

 [![](unified-storyboards-images/sizeclass08.png "La classe di dimensioni di altezza regolare la larghezza o normale")](unified-storyboards-images/sizeclass08.png#lightbox)

Per modificare il layout per un iPhone l'orientamento verticale, selezionare il quadrato nell'angolo inferiore sinistro. Rappresenta la classe di dimensioni di Compact normale di larghezza/altezza:

 [![](unified-storyboards-images/sizeclass09.png "La classe di dimensioni di altezza Compact larghezza/normale")](unified-storyboards-images/sizeclass09.png#lightbox)

Fare clic sul quadrato per selezionarlo e l'area di progettazione cambia le dimensioni dei controller di visualizzazione in base alla selezione di nuovo:

 [![](unified-storyboards-images/sizeclass10.png "Dimensioni dei controller di visualizzazione in base alla selezione di nuovo, come illustrato nell'area di progettazione verranno modificate")](unified-storyboards-images/sizeclass10.png#lightbox)

Vedere la sezione di classe di dimensione di questo articolo per ulteriori informazioni su classi e gli effetti di layout per iPhone e iPad.

### <a name="adaptive-segue-types"></a>Adattata definire i tipi

Se lo sviluppatore ha utilizzato storyboard prima, quindi saranno familiarità con i tipi di segue esistente di **Push**, **modale** e **Popover**. Quando sono abilitate le classi di dimensioni in un file di Storyboard unificata, la seguente adattivo definire i tipi (che corrispondono alla nuova vista Controller API discussi in precedenza) vengono resi disponibili: **Mostra** e **Mostra dettagli** .

> [!IMPORTANT]
> Quando sono abilitate le classi di dimensioni, qualsiasi esistente segues sarà possibile convertire i nuovi tipi.

Nell'esempio di iOS 8 applicazione che utilizza uno Storyboard unificata con un Controller di visualizzazione di divisione che dispone di un menu di navigazione di gioco semplice nella visualizzazione Master. Se l'utente fa clic su un pulsante di menu, i Controller di visualizzazione dell'elemento selezionato deve essere visualizzato nella sezione dei dettagli del Controller di visualizzazione di divisione quando in esecuzione in un iPad. In un iPhone View Controller dell'elemento deve essere inserito nello stack di navigazione.

Per ottenere questo effetto, nella finestra di progettazione iOS controllo fare clic sul pulsante e trascina una linea al Controller di visualizzazione da visualizzare. Quando viene rilasciato il pulsante del mouse, selezionare `Show Detail` dal menu di scelta rapida di tipo definire:

 [![](unified-storyboards-images/segue01.png "Selezionare Mostra dettagli dal menu di scelta rapida di tipo definire")](unified-storyboards-images/segue01.png#lightbox)

Verrà creato il nuovo segue tra il pulsante e il Controller di visualizzazione. Ora eseguire l'applicazione nel simulatore di iPhone e verrà visualizzato il Menu principale:

 [![](unified-storyboards-images/segue02.png "Nel Menu principale")](unified-storyboards-images/segue02.png#lightbox)

Fare clic su di **selezionare gioco** pulsante e Controller di visualizzazione dell'elemento verranno inseriti nello Stack di navigazione:

 [![](unified-storyboards-images/segue03.png "Gli elementi, visualizzazione Controller verranno inseriti nello Stack di navigazione come mostrato")](unified-storyboards-images/segue03.png#lightbox)

Arrestare il simulatore di iPhone ed eseguire l'applicazione nel simulatore iPad. Passare l'orientamento orizzontale e principale menu viene visualizzato di nuovo:

 [![](unified-storyboards-images/segue04.png "Viene visualizzato il menu principale")](unified-storyboards-images/segue04.png#lightbox)

Fare nuovamente clic su di **selezionare gioco** pulsante e Controller di visualizzazione dell'elemento viene visualizzato nella sezione dei dettagli del Controller di visualizzazione di suddivisione:

 [![](unified-storyboards-images/segue05.png "Gli elementi View Controller illustrato nella sezione dei dettagli del Controller di visualizzazione di divisione")](unified-storyboards-images/segue05.png#lightbox)

### <a name="excluding-an-element-from-a-size-class"></a>Esclusione di un elemento da una classe di dimensione

Sono disponibili gli orari in cui un determinato elemento (ad esempio una vista, controllo o un vincolo) non è richiesto all'interno di una classe specifica di dimensione. Per escludere un elemento da una classe di dimensione, selezionare l'elemento desiderato da escludere il **area di progettazione**. Scorrere fino alla fine del **Esplora proprietà** e fare clic su di **ingranaggio** menu a discesa. Selezionare la combinazione di **larghezza** e **altezza** per escludere l'elemento da:

[![](unified-storyboards-images/exclude-a.png "Selezionare la combinazione di larghezza e altezza")](unified-storyboards-images/exclude-a.png#lightbox)

Un nuovo *esclusione Case* verrà aggiunto all'elemento in fondo il **Esplora proprietà**. Successivamente, deselezionare il **installato** casella di controllo per la classe di dimensioni specificata:

[![](unified-storyboards-images/exclude-b.png "Deselezionare la casella di controllo installato")](unified-storyboards-images/exclude-b.png#lightbox)

Passare l'area di progettazione per la larghezza e altezza che l'elemento è stata esclusa dal, è stato rimosso dalla classe di dimensione specificato, ma non l'intera progettazione dell'interfaccia utente:

 [![](unified-storyboards-images/exclude02.png "Area di progettazione per la larghezza e altezza che l'elemento è stata esclusa dal commutatore")](unified-storyboards-images/exclude02.png#lightbox)

Tornare all'implementazione della classe di dimensioni di altezza Width/Any Any e l'elemento è ancora presente:

 [![](unified-storyboards-images/exclude03.png "Tornare all'implementazione della classe di dimensioni di altezza Width/Any Any")](unified-storyboards-images/exclude03.png#lightbox)

Quando l'applicazione viene eseguita nel simulatore iPad, viene visualizzato l'elemento:

 [![](unified-storyboards-images/exclude04.png "L'elemento mostrato quando l'app in esecuzione nel simulatore iPad")](unified-storyboards-images/exclude04.png#lightbox)

E quando l'applicazione viene eseguita nel simulatore di iPhone, l'elemento è mancante:

 [![](unified-storyboards-images/exclude05.png "L'elemento mancante quando l'app in esecuzione nel simulatore di iPhone")](unified-storyboards-images/exclude05.png#lightbox)

Per rimuovere un caso di esclusione da un elemento, è sufficiente selezionare l'elemento di **area di progettazione**, scorrere fino alla fine del **Esplora proprietà** e fare clic sul  **-** accanto il case da rimuovere.

Per visualizzare un'implementazione di storyboard unificata, esaminare il `UnifiedStoryboard` Xamarin iOS 8 applicazione associato a questo documento di esempio.

## <a name="dynamic-launch-screens"></a>Schermate di avvio dinamico

File della schermata di avvio viene visualizzato come una schermata durante l'avvio di un'applicazione iOS per fornire commenti e suggerimenti per l'utente che l'app è effettivamente avviamento. Prima di iOS 8, lo sviluppatore è più `Default.png` asset per ogni tipo, l'orientamento e schermata la risoluzione del dispositivo che l'applicazione verrebbe eseguito su immagine. Ad esempio, `Default@2x.png`, `Default-Landscape@2x~ipad.png`, `Default-Portrait@2x~ipad.png`e così via.

Factoring nuovo iPhone 6 e iPhone 6 Plus dispositivi e l'Apple Watch future con tutte le iPhone esistente e i dispositivi iPad, rappresenta un'ampia gamma di dimensioni variabili, gli orientamenti e risoluzioni di `Default.png` gli asset delle immagini schermata avvio che è necessario la creazione e gestione. Inoltre, questi file possono essere molto grandi e saranno "sovraccarico" il pacchetto di applicazione risultato finale, aumentando la quantità di tempo necessario per scaricare l'applicazione da iTunes App Store (possibilmente impedendone la possibilità di essere recapitati tramite una rete cellulare) e aumentare la quantità di spazio di archiviazione necessario nel dispositivo dell'utente finale.

Nuovo in iOS 8, lo sviluppatore può creare un singolo atomico `.xib` file in Xcode che usa Layout automatico e le classi di dimensione per creare un *dinamico della schermata avvio* che funzionerà per tutti i dispositivi, risoluzione e l'orientamento. Questo non solo riduce la quantità di lavoro necessaria per lo sviluppatore di creare e gestire tutti gli asset di immagini necessarie, ma riduce notevolmente la dimensione del pacchetto installato dell'applicazione.


Schermate di avvio dinamico sono le seguenti considerazioni e limitazioni:

 - Utilizzare solo `UIKit` classi.
 - Utilizzare una visualizzazione singola radice che è un `UIView` o `UIViewController` oggetto.
 - Non effettua alcuna connessione al codice dell'applicazione (non aggiungere **azioni** o **prese**).
 - Non aggiungere `UIWebView` oggetti.
 - Non utilizzare le classi personalizzate.
 - Non utilizzare gli attributi di runtime.

Con le linee guida precedente presente, si esaminerà l'aggiunta di una schermata di avvio dinamico a un progetto iOS 8 Xamarin esistente.

Seguire questa procedura:

1. Aprire **Visual Studio per Mac** e caricare il **soluzione** per aggiungere la schermata avvio dinamica per.
2. Nel **Esplora**, fare doppio clic su di `MainStoryboard.storyboard` file e selezionare **Apri con** > **Xcode interfaccia generatore**:

    [![](unified-storyboards-images/dls01.png "Apri con Generatore interfaccia Xcode")](unified-storyboards-images/dls01.png#lightbox)
3. In Xcode, selezionare **File** > **New** > **File...** :

    [![](unified-storyboards-images/dls02.png "Selezionare File / nuovo")](unified-storyboards-images/dls02.png#lightbox)
4. Selezionare **iOS** > **interfaccia utente** > **schermata avvio** e fare clic su di **Avanti** pulsante:

    [![](unified-storyboards-images/dls03.png "Selezionare iOS / dell'interfaccia utente / schermata di avvio")](unified-storyboards-images/dls03.png#lightbox)
5. Nome del file `LaunchScreen.xib` e fare clic su di **crea** pulsante:

    [![](unified-storyboards-images/dls04.png "Nome del file LaunchScreen.xib")](unified-storyboards-images/dls04.png#lightbox)
6. Modificare la progettazione della schermata di avvio aggiungendo elementi grafici e utilizzo di vincoli di Layout per posizionare i controlli per dispositivi specificati, orientamento e dimensioni dello schermo:

    [![](unified-storyboards-images/dls05.png "Modifica della progettazione della schermata di avvio")](unified-storyboards-images/dls05.png#lightbox)
7. Salvare le modifiche in `LaunchScreen.xib`.
8. Selezionare il **applicazioni destinazione** e **generale** scheda:

    [![](unified-storyboards-images/dls06.png "Selezionare la destinazione di applicazioni e la scheda Generale")](unified-storyboards-images/dls06.png#lightbox)
9. Fare clic sul **scegliere Info. plist** pulsante, selezionare il `Info.plist` per app Xamarin scegliere il **scegliere** pulsante:

    [![](unified-storyboards-images/dls07.png "Selezionare il file Info. plist per l'app Xamarin")](unified-storyboards-images/dls07.png#lightbox)
10. Nel **App icone e le immagini di avvio** sezione, aprire il **avviare File schermata** elenco a discesa e scegliere il `LaunchScreen.xib` creato in precedenza:

    [![](unified-storyboards-images/dls08.png "Scegliere il LaunchScreen.xib")](unified-storyboards-images/dls08.png#lightbox)
11. Salvare le modifiche al file e tornare a Visual Studio per Mac.
12. Attendere che Visual Studio per Mac completare la sincronizzazione delle modifiche con Xcode.
13. Nel **Esplora**, fare clic sul **risorse** cartella e selezionare **Aggiungi** > **Aggiungi file...** :

    [![](unified-storyboards-images/dls09.png "Selezionare Aggiungi / aggiungere i file...")](unified-storyboards-images/dls09.png#lightbox)
14. Selezionare il `LaunchScreen.xib` file creato in precedenza e fare clic su di **aprire** pulsante:

    [![](unified-storyboards-images/dls10.png "Selezionare il file LaunchScreen.xib")](unified-storyboards-images/dls10.png#lightbox)
15. Compilare l'applicazione.

### <a name="testing-the-dynamic-launch-screen"></a>Schermata di avvio dinamico di test

In Visual Studio per Mac, selezionare il simulatore di iPhone 4 Retina ed eseguire l'applicazione. Il formato corretto e l'orientamento verrà visualizzata la schermata di avvio dinamico:

[![](unified-storyboards-images/dls11.png "La schermata di avvio dinamico visualizzata in orientamento verticale")](unified-storyboards-images/dls11.png#lightbox)

Arrestare l'applicazione in Visual Studio per Mac e selezionare un dispositivo iOS 8 iPad. Eseguire l'applicazione e la schermata di avvio verrà formattata correttamente per questo dispositivo e l'orientamento:

[![](unified-storyboards-images/dls12.png "La schermata di avvio dinamico visualizzato con orientamento orizzontale")](unified-storyboards-images/dls12.png#lightbox)

Tornare a Visual Studio per Mac e arrestare l'esecuzione dell'applicazione.

### <a name="working-with-ios-7"></a>Utilizzo di iOS 7

Per mantenere la compatibilità con iOS 7, includere solo le consuete `Default.png` immagine asset normalmente l'applicazione iOS 8. iOS restituirà il comportamento precedente e utilizzare tali file come schermata iniziale durante l'esecuzione in un dispositivo iOS 7.

Per verificare un'implementazione di una schermata di avvio dinamico nella Xamarin, controllare il [schermate di avvio dinamico](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/) applicazione iOS 8 associato al documento di esempio.

## <a name="summary"></a>Riepilogo

In questo articolo ha richiesto un rapido controllo di classi di dimensione e gli effetti di layout in dispositivi iPhone e iPad. È stato illustrato funzionamento dei tratti, tratto ambienti e raccolte di tratto con classi di dimensione per creare interfacce unificata. Impiegato adattivo controller di visualizzazione breve panoramica e sul relativo funzionamento con le classi di dimensioni all'interno di interfacce unificata. Implementazione di classi e interfacce unificata completamente dal codice c# all'interno di un'applicazione iOS 8 Xamarin era.

Infine, in questo articolo descritte le nozioni di base di creazione di storyboard unificata con Xamarin iOS progettazione che verrà utilizzati in dispositivi iOS e la creazione di una singola schermata di avvio dinamico che verrà visualizzata come schermata iniziale su ogni dispositivo iOS 8.

## <a name="related-links"></a>Collegamenti correlati

- [Foto adattivo (esempio)](https://developer.xamarin.com/samples/monotouch/ios8/AdaptivePhotos/)
- [Esempio StoryboardIntro](https://developer.xamarin.com/samples/monotouch/StoryboardIntro/)
- [Schermate di avvio dinamico (esempio)](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/)
- [Introduzione a iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Layout dinamico in 8 - evolvere 2014 (video)](http://youtu.be/f3mMGlS-lM4)
- [UIPresentationController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPresentationController_class/)
- [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset)
