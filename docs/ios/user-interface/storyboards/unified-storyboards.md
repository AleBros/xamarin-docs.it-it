---
title: Storyboard unificati in xamarin. IOS
description: Questo documento descrive storyboard unificati in xamarin. IOS. Storyboard unificati consentono agli sviluppatori di supportare più dimensioni dello schermo con una definizione unica interfaccia.
ms.prod: xamarin
ms.assetid: F6F70374-FC2A-4401-A712-A16D0F9B340F
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/20/2017
ms.openlocfilehash: 26aeaa3d230a5c104014edd899b8d9231ced31e9
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108522"
---
# <a name="unified-storyboards-in-xamarinios"></a>Storyboard unificati in xamarin. IOS

iOS 8 include un meccanismo nuovo, più semplici da usare per la creazione dell'interfaccia utente, ovvero lo storyboard unificato. Con uno storyboard singolo per includere tutte le dimensioni dello schermo di un hardware diverso, veloce e reattive viste possono essere create con una "progettazione-uso-molti, una volta" stile di visualizzazione.

Quando lo sviluppatore non sono più necessari creare uno storyboard separato e specifico per i dispositivi iPhone e iPad, hanno la possibilità di progettare le applicazioni con un'interfaccia comune e quindi personalizzare tale interfaccia per classi di dimensioni diverse. In questo modo, un'applicazione può essere adattata a punti di forza di ogni fattore di forma e ogni interfaccia utente può essere ottimizzato per offrire un'esperienza ottimale.

<a name="size-classes" />

## <a name="size-classes"></a>Classi di dimensioni

Prima di iOS 8, usato dallo sviluppatore `UIInterfaceOrientation` e `UIInterfaceIdiom` per distinguere tra modalità verticale e orizzontale e tra i dispositivi iPhone e iPad. In IOS 8, dispositivo e l'orientamento è determinato consiste nell'usare *le classi di dimensioni*.

I dispositivi sono definiti dalle classi di dimensioni, in assi orizzontali e verticali, e sono disponibili due tipi di classi di dimensioni in iOS 8:

-  **Regolare** – si tratta di una dimensione di schermi di grandi dimensioni (ad esempio un iPad) o un gadget che dà l'impressione di grandi dimensioni (ad esempio un `UIScrollView`
-  **Compact** – si tratta di dispositivi di piccole dimensioni (ad esempio un iPhone). Queste dimensioni prende in considerazione l'orientamento del dispositivo.


Se i due concetti vengono utilizzati insieme, il risultato è una 2x2 griglia che definisce le diverse dimensioni possibili che possono essere usate in entrambi gli orientamenti diversi, come illustrato nel diagramma seguente:

 [![](unified-storyboards-images/sizeclassgrid.png "Una 2x2 griglia che definisce le diverse dimensioni possibili che possono essere utilizzate in normali e compatta gli orientamenti")](unified-storyboards-images/sizeclassgrid.png#lightbox)

Lo sviluppatore può creare un Controller di visualizzazione che utilizzano una qualsiasi delle quattro possibilità che comporterebbe diversi layout (come illustrato nelle figure precedenti).

### <a name="ipad-size-classes"></a>le classi di dimensioni iPad

L'iPad, a causa della dimensione, ha un **regolari** classe dimensioni per entrambi gli orientamenti.

 [![](unified-storyboards-images/image1.png "le classi di dimensioni iPad")](unified-storyboards-images/image1.png#lightbox)


### <a name="iphone-size-classes"></a>le classi di dimensioni iPhone

L'iPhone include classi di dimensioni diverse in base all'orientamento del dispositivo:

 [![](unified-storyboards-images/iphonesizeclasses.png "le classi di dimensioni iPhone")](unified-storyboards-images/iphonesizeclasses.png#lightbox)

-  Quando il dispositivo è in modalità verticale, la schermata ha un **compact** classe orizzontalmente e **regolari** verticalmente
-  Quando il dispositivo è in modalità orizzontale, le classi dello schermo vengono invertite dalla modalità verticale.

### <a name="iphone-6-plus-size-classes"></a>Classi di dimensioni iPhone 6 Plus

Le dimensioni sono le stesse l'iPhone precedenti in orientamento verticale, ma è diversa nel panorama:

[![](unified-storyboards-images/iphone6sizeclasses.png "Classi di dimensioni iPhone 6 Plus")](unified-storyboards-images/iphone6sizeclasses.png#lightbox)

Poiché l'iPhone 6 Plus dispone di uno schermo sufficientemente grande, è possibile avere una classe di dimensioni larghezza normale in modalità orizzontale.

### <a name="support-for-a-new-screen-scale"></a>Supporto per la scala di una schermata nuova

L'iPhone 6 Plus Usa un nuovo display Retina HD con un fattore di scala schermata di 3.0 (tre volte l'originale iPhone risoluzione dello schermo). Per fornire la migliore esperienza possibile in questi dispositivi, includere un nuovo disegno progettato su larga scala questa schermata. In Xcode 6 e versioni successive, asset cataloghi possono includere immagini a 3 dimensioni x; 1 x e 2 x è sufficiente aggiungere il nuovo Asset di immagini e iOS sceglierà le risorse corrette durante l'esecuzione in un iPhone 6 Plus.

L'immagine del comportamento in iOS di caricamento anche riconosce un `@3x` suffisso nei file di immagine. Ad esempio, se lo sviluppatore di un asset di immagine (con risoluzioni diverse) sono inclusi nel bundle dell'applicazione con i nomi di file seguenti: `MonkeyIcon.png`, `MonkeyIcon@2x.png`, e `MonkeyIcon@3x.png`. Nell'iPhone 6 Plus il `MonkeyIcon@3x.png` immagine verrà usata automaticamente se lo sviluppatore viene caricata un'immagine mediante il codice seguente:

```csharp
UIImage icon = UIImage.FromFile("MonkeyImage.png");
```
O se si assegna l'immagine a un elemento dell'interfaccia utente usando iOS come finestra di progettazione `MonkeyIcon.png`, il `MonkeyIcon@3x.png` verrà usato, anche in questo caso automaticamente su iPhone 6 Plus.

<a name="dynamic-launch-screens" />

### <a name="dynamic-launch-screens"></a>Schermate di avvio dinamico

File della schermata di avvio viene visualizzato come una schermata durante l'avvio di un'applicazione iOS per fornire commenti e suggerimenti all'utente che l'app viene effettivamente avvio backup. Prima di iOS 8, lo sviluppatore dovrà includere più `Default.png` gli asset per ogni tipo, l'orientamento e schermata la risoluzione del dispositivo che l'applicazione verrebbe eseguito su delle immagini.

Nuovo ai dispositivi iOS 8, lo sviluppatore può creare un singolo, atomico `.xib` file in Xcode che usa le classi di dimensioni e Layout automatico per creare un *schermata di avvio dinamico* che funzionerà per ogni dispositivo, la risoluzione e l'orientamento. Ciò non solo riduce la quantità di lavoro necessaria per lo sviluppatore di creare e gestire tutti gli asset immagini necessarie, ma riduce la dimensione del bundle di installazione dell'applicazione.

## <a name="traits"></a>Tratti

Tratti sono proprietà che possono essere usate per determinare come un layout cambia come modifiche apportate al relativo ambiente. Sono costituite da un set di proprietà (il `HorizontalSizeClass` e `VerticalSizeClass` base `UIUserInterfaceSizeClass`), nonché l'idioma interfaccia ( `UIUserInterfaceIdiom`) e la scala di visualizzazione.

Tutti gli stati precedenti vengono incapsulati in un contenitore che Apple fa riferimento a come una raccolta di tratto ( `UITraitCollection`), che contiene non solo le proprietà, ma anche i relativi valori.

## <a name="trait-environment"></a>Ambiente di tratto

Gli ambienti dei tratti sono una nuova interfaccia in iOS 8 e in grado di restituire una raccolta dei tratti per gli oggetti seguenti:

-  Le schermate ( `UIScreens` ).
-  Windows ( `UIWindows` ).
-  Visualizzare i controller ( `UIViewController` ).
-  Viste ( `UIView` ).
-  Controller di presentazione ( `UIPresentationController` ).


Lo sviluppatore utilizza la raccolta dei tratti restituito da un ambiente dei tratti per determinare come deve essere disposto un'interfaccia utente.

Tutti gli ambienti di ancoraggio è rendere una gerarchia, come illustrato nel diagramma seguente:

 [![](unified-storyboards-images/viewhierarchy.png "Il diagramma della gerarchia dei tratti ambienti")](unified-storyboards-images/viewhierarchy.png#lightbox)

La raccolta dei tratti ciascuno degli ambienti di tratto precedente con un flusso, per impostazione predefinita, dall'elemento padre per l'ambiente figlio.

Oltre a ottenere la raccolta di tratto corrente, l'ambiente di tratto presenta un `TraitCollectionDidChange` metodo, che può essere sottoposto a override in sottoclassi visualizzazione o Controller di visualizzazione. Lo sviluppatore può utilizzare questo metodo per modificare uno degli elementi dell'interfaccia utente che dipendono da traits quando i tratti sono state modificate.

## <a name="typical-trait-collections"></a>Raccolte di tratto tipico

Questa sezione illustra i tipi tipici delle raccolte di tratto l'esperienza utente quando si lavora con iOS 8.

Di seguito è una raccolta di tratto tipico che lo sviluppatore potrebbe essere visualizzato in un iPhone:

|Proprietà|Valore|
|--- |--- |
|`HorizontalSizeClass`|Compact|
|`VerticalSizeClass`|Regular|
|`UserInterfaceIdom`|Telefono|
|`DisplayScale`|2.0|

Il set precedente rappresenterebbe una completamente qualificato tratto di raccolta, purché dispongano di valori per tutte le relative proprietà dei tratti.

È anche possibile disporre di una raccolta dei tratti che mancano alcuni dei relativi valori (che Apple fa riferimento a come *Unspecified*):

|Proprietà|Valore|
|--- |--- |
|`HorizontalSizeClass`|Compact|
|`VerticalSizeClass`|Non specificato|
|`UserInterfaceIdom`|Non specificato|
|`DisplayScale`|Non specificato|

In generale, tuttavia, quando lo sviluppatore chiede all'ambiente dei tratti per la relativa raccolta di tratto, restituirà un insieme completo come illustrato nell'esempio precedente.

Se un ambiente di tratto (ad esempio, una vista o View Controller) non è presente all'interno della gerarchia di visualizzazione corrente, lo sviluppatore potrebbe ottenere nuovamente i valori non specificati per uno o più delle proprietà del tratto.

Gli sviluppatori potranno inoltre ottenere una raccolta di tratto parzialmente qualificati se usano uno dei metodi forniti da Apple, ad esempio `UITraitCollection.FromHorizontalSizeClass`, per creare una nuova raccolta.

Un'operazione che può essere eseguita su più raccolte di tratto è confrontarli tra loro, che implica che richiede una raccolta di tratto se contiene un altro. Cosa si intende per *contenimento* è che, per qualsiasi tratto specificato nella seconda raccolta, il valore deve corrispondere esattamente con il valore nella prima raccolta.

Per testare due tratti, usare il `Contains` metodo del `UITraitCollection` passando il valore del tratto da sottoporre a test.

Lo sviluppatore può eseguire i confronti manualmente nel codice per determinare come viste di layout o controller di visualizzazione. Tuttavia, `UIKit` utilizza internamente il metodo per fornire alcune delle proprie funzionalità, come il Proxy di aspetto, ad esempio.

## <a name="appearance-proxy"></a>Aspetto Proxy

Il Proxy di aspetto è stato introdotto nelle versioni precedenti di iOS per consentire agli sviluppatori di personalizzare le proprietà del proprio punto di vista. Si è stato esteso in iOS 8 per supportare le raccolte dei tratti.

I proxy aspetto includono ora un nuovo metodo `AppearanceForTraitCollection`, che restituisce un nuovo aspetto di Proxy per la raccolta dei tratti specificata che è stata passata. Tutte le personalizzazioni che lo sviluppatore dovrà eseguire su tale Proxy aspetto avranno effetto solo nelle viste che è conforme alla raccolta dei tratti specificata.

In genere lo sviluppatore passerà in una raccolta di tratto parzialmente specificato per il `AppearanceForTraitCollection` metodo, ad esempio quella appena specificata un'orizzontale dimensioni classe di compattare, in modo da cui è stato possibile personalizzare qualsiasi visualizzazione nell'applicazione che è compatto orizzontalmente.

## <a name="uiimage"></a>UIImage

Un'altra classe che Apple ha aggiunto il tratto di raccolta è `UIImage`. In passato lo sviluppatore era necessario specificare una @1X e @2x versione di qualsiasi asset di grafica bitmap che si prevede di includere nell'applicazione (ad esempio, un'icona).

iOS 8 è stata estesa per consentire agli sviluppatori di includere più versioni di un'immagine in un catalogo di immagine basato su una raccolta dei tratti. Ad esempio, lo sviluppatore può includere un'immagine più piccola per l'utilizzo di una classe di tratto Compact e un'immagine con dimensione completa per qualsiasi altra raccolta.

Quando una delle immagini viene usata all'interno di un `UIImageView` (classe), la visualizzazione di immagini visualizzano automaticamente la versione corretta dell'immagine per la raccolta dei tratti. Se l'ambiente di tratto cambia (ad esempio, il cambio utente il dispositivo da verticale a orizzontale), la visualizzazione di immagini selezionerà automaticamente le nuove dimensioni dell'immagine per associare la nuova raccolta di tratto e modificarne le dimensioni affinché corrisponda a quello della versione corrente dell'immagine da visualizzato.

## <a name="uiimageasset"></a>UIImageAsset

Apple ha aggiunto una nuova classe per iOS 8 denominata `UIImageAsset` per offrire allo sviluppatore un maggiore controllo sulla selezione di immagini.

Un Asset immagine eseguito il wrapping di tutte le versioni diverse di un'immagine e consente allo sviluppatore richiedere un'immagine specifica che corrisponde a una raccolta dei tratti che è stata passata. Le immagini possono essere aggiunti o rimossi da un Asset di immagine, in tempo reale.

Per altre informazioni su asset immagini contenute, vedere di Apple [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset) documentazione.

## <a name="combining-trait-collections"></a>Combinazione di raccolte di tratto

Un'altra funzione che uno sviluppatore può eseguire sulle raccolte di tratto consiste nell'aggiungere due insieme che comportare combinato insieme, in cui i valori da una raccolta non specificati vengono sostituiti dai valori specificati in un secondo. Questa operazione viene eseguita usando il `FromTraitsFromCollections` metodo di `UITraitCollection` classe.

Come indicato in precedenza, se uno qualsiasi dei tratti non è specificato in una delle raccolte di tratto e viene specificata in un altro, il valore verrà impostato per la versione specificata. Tuttavia, se sono presenti più versioni di un determinato valore specificato, il valore dall'ultimo tratto raccolta sarà il valore che viene usato.

## <a name="adaptive-view-controllers"></a>Controller di visualizzazione adattivo

Questa sezione illustra i dettagli della modalità iOS visualizzazione e controller di visualizzazione adottano i concetti di tratti e classi di dimensioni per essere automaticamente più adattive nelle applicazioni dello sviluppatore.

### <a name="split-view-controller"></a>Controller doppia visualizzazione

Una delle classi Controller di visualizzazione che è stata modificata la maggior parte in iOS 8 è il `UISplitViewController` classe. In passato, lo sviluppatore utilizzerebbe spesso un Controller doppia visualizzazione nella versione iPad dell'applicazione e quindi hanno fornire una versione completamente diversa della loro gerarchia di visualizzazione per la versione dell'app per iPhone.

In iOS 8, il `UISplitViewController` classe è disponibile in entrambe le piattaforme (iPad e iPhone), che consente allo sviluppatore di creare una gerarchia di Controller di visualizzazione che potrà essere utilizzato per iPhone e iPad.

Quando un iPhone è in modalità orizzontale, il Controller di visualizzazione divisa presenterà le viste side-by-side, esattamente come verrebbe quando viene visualizzato in un iPad.

### <a name="overriding-traits"></a>Si esegue l'override di tratti

Supporta la propagazione ambienti tratto dal contenitore padre fino a contenitori figlio, come illustrato nell'immagine seguente che mostra un Controller doppia visualizzazione su un iPad l'orientamento orizzontale:

 [![](unified-storyboards-images/cascadingclasses01.png "Un Controller doppia visualizzazione su un iPad l'orientamento orizzontale")](unified-storyboards-images/cascadingclasses01.png#lightbox)

Poiché l'iPad dispone di una classe di dimensioni normali in entrambi gli orientamenti orizzontali e verticali, la doppia visualizzazione verrà visualizzato il master e dettaglio le visualizzazioni.

In un iPhone, in cui la classe di dimensioni è compact in entrambi gli orientamenti, il Controller doppia visualizzazione Visualizza solo la visualizzazione dettagli, come illustrato di seguito:

 [![](unified-storyboards-images/cascadingclasses02.png "Il Controller doppia visualizzazione consente di visualizzare solo la visualizzazione dettagli")](unified-storyboards-images/cascadingclasses02.png#lightbox)

In un'applicazione in cui lo sviluppatore desidera una visualizzazione master e dettaglio in un iPhone nell'orientamento orizzontale, lo sviluppatore deve inserire un contenitore padre per il Controller doppia visualizzazione e la raccolta dei tratti di eseguire l'override. Come illustrato nella figura seguente:

 [![](unified-storyboards-images/cascadingclasses03.png "Lo sviluppatore deve inserire un contenitore padre per il Controller di visualizzazione di divisione e sostituire la raccolta dei tratti")](unified-storyboards-images/cascadingclasses03.png#lightbox)

Oggetto `UIView` è impostato come elemento padre del Controller di visualizzazione di divisione e il `SetOverrideTraitCollection` viene chiamato per la vista passando un nuovo insieme di tratto e destinazione è il Controller doppia visualizzazione. La nuova raccolta di tratto esegue l'override di `HorizontalSizeClass`, impostandola su `Regular`, in modo che il Controller doppia visualizzazione verrà visualizzato il master e dettaglio le visualizzazioni in un iPhone l'orientamento orizzontale.

Si noti che il `VerticalSizeClass` è stata impostata su `unspecified`, che consente la nuova raccolta di tratto da aggiungere alla raccolta dei tratti dell'elemento padre, causando un `Compact VerticalSizeClass` per l'elemento figlio Controller doppia visualizzazione.

### <a name="trait-changes"></a>Modifiche dei tratti

In questa sezione verrà esaminati, in modo dettagliato, in modo in cui le raccolte di tratto transizione quando viene modificato l'ambiente dei tratti. Ad esempio, quando il dispositivo viene ruotato da verticale a orizzontale.

 [![](unified-storyboards-images/traittransitions01.png "Il verticale a orizzontale panoramica sulle modifiche dei tratti")](unified-storyboards-images/traittransitions01.png#lightbox)

IOS 8 esegue in primo luogo, alcune configurazioni per la preparazione per la transizione per l'implementazione. Successivamente, il sistema consente di animare lo stato di transizione. Infine, iOS 8 pulisce alcuno stato temporaneo necessario durante la transizione.

iOS 8 fornisce i callback diversi che lo sviluppatore può utilizzare per partecipare la modifica di tratto, come illustrato nella tabella seguente:

|Phase|Callback|Descrizione|
|--- |--- |--- |
|Configurazione|<ul><li>`WillTransitionToTraitCollection`</li><li>`TraitCollectionDidChange`</li></ul>|<ul><li>Questo metodo viene chiamato all'inizio di una modifica del tratto prima di una raccolta di tratto viene impostata sul nuovo valore.</li><li>Il metodo viene chiamato quando viene modificato il valore della raccolta dei tratti ma prima che venga eseguita qualsiasi animazione.</li></ul>|
|Animazione|`WillTransitionToTraitCollection`|Il coordinatore di transizione che viene passato a questo metodo ha un `AnimateAlongside` proprietà che consente allo sviluppatore di aggiungere le animazioni che verranno eseguite con le animazioni predefinito.|
|Pulizia|`WillTransitionToTraitCollection`|Fornisce un metodo per gli sviluppatori di includere il proprio codice di pulizia dopo la transizione ha luogo.|

Il `WillTransitionToTraitCollection` metodo è molto utile per l'animazione di controller di visualizzazione con le modifiche all'insieme dei tratti. Il `WillTransitionToTraitCollection` metodo è disponibile solo nei controller di visualizzazione ( `UIViewController`) e non su altri ambienti di tratto, ad esempio `UIViews`.

Il `TraitCollectionDidChange` è ideale per operare con la `UIView` (classe), in cui lo sviluppatore desidera aggiornare l'interfaccia utente, come i tratti sono la modifica.

### <a name="collapsing-the-split-view-controllers"></a>Comprimere i controller di visualizzazione suddivisa

A questo punto è possibile osservare più da vicino cosa accade quando un Controller doppia visualizzazione consente di comprimere da una colonna di due a una visualizzazione di una colonna. Come parte di questa modifica, sono disponibili due processi che devono verificarsi:

-  Per impostazione predefinita, il Controller doppia visualizzazione utilizzerà il controller di visualizzazione primaria come visualizzazione dopo che si verifica la compressione. Lo sviluppatore può eseguire l'override di questo comportamento eseguendo l'override di `GetPrimaryViewControllerForCollapsingSplitViewController` metodo del `UISplitViewControllerDelegate` e fornendo qualsiasi Controller di visualizzazione che si desidera visualizzare in uno stato compresso.
-  Il Controller di visualizzazione secondaria deve uniti in un Controller di visualizzazione primaria. In genere lo sviluppatore non dovrà eseguire alcuna azione per questo passaggio. il Controller di visualizzazione Split include la gestione automatica di questa fase in base al dispositivo hardware. Tuttavia, potrebbero esserci alcuni casi speciali in cui lo sviluppatore dovrà interagire con questa modifica. Chiama il `CollapseSecondViewController` metodo del `UISplitViewControllerDelegate` consente al controller di visualizzazione master da visualizzare quando si verifica la compressione, anziché la visualizzazione dei dettagli.


### <a name="expanding-the-split-view-controller"></a>Espandendo il Controller di visualizzazione suddivisa

A questo punto è possibile osservare più da vicino cosa accade quando un Controller doppia visualizzazione è stato ampliato passando da uno stato compresso. Ancora una volta, esistono due fasi che devono verificarsi:

-  In primo luogo, definire il nuovo Controller di visualizzazione primaria. Per impostazione predefinita, il Controller doppia visualizzazione userà automaticamente il Controller di visualizzazione primaria dalla visualizzazione compressa. Anche in questo caso, lo sviluppatore può ignorare questo comportamento usando il `GetPrimaryViewControllerForExpandingSplitViewController` metodo di `UISplitViewControllerDelegate` .
-  Dopo aver selezionato il Controller di visualizzazione primaria, è necessario ricreare il Controller di visualizzazione secondaria. Anche in questo caso, il Controller di visualizzazione Split include la gestione automatica di questa fase in base al dispositivo hardware. Lo sviluppatore può eseguire l'override di questo comportamento chiamando il `SeparateSecondaryViewController` metodo di `UISplitViewControllerDelegate` .


In un Controller di visualizzazione di divisione, il Controller di visualizzazione primaria riveste un ruolo nell'espansione e compressione delle visualizzazioni implementando il `CollapseSecondViewController` e `SeparateSecondaryViewController` metodi del `UISplitViewControllerDelegate`. `UINavigationController` implementa questi metodi per eseguire il push e prelevare il controller di visualizzazione secondaria automaticamente.

### <a name="showing-view-controllers"></a>Controller di visualizzazione che mostra

Un'altra modifica che Apple ha tentato di iOS 8 è in modo che lo sviluppatore Visualizza controller di visualizzazione. In passato, se l'applicazione utilizzava un Controller di visualizzazione foglia (ad esempio, un Controller di visualizzazione tabella) e lo sviluppatore ha dimostrato uno diverso (ad esempio, nella risposta per il tocco di utente in una cella), l'applicazione sarà raggiungibile attraverso la gerarchia di controller per il Controller di visualizzazione di esplorazione e chiamare il `PushViewController` metodo su di esso per visualizzare la nuova visualizzazione.

Questa condizione ha presentato un accoppiamento stretto molto tra il Controller di spostamento e l'ambiente che era in esecuzione in. In iOS 8, Apple ha diviso questa fornendo due nuovi metodi:

-  `ShowViewController` – Adatta la soluzione per visualizzare il nuovo controller di visualizzazione basato sul proprio ambiente. Ad esempio, in un `UINavigationController` inserisce semplicemente la nuova visualizzazione nello stack. In un Controller di visualizzazione di divisione, il nuovo Controller di visualizzazione verrà visualizzato sul lato sinistro sotto il nuovo Controller di visualizzazione primaria. Se è presente alcun controller di visualizzazione di contenitore, la nuova visualizzazione verrà visualizzata come Controller di visualizzazione modale.
-  `ShowDetailViewController` – Funziona in modo simile a `ShowViewController`, ma viene implementato in un Controller di visualizzazione divisa per sostituire la visualizzazione dei dettagli con il nuovo Controller di visualizzazione vengono passati in. Se il Controller di visualizzazione Split viene compresso (come è possibile riscontrare in un'applicazione iPhone), la chiamata verrà reindirizzata al `ShowViewController` metodo e la nuova visualizzazione verrà visualizzato come Controller di visualizzazione primaria. Anche in questo caso, se è presente alcun controller di visualizzazione di contenitore, la nuova visualizzazione verrà visualizzata come Controller di visualizzazione modale.


Questi metodi funzionano iniziando il Controller di visualizzazione foglia e risale la gerarchia di visualizzazione fino a individuare il controller di visualizzazione del contenitore per gestire la visualizzazione della nuova visualizzazione.

Gli sviluppatori possono implementare `ShowViewController` e `ShowDetailViewController` nel proprio controller di visualizzazione personalizzata per ottenere lo stesso automatizzata funzionalità che `UINavigationController` e `UISplitViewController` fornisce.

### <a name="how-it-works"></a>Come funziona

In questa sezione verrà usato un quadro di come questi metodi vengono effettivamente implementati in iOS 8. Primo verrà ora esaminato il nuovo `GetTargetForAction` metodo:

 [![](unified-storyboards-images/gettargetforaction.png "Il nuovo metodo GetTargetForAction")](unified-storyboards-images/gettargetforaction.png#lightbox)

Questo metodo descrive la catena della gerarchia fino a quando non viene trovato il controller di visualizzazione del contenitore appropriato. Ad esempio:

1.  Se un `ShowViewController` viene chiamato il metodo, il primo Controller di visualizzazione nella catena che implementa questo metodo è il Controller di spostamento, quindi viene usato come elemento padre della nuova visualizzazione.
1.  Se un `ShowDetailViewController` metodo è stato chiamato, il Controller di visualizzazione di divisione è il primo Controller di visualizzazione per l'implementazione, quindi viene usato come elemento padre.


Il `GetTargetForAction` metodo funziona individuando un Controller di visualizzazione che implementa una determinata azione e quindi in cui viene chiesto se vuole ricevere tale azione tale Controller di visualizzazione. Poiché questo metodo è pubblico, gli sviluppatori possono creare i propri metodi personalizzati che funzionano come incorporato in `ShowViewController` e `ShowDetailViewController` metodi.

## <a name="adaptive-presentation"></a>Presentazione adattivo

In iOS 8, Apple ha reso nel Popover presentazioni ( `UIPopoverPresentationController`) adattivo anche. In modo che un Controller di visualizzazione nel Popover presentazione presenterà automaticamente una visualizzazione normale nel Popover in una classe di dimensioni normali, ma verrà visualizzato a schermo intero in una classe di dimensioni in senso orizzontale compact (ad esempio in un iPhone).

Per contenere le modifiche apportate all'interno del sistema di storyboard unificati, è stato creato un nuovo oggetto controller per gestire i controller di visualizzazione presentata, ovvero `UIPresentationController`. Il controller viene creato dal momento in cui che viene presentato il Controller di visualizzazione fino a quando non viene chiuso. Come è una classe di gestione, può essere considerato una superclasse tramite il Controller di visualizzazione come che risponda alle modifiche di dispositivo che riguardano il Controller di visualizzazione (ad esempio, l'orientamento) che vengono successivamente back "notte" nel Controller di visualizzazione consente di controllare il Controller di presentazione.

Quando lo sviluppatore visualizza un Controller di visualizzazione utilizzando il `PresentViewController` metodo, la gestione del processo di presentazione viene passata alla `UIKit`. Gestisce UIKit, tra altre cose, i controller corretto per lo stile viene creato, con la sola eccezione quando un Controller di visualizzazione ha lo stile impostato su `UIModalPresentationCustom`. In questo caso, l'applicazione può fornire è proprio PresentationController anziché il `UIKit` controller.

### <a name="custom-presentation-styles"></a>Stili di presentazione personalizzata

Con uno stile di presentazione personalizzata, gli sviluppatori hanno la possibilità di utilizzare un Controller di presentazione personalizzata. Questo controller personalizzato utilizzabile per modificare l'aspetto e comportamento della vista è videomonitoraggio a.

<a name="size-classes"/>

## <a name="working-with-size-classes"></a>Utilizzo di classi di dimensioni

Il progetto Xamarin foto adattiva è incluso in questo articolo offre un esempio funzionante dell'utilizzo delle classi di dimensioni e adattivo i controller di visualizzazione in un'applicazione con interfaccia unificata iOS 8.

Mentre l'applicazione crea la relativa interfaccia utente completamente dal codice, invece di usare la finestra di progettazione di IOS e la creazione di uno Storyboard unificati, applicano le stesse tecniche. Più avanti in questo articolo, ecco come usare le classi di dimensioni con uno Storyboard unificati e la finestra di progettazione in un'applicazione Xamarin iOS.

Ora diamo uno sguardo al modo in cui il progetto foto adattivo implementa alcune delle funzionalità di classe di dimensioni in iOS 8 per creare un'applicazione adattivi.

### <a name="adapting-to-trait-environment-changes"></a>Adattarsi alle modifiche all'ambiente dei tratti

Quando si esegue l'applicazione Adaptive foto in un iPhone, quando l'utente ruota il dispositivo da verticale a orizzontale, il Controller di visualizzazione divisa visualizzerà sia lo schema e i dettagli di visualizzazione:

 [![](unified-storyboards-images/rotation.png "Il Controller di visualizzazione divisa visualizzerà entrambi il master e visualizzazione dei dettagli, come illustrato di seguito")](unified-storyboards-images/rotation.png#lightbox)

Questa operazione viene eseguita eseguendo l'override di `UpdateConstraintsForTraitCollection` metodo del Controller di visualizzazione e modifica i vincoli in base al valore della `VerticalSizeClass`. Ad esempio:

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

Quando il Controller di visualizzazione divisa in foto Adaptive application va da compressi a espanso, le animazioni vengono aggiunti alle animazioni predefinita eseguendo l'override di `WillTransitionToTraitCollection` metodo del controller di visualizzazione. Ad esempio:

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

### <a name="overriding-the-trait-environment"></a>Si esegue l'override dell'ambiente dei tratti

Come mostrato sopra, l'applicazione Adaptive foto forza il Controller di visualizzazione divisa per visualizzare i dettagli e le visualizzazioni master quando il dispositivo iPhone è in visualizzazione orizzontale.

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

### <a name="expanding-and-collapsing-the-split-view-controller"></a>Espandere e comprimere il Controller di visualizzazione suddivisa

Successivo si esaminerà come è stato implementato il comportamento di espansione e compressione di Controller di visualizzazione divisa in Xamarin. Nel `AppDelegate`, quando viene creato il Controller di visualizzazione di divisione, il delegato viene assegnato per gestire queste modifiche:

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

Il `SeparateSecondaryViewController` metodo di test per verificare se una foto viene visualizzata e intraprende le azioni in base allo stato in questione. Se nessuna foto viene mostrato lo comprime il Controller di visualizzazione secondaria, in modo che venga visualizzato il Controller di visualizzazione Master.

Il `CollapseSecondViewController` metodo viene utilizzato quando si espande il Controller doppia visualizzazione per vedere se eventuali foto presenti nello stack, se così viene compressa in base alla vista.

### <a name="moving-between-view-controllers"></a>Lo spostamento tra i controller di visualizzazione

Successivamente, diamo un'occhiata al modo in cui l'applicazione Adaptive foto viene spostata tra i controller di visualizzazione. Nel `AAPLConversationViewController` classe quando l'utente seleziona una cella della tabella, la `ShowDetailViewController` metodo viene chiamato per visualizzare la visualizzazione dei dettagli:

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

### <a name="displaying-disclosure-indicators"></a>La visualizzazione degli indicatori di divulgazione

Nell'applicazione Adaptive foto esistono diversi punti in cui la diffusione di indicatori sono nascosti o visualizzati in base alle modifiche apportate all'ambiente dei tratti. Questa operazione viene gestita con il codice seguente:

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

Questi vengono implementati utilizzando la `GetTargetViewControllerForAction` metodo illustrato in dettaglio in precedenza.

Quando un Controller visualizzazione tabella vengono visualizzati i dati, Usa i metodi implementati sopra per visualizzare o meno un push sta per verificarsi e se visualizzare o nascondere l'indicatore la diffusione di conseguenza:

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

Apple ha aggiunto un nuovo tipo di notifica per l'uso di classi di dimensioni e ambienti tratto da un Controller di visualizzazione divisa, `ShowDetailTargetDidChangeNotification`. Questa notifica viene inviata ogni volta che cambia la visualizzazione dettagli per un Controller doppia visualizzazione di destinazione, ad esempio quando il controller si espande o comprime.

L'applicazione Adaptive foto utilizza questa notifica per aggiornare lo stato dell'indicatore di divulgazione quando viene modificato il Controller di visualizzazione di dettaglio:

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

Esaminiamo più da vicino l'applicazione Adaptive foto per visualizzare tutte le modalità che le classi di dimensioni, le raccolte di tratto e controller di visualizzazione adattivo può essere utilizzato per creare facilmente un'applicazione unificata di xamarin. IOS.

## <a name="unified-storyboards"></a>Storyboard unificati

Nuovo per iOS 8, Unified Storyboards consente allo sviluppatore di crearne uno, unificato file storyboard che può essere visualizzati nei dispositivi iPhone e iPad indicando come destinazione più classi di dimensioni. Usando gli storyboard unificati, lo sviluppatore scrive meno codice specifico dell'interfaccia utente e dispone di una sola interfaccia progettazione per creare e gestire.

Sono i principali vantaggi degli storyboard unificati:

-  Usare lo stesso file di storyboard per iPhone e iPad.
-  Distribuire con le versioni precedenti a iOS 6 e iOS 7.
-  Visualizzare in anteprima il layout per dispositivi diversi, gli orientamenti e le versioni del sistema operativo all'interno di Xamarin iOS Designer.

Questa funzionalità è completamente supportata in Visual Studio per Mac

<a name="enabling-size-classes" />

### <a name="enabling-size-classes"></a>Abilitazione delle classi di dimensioni

Per impostazione predefinita, ogni nuovo progetto xamarin. IOS utilizzerà classi di dimensioni. Per usare le classi di dimensioni e gli elementi segue adattivo all'interno di uno storyboard da un progetto precedente, deve prima essere convertito nel formato dello Storyboard Xcode 6 unificati all'interno di iOS Designer.

Eseguire questo aprire lo Storyboard da convertire in iOS Designer e verificare i **utilizzare le classi di dimensioni** casella di controllo:

 [![](unified-storyboards-images/sizeclass01.png "La casella di controllo di usare le classi di dimensioni")](unified-storyboards-images/sizeclass01.png#lightbox)

IOS Designer confermerà che lo sviluppatore desidera convertire il formato dello storyboard per usare le classi di dimensioni:

 [![](unified-storyboards-images/sizeclass02.png "L'avviso di classi di dimensioni di utilizzo")](unified-storyboards-images/sizeclass02.png#lightbox)

> [!IMPORTANT]
> Layout automatico deve essere selezionata anche per le classi di dimensioni funzionare correttamente.

### <a name="generic-device-types"></a>Tipi di dispositivo generici

Una volta lo storyboard è stato convertito per utilizzare le classi di dimensioni, verrà visualizzati nuovamente nella finestra di progettazione e la **Visualizza come** dispositivo sarà generico:

 [![](unified-storyboards-images/sizeclass03.png "Visualizza come un tipo di dispositivo generici")](unified-storyboards-images/sizeclass03.png#lightbox)

Quando viene selezionato il tipo di dispositivo generici, tutti i controller di visualizzazione verrà ridimensionati un quadrato 600 x 600. Questo quadrato rappresenta le dimensioni di qualsiasi larghezza e nessuna limitazione in altezza. Una volta iOS Designer in questa modalità, eventuali modifiche verranno applicate a tutte le classi di dimensioni.

Lo sviluppatore ha anche l'opzione di visualizzare l'area di progettazione come un iPhone:

 [![](unified-storyboards-images/sizeclass04.png "Visualizzazione nell'area di progettazione come un iPhone")](unified-storyboards-images/sizeclass04.png#lightbox)

O visualizzarlo come un iPad:

 [![](unified-storyboards-images/sizeclass05.png "Visualizzazione nell'area di progettazione come un iPad")](unified-storyboards-images/sizeclass05.png#lightbox)

### <a name="select-a-size-class"></a>Selezionare una classe di dimensioni

Pulsante del selettore di classe di dimensioni è l'angolo superiore sinistro dell'area di progettazione (quasi la Visualizza come elenco a discesa). Consente allo sviluppatore di selezionare le classi di dimensioni che sono attualmente in fase di modifica:

 [![](unified-storyboards-images/sizeclass06.png "Selezionare una classe di dimensioni")](unified-storyboards-images/sizeclass06.png#lightbox)

Il selettore viene visualizzata la selezione di classe di dimensioni come una 3x3 griglia. Ognuno dei quadrati della griglia rappresenta una combinazione di una classe di larghezza e una classe di altezza. Il quadrato centrale consente di selezionare la classe di dimensioni di altezza Width/Any Any (ovvero la visualizzazione predefinita di uno Storyboard unificati). Quando questo quadrato è selezionata, lo sviluppatore sta modificando il layout predefinito, che viene ereditato da tutte le altre configurazioni.

Il quadrato nell'angolo superiore sinistro della griglia rappresenta la classe di dimensioni di altezza larghezza/Compact Compact:

 [![](unified-storyboards-images/sizeclass07.png "La classe di dimensioni di altezza Compact larghezza/Compact")](unified-storyboards-images/sizeclass07.png#lightbox)

Questa modalità corrisponde a un iPhone l'orientamento orizzontale. Il quadrato in basso a destra della griglia rappresenta la classe dimensioni di altezza larghezza/normale regolari, che rappresenta un iPad:

 [![](unified-storyboards-images/sizeclass08.png "La classe di dimensioni di altezza regolare la larghezza/normale")](unified-storyboards-images/sizeclass08.png#lightbox)

Per modificare il layout per un iPhone l'orientamento verticale, selezionare il quadrato in basso a sinistra. Rappresenta la classe di dimensioni di altezza larghezza/normale Compact:

 [![](unified-storyboards-images/sizeclass09.png "La classe di dimensioni di altezza Compact larghezza/normale")](unified-storyboards-images/sizeclass09.png#lightbox)

Fare clic sul quadrato per selezionarla e l'area di progettazione cambia le dimensioni dei controller di visualizzazione in modo che corrisponda alla nuova selezione:

 [![](unified-storyboards-images/sizeclass10.png "Dimensioni dei controller di visualizzazione in modo che corrisponda alla nuova selezione come illustrato nell'area di progettazione verranno modificate")](unified-storyboards-images/sizeclass10.png#lightbox)

Vedere la sezione di classe di dimensioni di questo articolo per altre informazioni su classi di dimensioni e gli effetti di layout per i dispositivi iPhone e iPad.

### <a name="adaptive-segue-types"></a>Tipi di elemento Segue adattivo

Se lo sviluppatore ha utilizzato gli storyboard prima, quindi saranno familiarità con i tipi di elemento segue esistente di **Push**, **modale** e **Popover**. Quando le classi di dimensioni sono abilitate in un file Storyboard unificati, adattivo Segue le seguenti (che corrispondono alla nuova API di Controller di visualizzazione indicato in precedenza) vengono resi disponibili: **mostrare** e **Mostra dettaglio** .

> [!IMPORTANT]
> Quando sono abilitate le classi di dimensioni, tutte le classi esistenti gli elementi segue verranno essere convertiti nei tipi di nuovo.

Nell'esempio di iOS 8 applicazione che usa uno Storyboard unificati con un Controller doppia visualizzazione con un menu di navigazione di gioco semplice della visualizzazione Master. Se l'utente fa clic su un pulsante di menu, i Controller di visualizzazione dell'elemento selezionato deve essere visualizzato nella sezione dei dettagli del Controller di visualizzazione divisa quando in esecuzione in un iPad. In un iPhone View Controller dell'elemento deve essere inserito nello stack di navigazione.

Per ottenere questo effetto, in iOS Designer controllo fare clic sul pulsante e trascinare una linea al Controller di visualizzazione da visualizzare. Quando viene rilasciato il pulsante del mouse, selezionare `Show Detail` dal menu a comparsa di tipo elemento Segue:

 [![](unified-storyboards-images/segue01.png "Selezionare Mostra dettagli dal menu di scelta rapida di tipo elemento Segue")](unified-storyboards-images/segue01.png#lightbox)

Verrà creato il nuovo elemento segue tra i pulsante e il Controller di visualizzazione. Ora eseguire l'applicazione nel simulatore iPhone e verrà visualizzato il Menu principale:

 [![](unified-storyboards-images/segue02.png "Nel Menu principale")](unified-storyboards-images/segue02.png#lightbox)

Fare clic sui **gioco selezionare** pulsante e Controller di visualizzazione dell'elemento verranno inseriti nello Stack di navigazione:

 [![](unified-storyboards-images/segue03.png "I Controller di visualizzazione di elementi verranno inseriti nello Stack di navigazione come mostrato")](unified-storyboards-images/segue03.png#lightbox)

Arrestare il simulatore di iPhone ed eseguire l'applicazione nel simulatore iPad. Passa a principale e l'orientamento orizzontale menu viene visualizzato di nuovo:

 [![](unified-storyboards-images/segue04.png "Viene visualizzato il menu principale")](unified-storyboards-images/segue04.png#lightbox)

Anche in questo caso, fare clic sui **gioco selezionare** pulsante e Controller di visualizzazione dell'elemento viene visualizzato nella sezione dei dettagli del Controller di visualizzazione di suddivisione:

 [![](unified-storyboards-images/segue05.png "Gli elementi View Controller illustrato nella sezione dei dettagli del Controller di visualizzazione suddivisa")](unified-storyboards-images/segue05.png#lightbox)

### <a name="excluding-an-element-from-a-size-class"></a>Esclusione di un elemento da una classe di dimensioni

Vi sono casi quando un elemento specificato (ad esempio, una vista, controllo o un vincolo) non è necessario all'interno di una classe di dimensioni specifiche. Per escludere un elemento da una classe di dimensioni, selezionare l'elemento desiderato da escludere il **nell'area di progettazione**. Scorrere fino alla fine del **Esplora proprietà** e fare clic sul **ingranaggio** menu a discesa. Selezionare la combinazione delle **larghezza** e **altezza** per escludere l'elemento da:

[![](unified-storyboards-images/exclude-a.png "Selezionare la combinazione di larghezza e altezza")](unified-storyboards-images/exclude-a.png#lightbox)

Una nuova *caso di esclusione* verrà aggiunto all'elemento in basso il **Esplora proprietà**. Successivamente, deselezionare il **Installed** casella di controllo per la classe di dimensioni specificata:

[![](unified-storyboards-images/exclude-b.png "Deselezionare la casella di controllo installato")](unified-storyboards-images/exclude-b.png#lightbox)

Cambiare l'area di progettazione per la larghezza e altezza che l'elemento è stato escluso dalla, è stato rimosso dalla classe di dimensioni specificato, ma non l'intera progettazione dell'interfaccia utente:

 [![](unified-storyboards-images/exclude02.png "Area di progettazione per la larghezza e altezza che l'elemento è stato escluso dal commutatore")](unified-storyboards-images/exclude02.png#lightbox)

Verrà utilizzata la classe di dimensioni di altezza Width/Any Any e l'elemento sia ancora presente:

 [![](unified-storyboards-images/exclude03.png "Verrà utilizzata la classe di dimensioni di altezza Width/Any Any")](unified-storyboards-images/exclude03.png#lightbox)

Quando l'applicazione viene eseguita nel simulatore l'iPad, viene visualizzato l'elemento:

 [![](unified-storyboards-images/exclude04.png "L'elemento mostrato quando l'esecuzione dell'app nel simulatore iPad")](unified-storyboards-images/exclude04.png#lightbox)

E quando l'applicazione viene eseguita nel simulatore iPhone, manca l'elemento:

 [![](unified-storyboards-images/exclude05.png "L'elemento mancante quando l'esecuzione dell'app nel simulatore iPhone")](unified-storyboards-images/exclude05.png#lightbox)

Per rimuovere un caso di esclusione da un elemento, è sufficiente selezionare l'elemento nel **nell'area di progettazione**, scorrere fino alla fine del **Esplora proprietà** e fare clic sui **-** sul pulsante accanto il caso da rimuovere.

Per un'implementazione degli storyboard unificati, esaminare il `UnifiedStoryboard` un'applicazione Xamarin iOS 8 associata a questo documento di esempio.

## <a name="dynamic-launch-screens"></a>Schermate di avvio dinamico

File della schermata di avvio viene visualizzato come una schermata durante l'avvio di un'applicazione iOS per fornire commenti e suggerimenti all'utente che l'app viene effettivamente avvio backup. Prima di iOS 8, lo sviluppatore dovrà includere più `Default.png` gli asset per ogni tipo, l'orientamento e schermata la risoluzione del dispositivo che l'applicazione verrebbe eseguito su delle immagini. Ad esempio, `Default@2x.png`, `Default-Landscape@2x~ipad.png`, `Default-Portrait@2x~ipad.png`e così via.

Considerando il nuovo iPhone 6 e iPhone 6 Plus dispositivi e l'imminente di Apple Watch con tutte le iPhone esistente e i dispositivi iPad, rappresenta una matrice di dimensioni variabili, gli orientamenti e le soluzioni di grandi dimensioni `Default.png` gli asset immagine dello schermo di avvio che devono creare e mantenere. Inoltre, questi file possono essere piuttosto grandi e "riempiranno" il bundle dell'applicazione del risultato finale, aumentando la quantità di tempo necessaria per scaricare l'applicazione da iTunes App Store (potenzialmente impedendone la possibilità di essere recapitati tramite una rete cellulare) e aumentare la quantità di spazio di archiviazione necessario nel dispositivo dell'utente finale.

Nuovo ai dispositivi iOS 8, lo sviluppatore può creare un singolo, atomico `.xib` file in Xcode che usa le classi di dimensioni e Layout automatico per creare un *schermata di avvio dinamico* che funzionerà per ogni dispositivo, la risoluzione e l'orientamento. Ciò non solo riduce la quantità di lavoro necessaria per lo sviluppatore di creare e gestire tutti gli asset immagini necessarie, ma riduce notevolmente le dimensioni del bundle di installazione dell'applicazione.


Schermate di avvio dinamico hanno le limitazioni e le considerazioni seguenti:

 - Usare solo `UIKit` classi.
 - Usare una visualizzazione singola radice che è un `UIView` o `UIViewController` oggetto.
 - Non apportare tutte le connessioni per il codice dell'applicazione (non aggiungono **azioni** oppure **Outlet**).
 - Non aggiungere `UIWebView` oggetti.
 - Non usare le classi personalizzate.
 - Non usare gli attributi di runtime.

Con le linee guida precedenti mente, esaminiamo l'aggiunta di una schermata di avvio dinamico a un progetto iOS 8 Xamarin esistente.

Seguire questa procedura:

1. Aprire **Visual Studio per Mac** e caricare il **soluzione** dinamica schermata di avvio per aggiungere.
2. Nel **Esplora soluzioni**, fare doppio clic sui `MainStoryboard.storyboard` del file e selezionare **Apri con** > **Interface Builder Xcode**:

    [![](unified-storyboards-images/dls01.png "Apri con Interface Builder di Xcode")](unified-storyboards-images/dls01.png#lightbox)
3. In Xcode, selezionare **File** > **New** > **File...** :

    [![](unified-storyboards-images/dls02.png "Selezionare File / nuovo")](unified-storyboards-images/dls02.png#lightbox)
4. Selezionare **iOS** > **interfaccia utente** > **schermata avvio** e fare clic su di **successivo** pulsante:

    [![](unified-storyboards-images/dls03.png "Selezionare iOS / interfaccia utente / avvio dello schermo")](unified-storyboards-images/dls03.png#lightbox)
5. Denominare il file `LaunchScreen.xib` e scegliere il **Create** pulsante:

    [![](unified-storyboards-images/dls04.png "Denominare il file LaunchScreen.xib")](unified-storyboards-images/dls04.png#lightbox)
6. Modificare la progettazione della schermata di avvio per aggiungere elementi grafici e usando i vincoli di Layout per posizionare i controlli per dispositivi specificati, gli orientamenti e le dimensioni dello schermo:

    [![](unified-storyboards-images/dls05.png "Modifica la progettazione della schermata di avvio")](unified-storyboards-images/dls05.png#lightbox)
7. Salvare le modifiche apportate a `LaunchScreen.xib`.
8. Selezionare il **applicazioni di destinazione** e il **generali** scheda:

    [![](unified-storyboards-images/dls06.png "Selezionare la destinazione delle applicazioni e la scheda Generale")](unified-storyboards-images/dls06.png#lightbox)
9. Fare clic sul **scegliere Info. plist** pulsante, seleziona la `Info.plist` per le app Xamarin e scegliere il **Scegli** pulsante:

    [![](unified-storyboards-images/dls07.png "Selezionare il file Info. plist per l'app per Xamarin")](unified-storyboards-images/dls07.png#lightbox)
10. Nel **icone dell'App e immagini di avvio** sezione, aprire il **avviare File schermata** elenco a discesa e scegliere il `LaunchScreen.xib` creato in precedenza:

    [![](unified-storyboards-images/dls08.png "Scegliere il LaunchScreen.xib")](unified-storyboards-images/dls08.png#lightbox)
11. Salvare le modifiche apportate al file e tornare a Visual Studio per Mac.
12. Attendere che Visual Studio per Mac completare la sincronizzazione delle modifiche con Xcode.
13. Nel **Esplora soluzioni**, fare clic sul **Resource** cartella e selezionare **Add** > **aggiungere file...** :

    [![](unified-storyboards-images/dls09.png "Selezionare Add / aggiungere file...")](unified-storyboards-images/dls09.png#lightbox)
14. Selezionare il `LaunchScreen.xib` file creato in precedenza e fare clic sui **Open** pulsante:

    [![](unified-storyboards-images/dls10.png "Selezionare il file LaunchScreen.xib")](unified-storyboards-images/dls10.png#lightbox)
15. Compilare l'applicazione.

### <a name="testing-the-dynamic-launch-screen"></a>Schermata di avvio dinamico di test

In Visual Studio per Mac, selezionare il simulatore di iPhone 4 Retina ed eseguire l'applicazione. Schermata di avvio dinamico verrà visualizzata nel formato corretto e orientamento:

[![](unified-storyboards-images/dls11.png "La schermata di avvio dinamico visualizzata nell'orientamento verticale")](unified-storyboards-images/dls11.png#lightbox)

Arrestare l'applicazione in Visual Studio per Mac e selezionare un dispositivo iOS 8 iPad. Eseguire l'applicazione e la schermata di avvio verrà formattata correttamente per questo dispositivo e l'orientamento:

[![](unified-storyboards-images/dls12.png "La schermata di avvio dinamico visualizzata nell'orientamento orizzontale")](unified-storyboards-images/dls12.png#lightbox)

Tornare a Visual Studio per Mac e arrestare l'esecuzione dell'applicazione.

### <a name="working-with-ios-7"></a>Uso di iOS 7

Per mantenere la compatibilità con le versioni precedenti con iOS 7, includere semplicemente il consueto `Default.png` gli asset come di consueto nell'applicazione iOS 8 delle immagini. iOS restituirà il comportamento precedente e usare tali file come schermata iniziale durante l'esecuzione in un dispositivo iOS 7.

Per visualizzare un'implementazione di una schermata di avvio dinamico in Xamarin, vedere la [schermate di avvio dinamico](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/) applicazione iOS 8 associato a questo documento di esempio.

## <a name="summary"></a>Riepilogo

Questo articolo ha richiesto una sintesi delle classi di dimensioni e gli effetti di layout in dispositivi iPhone e iPad. È stato illustrato come tratti, tratto ambienti e le raccolte dei tratti funzionano con classi di dimensioni di creare interfacce unificata. Tempo impiegato breve rassegna adattivo i controller di visualizzazione e sul relativo funzionamento con le classi di dimensioni all'interno di interfacce unificata. Farlo somigliare di implementazione di classi di dimensioni e le interfacce unificata completamente da C# codice all'interno di un'applicazione per Xamarin iOS 8.

Infine, questo articolo ha illustrato le nozioni di base della creazione di storyboard unificati con Xamarin iOS Designer che verranno utilizzati in dispositivi iOS di proprietà e la creazione di una singola schermata di avvio dinamico che verrà visualizzata come schermata iniziale in tutti i dispositivi iOS 8.

## <a name="related-links"></a>Collegamenti correlati

- [Foto adattive (esempio)](https://developer.xamarin.com/samples/monotouch/ios8/AdaptivePhotos/)
- [Esempio StoryboardIntro](https://developer.xamarin.com/samples/monotouch/StoryboardIntro/)
- [Schermate di avvio dinamico (esempio)](https://developer.xamarin.com/samples/monotouch/ios8/DynamicLaunchScreen/)
- [Introduzione a iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Layout dinamico in IOS 8 - evolvere 2014 (video)](http://youtu.be/f3mMGlS-lM4)
- [UIPresentationController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPresentationController_class/)
- [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset)
