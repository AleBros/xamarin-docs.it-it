---
title: Storyboard unificati in Novell. iOS
description: Questo documento descrive gli storyboard unificati in Novell. iOS. Gli storyboard unificati consentono agli sviluppatori di supportare più dimensioni dello schermo con una singola definizione di interfaccia.
ms.prod: xamarin
ms.assetid: F6F70374-FC2A-4401-A712-A16D0F9B340F
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/20/2017
ms.openlocfilehash: 7005b7a675af084db6d0563acd3ba4b9c0190832
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84572364"
---
# <a name="unified-storyboards-in-xamarinios"></a>Storyboard unificati in Novell. iOS

iOS 8 include un nuovo meccanismo più semplice da usare per la creazione dell'interfaccia utente, lo storyboard unificato. Con un singolo storyboard per coprire tutte le diverse dimensioni dello schermo hardware, è possibile creare visualizzazioni rapide e reattive in uno stile di tipo "progettazione, utilizzo e molti".

Poiché lo sviluppatore non deve più creare uno storyboard separato e specifico per i dispositivi iPhone e iPad, ha la possibilità di progettare applicazioni con un'interfaccia comune e quindi di personalizzare tale interfaccia per classi di dimensioni diverse. In questo modo, un'applicazione può essere adattata ai punti di forza di ogni fattore di forma e ogni interfaccia utente può essere ottimizzata per garantire un'esperienza ottimale.

<a name="size-classes"></a>

## <a name="size-classes"></a>Classi di dimensioni

Prima di iOS 8, lo sviluppatore usava `UIInterfaceOrientation` e `UIInterfaceIdiom` per distinguere tra modalità verticale e orizzontale e tra dispositivi iPhone e iPad. In iOS 8, l'orientamento e il dispositivo vengono determinati utilizzando *le classi di dimensioni*.

I dispositivi sono definiti da classi di dimensioni, sia in assi verticali che orizzontali, ed esistono due tipi di classi di dimensioni in iOS 8:

- **Normale** : si tratta di una dimensione dello schermo grande (ad esempio un iPad) o di un gadget che offre un'impressione di grandi dimensioni, ad esempio`UIScrollView`
- **Compatta** : si tratta di dispositivi più piccoli, ad esempio un iPhone. Questa dimensione prende in considerazione l'orientamento del dispositivo.

Se i due concetti vengono usati insieme, il risultato è una griglia 2 x 2 che definisce le diverse dimensioni possibili che possono essere usate in entrambi gli orientamenti diversi, come illustrato nel diagramma seguente:

 [![](unified-storyboards-images/sizeclassgrid.png "A 2 x 2 grid that defines the different possible sizes that can be used in Regular and Compact orientations")](unified-storyboards-images/sizeclassgrid.png#lightbox)

Lo sviluppatore può creare un controller di visualizzazione che usa una delle quattro possibilità che comporterebbe layout diversi, come illustrato nell'immagine precedente.

### <a name="ipad-size-classes"></a>Classi di dimensioni iPad

L'iPad, a causa delle dimensioni, ha una **normale** dimensione della classe per entrambi gli orientamenti.

 [![](unified-storyboards-images/image1.png "iPad Size Classes")](unified-storyboards-images/image1.png#lightbox)

### <a name="iphone-size-classes"></a>Classi di dimensioni iPhone

L'iPhone ha classi di dimensioni diverse in base all'orientamento del dispositivo:

 [![](unified-storyboards-images/iphonesizeclasses.png "iPhone Size Classes")](unified-storyboards-images/iphonesizeclasses.png#lightbox)

- Quando il dispositivo è in modalità verticale, la schermata presenta una classe **compatta** orizzontalmente e **normale** verticalmente
- Quando il dispositivo è in modalità orizzontale, le classi della schermata vengono invertite dalla modalità verticale.

### <a name="iphone-6-plus-size-classes"></a>Classi di iPhone 6 Plus Size

Le dimensioni sono uguali a quelle degli iPhone precedenti quando sono in orientamento verticale, ma diverse in orizzontale:

[![](unified-storyboards-images/iphone6sizeclasses.png "iPhone 6 Plus Size Classes")](unified-storyboards-images/iphone6sizeclasses.png#lightbox)

Poiché iPhone 6 Plus ha una schermata sufficientemente grande, può avere una classe di dimensioni di larghezza regolare in modalità orizzontale.

### <a name="support-for-a-new-screen-scale"></a>Supporto per una nuova scala dello schermo

IPhone 6 Plus usa un nuovo display Retina HD con un fattore di scala dello schermo pari a 3,0 (tre volte la risoluzione dello schermo iPhone originale). Per fornire la migliore esperienza possibile su questi dispositivi, includere la nuova grafica progettata per questa scala dello schermo. In Xcode 6 e versioni successive, i cataloghi asset possono includere immagini con dimensioni 1x, 2x e 3x; è sufficiente aggiungere i nuovi asset immagine e iOS sceglierà gli asset corretti quando viene eseguito su un iPhone 6 Plus.

Il comportamento di caricamento dell'immagine in iOS riconosce anche un `@3x` suffisso nei file di immagine. Ad esempio, se lo sviluppatore include un asset immagini (con risoluzioni diverse) nel bundle dell'applicazione con i nomi file seguenti: `MonkeyIcon.png` , `MonkeyIcon@2x.png` e `MonkeyIcon@3x.png` . In iPhone 6 Plus l' `MonkeyIcon@3x.png` immagine verrà usata automaticamente se lo sviluppatore carica un'immagine usando il codice seguente:

```csharp
UIImage icon = UIImage.FromFile("MonkeyImage.png");
```

In alternativa, se l'immagine viene assegnata a un elemento dell'interfaccia utente usando la finestra di progettazione iOS come `MonkeyIcon.png` , `MonkeyIcon@3x.png` verrà usato di nuovo automaticamente in iPhone 6 Plus.

<a name="dynamic-launch-screens"></a>

### <a name="dynamic-launch-screens"></a>Schermate di avvio dinamico

Il file della schermata di avvio viene visualizzato come schermata iniziale mentre viene avviata un'applicazione iOS per fornire commenti e suggerimenti all'utente che l'app è effettivamente avviata. Prima di iOS 8, lo sviluppatore avrebbe dovuto includere più `Default.png` Asset immagine per ogni tipo di dispositivo, orientamento e risoluzione dello schermo in cui l'applicazione verrebbe eseguita.

Una novità di iOS 8, lo sviluppatore può creare un singolo `.xib` file atomico in Xcode che usa le classi di layout e dimensioni automatiche per creare una *schermata di avvio dinamico* che funzionerà per ogni dispositivo, risoluzione e orientamento. Questo non solo riduce la quantità di lavoro richiesto allo sviluppatore per creare e gestire tutte le risorse di immagine necessarie, ma riduce le dimensioni del bundle installato dell'applicazione.

## <a name="traits"></a>Tratti

I tratti sono proprietà che possono essere usate per determinare la modalità di modifica di un layout in base alle modifiche apportate all'ambiente. Sono costituiti da un set di proprietà ( `HorizontalSizeClass` e `VerticalSizeClass` in base a `UIUserInterfaceSizeClass` ), nonché dall'interfaccia idioma ( `UIUserInterfaceIdiom` ) e dalla scala di visualizzazione.

Tutti gli stati precedenti sono inclusi in un contenitore a cui Apple fa riferimento come raccolta di tratti ( `UITraitCollection` ), che contiene non solo le proprietà, ma anche i relativi valori.

## <a name="trait-environment"></a>Ambiente del tratto

Gli ambienti del tratto sono una nuova interfaccia in iOS 8 e possono restituire una raccolta di tratti per gli oggetti seguenti:

- Schermate ( `UIScreens` ).
- Windows ( `UIWindows` ).
- Controller di visualizzazione ( `UIViewController` ).
- Viste ( `UIView` ).
- Controller di presentazione ( `UIPresentationController` ).

Lo sviluppatore usa la raccolta di tratti restituita da un ambiente di tratti per determinare la disposizione di un'interfaccia utente.

Tutti gli ambienti del tratto costituiscono una gerarchia, come illustrato nel diagramma seguente:

 [![](unified-storyboards-images/viewhierarchy.png "The Trait Environments hierarchy diagram")](unified-storyboards-images/viewhierarchy.png#lightbox)

Per impostazione predefinita, la raccolta di tratti che ognuno degli ambienti di tratti descritti in precedenza deve fluire dall'elemento padre all'ambiente figlio.

Oltre a ottenere la raccolta dei tratti correnti, l'ambiente del tratto dispone di un `TraitCollectionDidChange` metodo, che può essere sottoposto a override nelle sottoclassi della vista o del controller di visualizzazione. Lo sviluppatore può utilizzare questo metodo per modificare gli elementi dell'interfaccia utente che dipendono da tratti quando tali tratti sono stati modificati.

## <a name="typical-trait-collections"></a>Raccolte di tratti tipici

In questa sezione vengono descritti i tipi tipici di raccolte di tratti che l'utente proverà a usare con iOS 8.

Di seguito è riportato un tipico insieme di tratti che lo sviluppatore potrebbe vedere su un iPhone:

|Proprietà|valore|
|--- |--- |
|`HorizontalSizeClass`|Compact|
|`VerticalSizeClass`|Regular|
|`UserInterfaceIdom`|Layout|
|`DisplayScale`|2.0|

Il set precedente rappresenterebbe una raccolta di tratti completa, in quanto dispone di valori per tutte le relative proprietà del tratto.

È anche possibile disporre di una raccolta di tratti in cui mancano alcuni dei relativi valori (a cui Apple fa riferimento come non *specificato*):

|Proprietà|valore|
|--- |--- |
|`HorizontalSizeClass`|Compact|
|`VerticalSizeClass`|Non specificata|
|`UserInterfaceIdom`|Non specificata|
|`DisplayScale`|Non specificata|

In genere, tuttavia, quando lo sviluppatore chiede l'ambiente del tratto per la raccolta di tratti, restituirà una raccolta completa, come illustrato nell'esempio precedente.

Se un ambiente del tratto, ad esempio un controller di visualizzazione o di visualizzazione, non si trova all'interno della gerarchia di visualizzazione corrente, lo sviluppatore potrebbe ottenere valori non specificati per una o più proprietà del tratto.

Lo Sviluppatore otterrà anche una raccolta di tratti parzialmente qualificata se usa uno dei metodi di creazione forniti da Apple, ad esempio `UITraitCollection.FromHorizontalSizeClass` , per creare una nuova raccolta.

Un'operazione che può essere eseguita su più raccolte di tratti è il confronto tra loro, che implica la richiesta di una raccolta di tratti se ne contiene un'altra. Il significato del *contenimento* è che, per qualsiasi tratto specificato nella seconda raccolta, il valore deve corrispondere esattamente al valore nella prima raccolta.

Per testare due tratti, usare il `Contains` metodo di `UITraitCollection` passando il valore del tratto da testare.

Lo sviluppatore può eseguire i confronti manualmente nel codice per determinare la modalità di layout delle visualizzazioni o dei controller di visualizzazione. Tuttavia, `UIKit` utilizza questo metodo internamente per fornire alcune delle funzionalità, come nel proxy di aspetto, ad esempio.

## <a name="appearance-proxy"></a>Proxy aspetto

Il proxy di aspetto è stato introdotto nelle versioni precedenti di iOS per consentire agli sviluppatori di personalizzare le proprietà delle visualizzazioni. È stata estesa in iOS 8 per supportare le raccolte di tratti.

I proxy di aspetto ora includono un nuovo metodo, `AppearanceForTraitCollection` , che restituisce un nuovo proxy di aspetto per la raccolta di tratti specificata che è stata passata. Tutte le personalizzazioni eseguite dallo sviluppatore sul proxy di aspetto diverranno effettive solo nelle viste conformi alla raccolta di tratti specificata.

In genere lo sviluppatore passa una raccolta di tratti parzialmente specificata al `AppearanceForTraitCollection` metodo, ad esempio un oggetto che ha appena specificato una classe di dimensioni orizzontali Compact, in modo che sia possibile personalizzare qualsiasi visualizzazione nell'applicazione compatta orizzontalmente.

## <a name="uiimage"></a>UIImage

Un'altra classe a cui Apple ha aggiunto la raccolta di tratti è `UIImage` . In passato lo sviluppatore doveva specificare una @1X @2x versione di e di qualsiasi asset grafico bitmap da includere nell'applicazione (ad esempio un'icona).

iOS 8 è stato ampliato per consentire allo sviluppatore di includere più versioni di un'immagine in un catalogo immagini in base a una raccolta di tratti. Ad esempio, lo sviluppatore potrebbe includere un'immagine più piccola per l'utilizzo di una classe di tratto compatta e un'immagine di dimensioni complete per qualsiasi altra raccolta.

Quando una delle immagini viene usata all'interno di una `UIImageView` classe, la visualizzazione immagine visualizzerà automaticamente la versione corretta dell'immagine per la raccolta di tratti. Se l'ambiente del tratto cambia (ad esempio, l'utente che passa dal ritratto a quello orizzontale), la visualizzazione immagine seleziona automaticamente le nuove dimensioni dell'immagine in modo che corrispondano alla nuova raccolta di tratti e ne modifica le dimensioni in modo che corrispondano a quelle della versione corrente dell'immagine da visualizzare.

## <a name="uiimageasset"></a>UIImageAsset

Apple ha aggiunto una nuova classe a iOS 8 denominata `UIImageAsset` per offrire allo sviluppatore un maggiore controllo sulla selezione di immagini.

Un asset di immagine esegue il wrapping di tutte le diverse versioni di un'immagine e consente allo sviluppatore di richiedere un'immagine specifica che corrisponda a una raccolta di tratti passata. Le immagini possono essere aggiunte o rimosse da un asset di immagine in tempo reale.

Per altre informazioni sugli asset di immagine, vedere la documentazione di Apple [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset) .

## <a name="combining-trait-collections"></a>Combinazione di raccolte di tratti

Un'altra funzione che uno sviluppatore può eseguire sulle raccolte di tratti consiste nell'aggiungere due insieme che comporteranno la raccolta combinata, in cui i valori non specificati di una raccolta vengono sostituiti dai valori specificati in un secondo. Questa operazione viene eseguita usando il `FromTraitsFromCollections` metodo della `UITraitCollection` classe.

Come indicato in precedenza, se uno dei tratti non è specificato in una delle raccolte di tratti e viene specificato in un altro, il valore verrà impostato sulla versione specificata. Tuttavia, se sono presenti più versioni di un determinato valore, il valore dell'ultima raccolta di tratti sarà il valore usato.

## <a name="adaptive-view-controllers"></a>Controller di visualizzazione adattivo

In questa sezione vengono illustrati i dettagli del modo in cui i controller di visualizzazione e visualizzazione iOS hanno adottato i concetti di tratti e classi di dimensioni per essere automaticamente più adattivi nelle applicazioni dello sviluppatore.

### <a name="split-view-controller"></a>Controller di visualizzazione divisa

Una delle classi controller di visualizzazione che hanno modificato il maggior numero di iOS 8 è la `UISplitViewController` classe. In passato, lo sviluppatore avrebbe usato spesso un controller Split View nella versione iPad dell'applicazione e quindi avrebbe dovuto fornire una versione completamente diversa della gerarchia di visualizzazione per la versione dell'app per iPhone.

In iOS 8 la `UISplitViewController` classe è disponibile su entrambe le piattaforme (iPad e iPhone), che consente allo sviluppatore di creare una gerarchia del controller di visualizzazione che funzionerà sia per iPhone che per iPad.

Quando un iPhone è in orizzontale, il controller di visualizzazione divisa presenta le visualizzazioni side-by-Side, così come se fosse visualizzato su un iPad.

### <a name="overriding-traits"></a>Override di tratti

Gli ambienti di tratto si propagano dal contenitore padre ai contenitori figlio, come nel grafico seguente che mostra un controller di visualizzazione suddiviso in un iPad con orientamento orizzontale:

 [![](unified-storyboards-images/cascadingclasses01.png "A Split View Controller on an iPad in the landscape orientation")](unified-storyboards-images/cascadingclasses01.png#lightbox)

Poiché l'iPad ha una classe di dimensioni regolari negli orientamenti orizzontali e verticali, la visualizzazione divisa visualizzerà sia le visualizzazioni master che i dettagli.

In un iPhone, in cui la classe Size è compatta in entrambi gli orientamenti, il controller di visualizzazione divisa Visualizza solo la visualizzazione dettagli, come illustrato di seguito:

 [![](unified-storyboards-images/cascadingclasses02.png "The Split View Controller only displays the detail view")](unified-storyboards-images/cascadingclasses02.png#lightbox)

In un'applicazione in cui lo sviluppatore vuole visualizzare la visualizzazione master e i dettagli in un iPhone con orientamento orizzontale, lo sviluppatore deve inserire un contenitore padre per il controller di visualizzazione suddiviso ed eseguire l'override della raccolta di tratti. Come illustrato nell'immagine seguente:

 [![](unified-storyboards-images/cascadingclasses03.png "The developer must insert a parent container for the Split View Controller and override the Trait Collection")](unified-storyboards-images/cascadingclasses03.png#lightbox)

Un `UIView` viene impostato come elemento padre del controller di visualizzazione suddiviso e il `SetOverrideTraitCollection` metodo viene chiamato sulla visualizzazione che passa in una nuova raccolta di tratti e che fa riferimento al controller di visualizzazione divisa. La nuova raccolta di tratti esegue l'override `HorizontalSizeClass` di, impostando il parametro `Regular` su, in modo che il controller di visualizzazione divisa visualizzi sia le visualizzazioni master che i dettagli su un iPhone con orientamento orizzontale.

Si noti che `VerticalSizeClass` è stato impostato su `unspecified` , che consente di aggiungere la nuova raccolta di tratti alla raccolta di tratti nell'elemento padre, ottenendo un oggetto `Compact VerticalSizeClass` per il controller di visualizzazione suddivisione figlio.

### <a name="trait-changes"></a>Modifiche del tratto

In questa sezione verrà illustrato in dettaglio in che modo le raccolte di tratti cambiano quando viene modificato l'ambiente del tratto. Ad esempio, quando il dispositivo viene ruotato da verticale a orizzontale.

 [![](unified-storyboards-images/traittransitions01.png "The portrait to landscape Trait Changes overview")](unified-storyboards-images/traittransitions01.png#lightbox)

In primo luogo, iOS 8 esegue alcune operazioni di installazione per preparare la transizione. Successivamente, il sistema anima lo stato di transizione. Infine, iOS 8 pulisce tutti gli stati temporanei richiesti durante la transizione.

iOS 8 fornisce diverse richiamate che lo sviluppatore può usare per partecipare alla modifica del tratto, come illustrato nella tabella seguente:

|Fase|Callback|Descrizione|
|--- |--- |--- |
|Configurazione|<ul><li>`WillTransitionToTraitCollection`</li><li>`TraitCollectionDidChange`</li></ul>|<ul><li>Questo metodo viene chiamato all'inizio di una modifica del tratto prima che un insieme di tratti venga impostato sul nuovo valore.</li><li>Il metodo viene chiamato quando il valore della raccolta di tratti è stato modificato ma prima che venga eseguita un'animazione.</li></ul>|
|Animazione|`WillTransitionToTraitCollection`|Il coordinatore della transizione che viene passato a questo metodo dispone di una `AnimateAlongside` proprietà che consente allo sviluppatore di aggiungere animazioni che verranno eseguite insieme alle animazioni predefinite.|
|Eliminazione|`WillTransitionToTraitCollection`|Fornisce un metodo che consente agli sviluppatori di includere il proprio codice di pulitura dopo che è stata effettuata la transizione.|

Il `WillTransitionToTraitCollection` metodo è ideale per animare i controller di visualizzazione insieme alle modifiche apportate alla raccolta dei tratti. Il `WillTransitionToTraitCollection` metodo è disponibile solo nei controller di visualizzazione ( `UIViewController` ) e non in altri ambienti di tratti, ad esempio `UIViews` .

`TraitCollectionDidChange`È ideale per lavorare con la `UIView` classe, in cui lo sviluppatore vuole aggiornare l'interfaccia utente mentre i tratti cambiano.

### <a name="collapsing-the-split-view-controllers"></a>Compressione dei controller di visualizzazione divisa

Si esaminerà ora cosa accade quando un controller di visualizzazione suddiviso viene compresso da due colonne a una visualizzazione a una colonna. Come parte di questa modifica, è necessario che si verifichino due processi:

- Per impostazione predefinita, il controller di visualizzazione divisa utilizzerà il controller di visualizzazione principale come vista dopo la compressione. Lo sviluppatore può eseguire l'override di questo comportamento eseguendo l'override del `GetPrimaryViewControllerForCollapsingSplitViewController` metodo di `UISplitViewControllerDelegate` e fornendo qualsiasi controller di visualizzazione che desidera visualizzare nello stato compresso.
- Il controller di visualizzazione secondario deve essere unito al controller di visualizzazione principale. Generalmente lo sviluppatore non dovrà eseguire alcuna azione per questo passaggio. il controller Split View include la gestione automatica di questa fase in base al dispositivo hardware. Tuttavia, potrebbero esserci alcuni casi speciali in cui lo sviluppatore vuole interagire con questa modifica. La chiamata al `CollapseSecondViewController` metodo dell'oggetto `UISplitViewControllerDelegate` consente di visualizzare il controller di visualizzazione master quando si verifica il collasso anziché la visualizzazione dettagli.

### <a name="expanding-the-split-view-controller"></a>Espansione del controller di visualizzazione divisa

Esaminiamo ora cosa accade quando un controller di visualizzazione suddiviso viene espanso da uno stato compresso. Ancora una volta, è necessario eseguire due fasi:

- Definire innanzitutto il nuovo controller di visualizzazione principale. Per impostazione predefinita, il controller di visualizzazione divisa utilizzerà automaticamente il controller di visualizzazione principale dalla vista compressa. Anche in questo caso, lo sviluppatore può eseguire l'override di questo comportamento usando il `GetPrimaryViewControllerForExpandingSplitViewController` metodo di `UISplitViewControllerDelegate` .
- Una volta scelto il controller di visualizzazione principale, è necessario ricreare il controller di visualizzazione secondaria. Anche in questo caso, il controller di visualizzazione suddiviso include la gestione automatica di questa fase in base al dispositivo hardware. Lo sviluppatore può eseguire l'override di questo comportamento chiamando il `SeparateSecondaryViewController` metodo di `UISplitViewControllerDelegate` .

In un controller di visualizzazione suddiviso, il controller di visualizzazione principale svolge una parte dell'espansione e della compressione delle visualizzazioni implementando i `CollapseSecondViewController` `SeparateSecondaryViewController` metodi e di `UISplitViewControllerDelegate` . `UINavigationController`implementa questi metodi per effettuare automaticamente il push e il pop del controller di visualizzazione secondaria.

### <a name="showing-view-controllers"></a>Visualizzazione di controller di visualizzazione

Un'altra modifica apportata da Apple a iOS 8 è la modalità con cui lo sviluppatore Mostra i controller di visualizzazione. In passato, se l'applicazione dispone di un controller di visualizzazione foglia, ad esempio un controller di visualizzazione tabella, e lo sviluppatore ne ha mostrato uno diverso (ad esempio, in risposta all'utente che tocca una cella), l'applicazione raggiungerà di nuovo la gerarchia del controller per il controller di visualizzazione di navigazione e chiamerà il `PushViewController` metodo per visualizzare la nuova visualizzazione.

Questo ha presentato un accoppiamento molto stretto tra il controller di spostamento e l'ambiente in cui era in esecuzione. In iOS 8, Apple ha separato questa impostazione fornendo due nuovi metodi:

- `ShowViewController`: Adatta per visualizzare il nuovo controller di visualizzazione in base al relativo ambiente. Ad esempio, in un `UINavigationController` è sufficiente inserire la nuova visualizzazione nello stack. In un controller di visualizzazione divisa, il nuovo controller di visualizzazione viene visualizzato sul lato sinistro come nuovo controller di visualizzazione principale. Se non è presente alcun controller di visualizzazione del contenitore, la nuova vista verrà visualizzata come controller di visualizzazione modale.
- `ShowDetailViewController`– Funziona in modo simile a `ShowViewController` , ma viene implementato in un controller di visualizzazione suddiviso per sostituire la visualizzazione dettagli con il nuovo controller di visualizzazione passato. Se il controller di visualizzazione suddiviso è compresso (come potrebbe essere visualizzato in un'applicazione iPhone), la chiamata verrà reindirizzata al `ShowViewController` metodo e la nuova visualizzazione verrà visualizzata come controller di visualizzazione principale. Anche in questo caso, se non è presente alcun controller di visualizzazione del contenitore, la nuova vista verrà visualizzata come controller di visualizzazione modale.

Questi metodi funzionano iniziando dal controller di visualizzazione foglia e analizzano la gerarchia di visualizzazione finché non trovano il controller di visualizzazione del contenitore appropriato per gestire la visualizzazione della nuova visualizzazione.

Gli sviluppatori possono implementare `ShowViewController` e `ShowDetailViewController` nei propri controller di visualizzazione personalizzati per ottenere le stesse funzionalità automatizzate `UINavigationController` fornite da e `UISplitViewController` .

### <a name="how-it-works"></a>Funzionamento

Questa sezione illustra il modo in cui questi metodi sono implementati in iOS 8. Per prima cosa, esaminiamo il nuovo `GetTargetForAction` Metodo:

 [![](unified-storyboards-images/gettargetforaction.png "The new GetTargetForAction method")](unified-storyboards-images/gettargetforaction.png#lightbox)

Questo metodo percorre la catena della gerarchia finché non viene trovato il controller di visualizzazione del contenitore corretto. Ad esempio:

1. Se `ShowViewController` viene chiamato un metodo, il primo controller di visualizzazione nella catena che implementa questo metodo è il controller di spostamento, quindi viene utilizzato come elemento padre della nuova visualizzazione.
1. Se `ShowDetailViewController` invece è stato chiamato un metodo, il controller di visualizzazione divisa è il primo controller di visualizzazione per implementarlo, quindi viene usato come padre.

Il `GetTargetForAction` metodo funziona individuando un controller di visualizzazione che implementa una determinata azione e quindi richiedendo a tale controller di visualizzazione se desidera ricevere l'azione. Poiché questo metodo è pubblico, gli sviluppatori possono creare metodi personalizzati che funzionano esattamente come i metodi incorporati `ShowViewController` e `ShowDetailViewController` .

## <a name="adaptive-presentation"></a>Presentazione adattiva

In iOS 8, Apple ha reso anche le presentazioni di popover ( `UIPopoverPresentationController` ). Quindi, un controller di visualizzazione di presentazione di popopover presenta automaticamente una normale visualizzazione popopov in una classe di dimensioni regolari, ma lo Visualizza a schermo intero in una classe di dimensioni compatte orizzontalmente, ad esempio in un iPhone.

Per adattare le modifiche all'interno del sistema storyboard unificato, è stato creato un nuovo oggetto controller per gestire i controller di visualizzazione presentati, ovvero `UIPresentationController` . Questo controller viene creato a partire dal momento in cui viene presentato il controller di visualizzazione finché non viene rilasciata. Poiché si tratta di una classe di gestione, può essere considerata una superclasse sul controller di visualizzazione poiché risponde alle modifiche del dispositivo che interessano il controller di visualizzazione, ad esempio l'orientamento, che vengono quindi riportate nel controller di visualizzazione che controlla il controller di presentazione.

Quando lo sviluppatore presenta un controller di visualizzazione che usa il `PresentViewController` metodo, la gestione del processo di presentazione viene passata a `UIKit` . UIKit gestisce (tra le altre cose) il controller corretto per lo stile che viene creato, con l'unica eccezione quando uno stile del controller di visualizzazione è impostato su `UIModalPresentationCustom` . Qui, l'applicazione può fornire la propria PresentationController anziché usare il `UIKit` controller.

### <a name="custom-presentation-styles"></a>Stili di presentazione personalizzati

Con uno stile di presentazione personalizzato, gli sviluppatori hanno la possibilità di utilizzare un controller di presentazione personalizzato. Questo controller personalizzato può essere usato per modificare l'aspetto e il comportamento della vista a cui è associata.

<a name="size-classes"></a>

## <a name="working-with-size-classes"></a>Utilizzo delle classi di dimensioni

Il progetto Novell Photos adattivo incluso in questo articolo offre un esempio funzionante di uso delle classi di dimensioni e dei controller di visualizzazione adattivo in un'applicazione iOS 8 Unified Interface.

Mentre l'applicazione crea completamente l'interfaccia utente dal codice, anziché usare IOS designer e creare uno storyboard unificato, si applicano le stesse tecniche. Più avanti in questo articolo verrà illustrato come usare le classi di dimensioni con uno storyboard unificato e iOS designer in un'applicazione Novell.

Si esaminerà ora il modo in cui il progetto foto adattivo implementa diverse funzionalità della classe size in iOS 8 per creare un'applicazione adattiva.

### <a name="adapting-to-trait-environment-changes"></a>Adattamento alle modifiche dell'ambiente del tratto

Quando si esegue l'applicazione Adaptive Photos su un iPhone, quando l'utente ruota il dispositivo da verticale a orizzontale, il controller Split View Visualizza sia la visualizzazione master che i dettagli:

 [![](unified-storyboards-images/rotation.png "The Split View Controller will display both the master and details view as seen here")](unified-storyboards-images/rotation.png#lightbox)

Questa operazione viene eseguita eseguendo l'override del `UpdateConstraintsForTraitCollection` metodo del controller di visualizzazione e modificando i vincoli in base al valore di `VerticalSizeClass` . Ad esempio:

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

Quando il controller di visualizzazione divisa nell'applicazione foto adattiva passa da compresso a espanso, le animazioni vengono aggiunte alle animazioni predefinite eseguendo l'override del `WillTransitionToTraitCollection` metodo del controller di visualizzazione. Ad esempio:

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

### <a name="overriding-the-trait-environment"></a>Override dell'ambiente del tratto

Come illustrato in precedenza, l'applicazione Adaptive Photos impone al controller Split View di visualizzare i dettagli e le visualizzazioni master quando il dispositivo iPhone si trova nella visualizzazione orizzontale.

Questa operazione è stata eseguita usando il codice seguente nel controller di visualizzazione:

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

### <a name="expanding-and-collapsing-the-split-view-controller"></a>Espansione e compressione del controller di visualizzazione divisa

Si esaminerà quindi come il comportamento di espansione e compressione del controller di visualizzazione suddivisa è stato implementato in Novell. In `AppDelegate` , quando viene creato il controller di visualizzazione divisa, il delegato viene assegnato per gestire le modifiche:

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

Il `SeparateSecondaryViewController` metodo verifica se una foto viene visualizzata ed esegue un'azione in base a tale stato. Se non viene visualizzata alcuna foto, il controller di visualizzazione secondario viene compresso in modo da visualizzare il controller di visualizzazione master.

Il `CollapseSecondViewController` metodo viene utilizzato quando si espande il controller di visualizzazione divisa per verificare se sono presenti foto nello stack, in caso contrario viene compresso nuovamente in tale visualizzazione.

### <a name="moving-between-view-controllers"></a>Passaggio tra controller di visualizzazione

Verrà ora esaminato il modo in cui l'applicazione Adaptive Photos viene spostata tra i controller di visualizzazione. Nella `AAPLConversationViewController` classe quando l'utente seleziona una cella dalla tabella, `ShowDetailViewController` viene chiamato il metodo per visualizzare la visualizzazione Dettagli:

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

### <a name="displaying-disclosure-indicators"></a>Visualizzazione degli indicatori di divulgazione

Nell'applicazione foto adattiva sono disponibili diverse posizioni in cui gli indicatori di divulgazione sono nascosti o visualizzati in base alle modifiche apportate all'ambiente del tratto. Questa operazione viene gestita con il codice seguente:

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

Vengono implementate usando il `GetTargetViewControllerForAction` metodo descritto in dettaglio in precedenza.

Quando un controller di visualizzazione tabella Visualizza i dati, USA i metodi implementati in precedenza per verificare se si verifica o meno un push e se visualizzare o nascondere l'indicatore di divulgazione di conseguenza:

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

Apple ha aggiunto un nuovo tipo di notifica per lavorare con le classi di dimensioni e gli ambienti di tratti dall'interno di un controller di visualizzazione suddiviso `ShowDetailTargetDidChangeNotification` . Questa notifica viene inviata ogni volta che viene modificata la visualizzazione dei dettagli di destinazione per un controller di visualizzazione divisa, ad esempio quando il controller si espande o si comprime.

L'applicazione Adaptive Photos usa questa notifica per aggiornare lo stato dell'indicatore di divulgazione quando cambia il controller di visualizzazione Dettagli:

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

Esaminare più da vicino l'applicazione Adaptive Photos per visualizzare tutti i modi in cui le classi di dimensioni, le raccolte di tratti e i controller di visualizzazione adattivo possono essere usati per creare facilmente un'applicazione unificata in Novell. iOS.

## <a name="unified-storyboards"></a>Storyboard unificati

Una novità di iOS 8, gli storyboard unificati consentono allo sviluppatore di creare un file storyboard unificato che può essere visualizzato nei dispositivi iPhone e iPad selezionando più classi di dimensioni. Utilizzando gli storyboard unificati, lo sviluppatore scrive meno codice specifico dell'interfaccia utente e dispone solo di un progetto di interfaccia da creare e gestire.

I vantaggi principali degli storyboard unificati sono:

- Usare lo stesso file di storyboard per iPhone e iPad.
- Eseguire la distribuzione all'indietro in iOS 6 e iOS 7.
- Visualizzare in anteprima il layout per diversi dispositivi, orientamenti e versioni del sistema operativo all'interno di Novell iOS designer.

Questa funzionalità è completamente supportata in Visual Studio per Mac

<a name="enabling-size-classes"></a>

### <a name="enabling-size-classes"></a>Abilitazione di classi di dimensioni

Per impostazione predefinita, qualsiasi nuovo progetto Novell. iOS ci consentirà di ridimensionare le classi. Per usare le classi di dimensioni e i gli elementi segue adattivi all'interno di uno storyboard da un progetto precedente, è necessario prima convertirlo nel formato dello storyboard unificato di Xcode 6 dall'interno della finestra di progettazione di iOS.

A tale scopo, aprire lo storyboard da convertire nella finestra di progettazione iOS e selezionare la casella di controllo **usa classi di dimensioni** :

 [![](unified-storyboards-images/sizeclass01.png "The Use Size Classes check box")](unified-storyboards-images/sizeclass01.png#lightbox)

IOS designer conferma che lo sviluppatore vuole convertire il formato dello storyboard per usare le classi di dimensioni:

 [![](unified-storyboards-images/sizeclass02.png "The use Size Classes alert")](unified-storyboards-images/sizeclass02.png#lightbox)

> [!IMPORTANT]
> Per il corretto funzionamento delle classi di dimensioni, è necessario verificare anche il layout automatico.

### <a name="generic-device-types"></a>Tipi di dispositivi generici

Una volta che lo storyboard è stato convertito per usare le classi di dimensioni, verrà visualizzato nuovamente nella Area di progettazione e la **visualizzazione come** dispositivo sarà generica:

 [![](unified-storyboards-images/sizeclass03.png "View as a Generic device type")](unified-storyboards-images/sizeclass03.png#lightbox)

Quando si seleziona il tipo di dispositivo generico, tutti i controller di visualizzazione verranno ridimensionati in un quadrato di 600 x 600. Questo quadrato rappresenta le dimensioni di qualsiasi larghezza e qualsiasi altezza. Quando iOS designer è in questa modalità, tutte le modifiche verranno applicate a tutte le classi di dimensioni.

Lo sviluppatore ha anche la possibilità di visualizzare l'area di progettazione come un iPhone:

 [![](unified-storyboards-images/sizeclass04.png "Viewing the design surface as an iPhone")](unified-storyboards-images/sizeclass04.png#lightbox)

Oppure visualizzarlo come iPad:

 [![](unified-storyboards-images/sizeclass05.png "Viewing the design surface as an iPad")](unified-storyboards-images/sizeclass05.png#lightbox)

### <a name="select-a-size-class"></a>Selezionare una classe di dimensioni

Il pulsante di selezione della classe Size si trova nell'angolo superiore sinistro del Area di progettazione (accanto alla visualizzazione come elenco a discesa). Consente allo sviluppatore di selezionare le classi di dimensioni attualmente in fase di modifica:

 [![](unified-storyboards-images/sizeclass06.png "Select a Size Class")](unified-storyboards-images/sizeclass06.png#lightbox)

Il selettore presenta la selezione della classe Size come griglia 3 x 3. Ognuno dei quadrati della griglia rappresenta una combinazione di una classe Width e una classe Height. Il quadrato centrale seleziona la classe any Width/any Height size (che è la visualizzazione predefinita per uno storyboard unificato). Quando questo quadrato è selezionato, lo sviluppatore sta modificando il layout predefinito, che viene ereditato da tutte le altre configurazioni.

Il quadrato nell'angolo superiore sinistro della griglia rappresenta la classe Compact Width/Compact Height Size:

 [![](unified-storyboards-images/sizeclass07.png "The Compact Width/Compact Height Size Class")](unified-storyboards-images/sizeclass07.png#lightbox)

Questa modalità corrisponde a un iPhone con orientamento orizzontale. Il quadrato nell'angolo inferiore destro della griglia rappresenta la classe Regular Width/Regular Height size, che rappresenta un iPad:

 [![](unified-storyboards-images/sizeclass08.png "The Regular Width/Regular Height Size Class")](unified-storyboards-images/sizeclass08.png#lightbox)

Per modificare il layout per un iPhone con orientamento verticale, selezionare il quadrato nell'angolo in basso a sinistra. Rappresenta la classe Compact Width/Regular Height Size:

 [![](unified-storyboards-images/sizeclass09.png "The Compact Width/Regular Height Size Class")](unified-storyboards-images/sizeclass09.png#lightbox)

Fare clic sul quadrato per selezionarlo e il Area di progettazione modificherà le dimensioni dei controller di visualizzazione in modo che corrispondano alla nuova selezione:

 [![](unified-storyboards-images/sizeclass10.png "The Design Surface will change the size of the View Controllers to match the new selection as shown")](unified-storyboards-images/sizeclass10.png#lightbox)

Vedere la sezione relativa alla classe di dimensioni di questo articolo per altre informazioni sulle classi di dimensioni e sul modo in cui influiscono sul layout per iPhone e iPad.

### <a name="adaptive-segue-types"></a>Tipi di segue adattivi

Se lo sviluppatore ha usato gli storyboard in precedenza, avrà familiarità con i tipi di segue esistenti di **push**, **modale** e **popover**. Quando le classi di dimensioni sono abilitate in un file storyboard unificato, vengono resi disponibili i seguenti tipi segue adattivi (che corrispondono alla nuova API del controller di visualizzazione descritta in precedenza): **Mostra** e **Mostra dettagli**.

> [!IMPORTANT]
> Quando le classi di dimensioni sono abilitate, qualsiasi gli elementi segue esistente verrà convertito nei nuovi tipi.

Prendere in considerazione l'esempio di un'applicazione iOS 8 che usa uno storyboard unificato con un controller di visualizzazione suddiviso con un semplice menu di navigazione del gioco nella visualizzazione master. Se l'utente fa clic su un pulsante di menu, il controller di visualizzazione dell'elemento selezionato dovrebbe essere visualizzato nella sezione dei dettagli del controller di visualizzazione divisa quando viene eseguito su un iPad. In un iPhone il controller di visualizzazione dell'elemento deve essere inserito nello stack di navigazione.

Per ottenere questo risultato, nel controllo iOS Designer fare clic sul pulsante e trascinare una riga sul controller di visualizzazione da visualizzare. Quando si rilascia il pulsante del mouse, selezionare `Show Detail` dal menu popup del tipo di segue:

 [![](unified-storyboards-images/segue01.png "Select Show Detail from the Segue Type Popup menu")](unified-storyboards-images/segue01.png#lightbox)

Il nuovo segue verrà creato tra il pulsante e il controller di visualizzazione. A questo punto, eseguire l'applicazione nel simulatore iPhone. verrà visualizzato il menu principale:

 [![](unified-storyboards-images/segue02.png "The Main Menu")](unified-storyboards-images/segue02.png#lightbox)

Fai clic sul pulsante **Seleziona gioco** e il controller di visualizzazione dell'elemento verrà inserito nello stack di navigazione:

 [![](unified-storyboards-images/segue03.png "The items View Controller will be pushed onto the Navigation Stack as shown")](unified-storyboards-images/segue03.png#lightbox)

Arrestare il simulatore iPhone ed eseguire l'applicazione nel simulatore iPad. Passare all'orientamento orizzontale e visualizzare di nuovo il menu principale:

 [![](unified-storyboards-images/segue04.png "The main menu displayed")](unified-storyboards-images/segue04.png#lightbox)

Anche in questo caso fare clic sul pulsante **Seleziona gioco** e il controller di visualizzazione dell'elemento viene visualizzato nella sezione dettagli del controller di visualizzazione divisa:

 [![](unified-storyboards-images/segue05.png "The items View Controller shown in the Details section of the Split View Controller")](unified-storyboards-images/segue05.png#lightbox)

### <a name="excluding-an-element-from-a-size-class"></a>Esclusione di un elemento da una classe Size

In alcuni casi un dato elemento, ad esempio una vista, un controllo o un vincolo, non è necessario all'interno di una classe di dimensioni specifiche. Per escludere un elemento da una classe size, selezionare l'elemento desiderato da escludere nella **area di progettazione**. Scorrere fino alla fine di **Esplora proprietà** e fare clic sul menu a discesa dell' **ingranaggio** . Selezionare la combinazione di **larghezza** e **altezza** da cui escludere l'elemento:

[![](unified-storyboards-images/exclude-a.png "Select the combination of Width and Height")](unified-storyboards-images/exclude-a.png#lightbox)

Un nuovo *caso di esclusione* verrà aggiunto all'elemento nella parte inferiore di **Esplora proprietà**. Deselezionare quindi la casella di controllo **installato** per la classe Size specificata:

[![](unified-storyboards-images/exclude-b.png "Uncheck the Installed checkbox")](unified-storyboards-images/exclude-b.png#lightbox)

Passare il Area di progettazione alla larghezza e all'altezza da cui l'elemento è stato escluso, che è stato rimosso dalla classe Size specificata, ma non dall'intera progettazione dell'interfaccia utente:

 [![](unified-storyboards-images/exclude02.png "Switch the Design Surface to the Width and Height that the item was excluded from")](unified-storyboards-images/exclude02.png#lightbox)

Tornando alla classe any Width/any Height size e l'elemento è ancora disponibile:

 [![](unified-storyboards-images/exclude03.png "Switching back to the Any Width/Any Height size class")](unified-storyboards-images/exclude03.png#lightbox)

Quando l'applicazione viene eseguita nel simulatore iPad, viene visualizzato l'elemento:

 [![](unified-storyboards-images/exclude04.png "The element shown when the running app in the iPad Simulator")](unified-storyboards-images/exclude04.png#lightbox)

Quando l'applicazione viene eseguita nel simulatore iPhone, l'elemento è mancante:

 [![](unified-storyboards-images/exclude05.png "The element missing when the running app in the iPhone Simulator")](unified-storyboards-images/exclude05.png#lightbox)

Per rimuovere un caso di esclusione da un elemento, è sufficiente selezionare l'elemento nella **area di progettazione**, scorrere fino alla fine di **Esplora proprietà** e fare clic sul **-** pulsante accanto al case da rimuovere.

Per visualizzare un'implementazione degli storyboard unificati, vedere l'applicazione di `UnifiedStoryboard` esempio Novell iOS 8 collegata a questo documento.

## <a name="dynamic-launch-screens"></a>Schermate di avvio dinamico

Il file della schermata di avvio viene visualizzato come schermata iniziale mentre viene avviata un'applicazione iOS per fornire commenti e suggerimenti all'utente che l'app è effettivamente avviata. Prima di iOS 8, lo sviluppatore avrebbe dovuto includere più `Default.png` Asset immagine per ogni tipo di dispositivo, orientamento e risoluzione dello schermo in cui l'applicazione verrebbe eseguita. Ad esempio,,, `Default@2x.png` `Default-Landscape@2x~ipad.png` `Default-Portrait@2x~ipad.png` e così via.

Il factoring nei nuovi dispositivi iPhone 6 e iPhone 6 Plus (e il prossimo Apple Watch) con tutti i dispositivi iPhone e iPad esistenti, rappresenta un'ampia gamma di dimensioni variabili, orientamenti e risoluzioni delle `Default.png` risorse dell'immagine della schermata iniziale che devono essere create e gestite. Inoltre, questi file possono avere dimensioni molto elevate e comportano il "gonfiore" del bundle di applicazioni finali, aumentando la quantità di tempo necessaria per scaricare l'applicazione dall'iTunes App Store (probabilmente impedendone la recapito in una rete cellulare) e aumentando la quantità di spazio di archiviazione necessaria sul dispositivo dell'utente finale.

Una novità di iOS 8, lo sviluppatore può creare un singolo `.xib` file atomico in Xcode che usa le classi di layout e dimensioni automatiche per creare una *schermata di avvio dinamico* che funzionerà per ogni dispositivo, risoluzione e orientamento. Questo non solo riduce la quantità di lavoro richiesto allo sviluppatore per creare e gestire tutte le risorse di immagine necessarie, ma riduce notevolmente le dimensioni del bundle installato dell'applicazione.

Le schermate di avvio dinamico presentano le limitazioni e le considerazioni seguenti:

- Usare solo `UIKit` le classi.
- Usare una singola visualizzazione radice che è un `UIView` `UIViewController` oggetto o.
- Non eseguire alcuna connessione al codice dell'applicazione (non aggiungere **azioni** o **punti vendita**).
- Non aggiungere `UIWebView` oggetti.
- Non usare classi personalizzate.
- Non usare gli attributi di Runtime.

Tenendo presenti le linee guida precedenti, è possibile esaminare l'aggiunta di una schermata di avvio dinamico a un progetto Novell iOS 8 esistente.

Eseguire le operazioni seguenti:

1. Aprire **Visual Studio per Mac** e caricare la **soluzione** per aggiungere la schermata avvio dinamico a.
2. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sul `MainStoryboard.storyboard` file e scegliere **Apri con**  >  **Xcode Interface Builder**:

    [![](unified-storyboards-images/dls01.png "Open With Xcode Interface Builder")](unified-storyboards-images/dls01.png#lightbox)
3. In Xcode selezionare **file**  >  **nuovo**  >  **file...**:

    [![](unified-storyboards-images/dls02.png "Select File / New")](unified-storyboards-images/dls02.png#lightbox)
4. Selezionare **iOS**  >  la schermata di avvio**dell'interfaccia utente**iOS  >  **Launch Screen** e fare clic sul pulsante **Avanti** :

    [![](unified-storyboards-images/dls03.png "Select iOS / User Interface / Launch Screen")](unified-storyboards-images/dls03.png#lightbox)
5. Assegnare un nome al file `LaunchScreen.xib` e fare clic sul pulsante **Create (crea** ):

    [![](unified-storyboards-images/dls04.png "Name the file LaunchScreen.xib")](unified-storyboards-images/dls04.png#lightbox)
6. Modificare la progettazione della schermata di avvio aggiungendo elementi grafici e usando vincoli di layout per posizionarli per i dispositivi, gli orientamenti e le dimensioni dello schermo specificati:

    [![](unified-storyboards-images/dls05.png "Editing the design of the launch screen")](unified-storyboards-images/dls05.png#lightbox)
7. Salvare le modifiche in `LaunchScreen.xib`.
8. Selezionare la **destinazione applicazioni** e la scheda **generale** :

    [![](unified-storyboards-images/dls06.png "Select the Applications Target and the General tab")](unified-storyboards-images/dls06.png#lightbox)
9. Fare clic sul pulsante **Choose info. plist** , selezionare il `Info.plist` per l'app Novell e fare clic sul pulsante **Choose (Scegli** ):

    [![](unified-storyboards-images/dls07.png "Select the Info.plist for the Xamarin app")](unified-storyboards-images/dls07.png#lightbox)
10. Nella sezione **icone e immagini di avvio dell'app** aprire l'elenco a discesa **file schermata di avvio** e scegliere il `LaunchScreen.xib` creato sopra:

    [![](unified-storyboards-images/dls08.png "Choose the LaunchScreen.xib")](unified-storyboards-images/dls08.png#lightbox)
11. Salvare le modifiche apportate al file e tornare a Visual Studio per Mac.
12. Attendere che Visual Studio per Mac completi la sincronizzazione delle modifiche con Xcode.
13. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sulla cartella della **risorsa** e scegliere **Aggiungi**  >  **Aggiungi file...**:

    [![](unified-storyboards-images/dls09.png "Select Add / Add Files...")](unified-storyboards-images/dls09.png#lightbox)
14. Selezionare il `LaunchScreen.xib` file creato sopra e fare clic sul pulsante **Apri** :

    [![](unified-storyboards-images/dls10.png "Select the LaunchScreen.xib file")](unified-storyboards-images/dls10.png#lightbox)
15. Compilare l'applicazione.

### <a name="testing-the-dynamic-launch-screen"></a>Test della schermata di avvio dinamico

In Visual Studio per Mac selezionare il simulatore di iPhone 4 retina ed eseguire l'applicazione. La schermata di avvio dinamico verrà visualizzata nel formato e nell'orientamento corretti:

[![](unified-storyboards-images/dls11.png "The Dynamic Launch Screen displayed in the vertical orientation")](unified-storyboards-images/dls11.png#lightbox)

Arrestare l'applicazione in Visual Studio per Mac e selezionare un dispositivo iPad iOS 8. Eseguire l'applicazione e la schermata di avvio verrà formattata correttamente per il dispositivo e l'orientamento:

[![](unified-storyboards-images/dls12.png "The Dynamic Launch Screen displayed in the horizontal orientation")](unified-storyboards-images/dls12.png#lightbox)

Tornare a Visual Studio per Mac e arrestare l'esecuzione dell'applicazione.

### <a name="working-with-ios-7"></a>Uso di iOS 7

Per mantenere la compatibilità con le versioni precedenti di iOS 7, è sufficiente includere le `Default.png` normali risorse immagine come di consueto nell'applicazione iOS 8. iOS restituirà il comportamento precedente e utilizzerà tali file come schermata iniziale quando viene eseguito in un dispositivo iOS 7.

Per un'implementazione di una schermata di avvio dinamico in Novell, vedere le [schermate di avvio dinamico](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-dynamiclaunchscreen) applicazione iOS 8 di esempio collegata a questo documento.

## <a name="summary"></a>Summary

Questo articolo ha esaminato rapidamente le classi di dimensioni e il modo in cui influiscono sul layout nei dispositivi iPhone e iPad. Questo argomento ha illustrato il funzionamento dei tratti, degli ambienti di tratti e delle raccolte di tratti con le classi di dimensioni per la creazione di interfacce unificate. Sono stati esaminati brevemente i controller di visualizzazione adattiva e il modo in cui funzionano con le classi di dimensioni all'interno delle interfacce unificate. Si è cercato di implementare classi di dimensioni e interfacce unificate completamente dal codice C# all'interno di un'applicazione Novell iOS 8.

Infine, in questo articolo sono state illustrate le nozioni di base sulla creazione di storyboard unificati con Novell iOS designer, che funzionerà tra i dispositivi iOS e la creazione di una singola schermata di avvio dinamico che verrà visualizzata come schermata iniziale in ogni dispositivo iOS 8.

## <a name="related-links"></a>Collegamenti correlati

- [Foto adattive (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-adaptivephotos)
- [Schermate di avvio dinamico (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/ios8-dynamiclaunchscreen)
- [Introduzione a iOS 8](~/ios/platform/introduction-to-ios8.md)
- [Layout dinamici in iOS 8-evolve 2014 (video)](https://youtu.be/f3mMGlS-lM4)
- [UIPresentationController](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPresentationController_class/)
- [UIImageAsset](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIImageAsset_Ref/index.html#//apple_ref/occ/cl/UIImageAsset)
