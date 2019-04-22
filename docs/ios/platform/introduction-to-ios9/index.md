---
title: Introduzione a iOS 9
description: Questo articolo illustra tutte le nuove e modificate le API e funzionalità disponibili in iOS 9 per gli sviluppatori di xamarin. IOS.
ms.prod: xamarin
ms.assetid: 4D71BBD9-B948-4B59-9AF5-F199C51CBEB3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: d0ad25a1ecff7262b9b4b41a5f9d73a5931bbd1c
ms.sourcegitcommit: 3489c281c9eb5ada2cddf32d73370943342a1082
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58870196"
---
# <a name="introduction-to-ios-9"></a>Introduzione a iOS 9

_Questo articolo illustra tutte le nuove e modificate le API e funzionalità disponibili in iOS 9 per gli sviluppatori di xamarin. IOS._

![](images/ios9-sml.png "Il logo di iOS 9")

Apple ha aggiunto diverse nuove API e servizi in iOS 9 e molti miglioramenti alle funzionalità esistenti.

## <a name="3d-touch"></a>3D Touch

Novità di iOS 9 e iPhone 6s e iPhone 6s Plus, 3D Touch aggiunge i movimenti di pressione sensibili per le app iOS. Con 3D Touch, un'app per iPhone è ora in grado di comunicare non solo che l'utente tocca lo schermo del dispositivo, può anche valutare la pressione è ovvero stanno inviando all'utente di rilevare e rispondere ai livelli di pressione diversi.

3D Touch offre le funzionalità seguenti per l'app:

- **Pressione sensibilità** : le app ora è possono misurare la difficoltà o chiaro l'utente tocca la schermata e Ottieni i vantaggi di tali informazioni. Ad esempio, un'app di disegno può effettuare una linea spessa o sottili base difficile l'utente tocca la schermata.
- **Visualizza e Pop** -l'app può ora consentire all'utente di interagire con i relativi dati senza la necessità di uscire dal relativo contesto corrente. Premendo rigido sullo schermo, è possibile *Peek* nell'elemento di cui sono interessati (ad esempio, l'anteprima di un messaggio). Premendo più difficile, potranno *Pop* nell'elemento.
- **Azioni rapide** -pensare di azioni rapide, ad esempio il menu di scelta rapida che può essere estratto verticale quando l'utente fa clic su un elemento in un'app desktop. Con azioni rapide, è possibile aggiungere più comuni, rapido e semplice ai collegamenti di accesso alle funzioni nell'app dall'icona della schermata Home nel dispositivo iOS.

Per altre informazioni, vedere la [Introduzione a 3D Touch](~/ios/platform/3d-touch.md) Guida.

## <a name="app-transport-security"></a>ATS (App Transport Security)

Nuovo a iOS 9, App Transport Security (ATS) applica le connessioni sicure tra le risorse internet (ad esempio i server back-end dell'app) e l'app. ATS garantisce che tutte le comunicazioni internet siano conformi per proteggere la connessione le procedure consigliate, evitando la diffusione accidentale di informazioni riservate direttamente tramite l'app o una libreria che sta utilizzando.

Perché è abilitata per impostazione predefinita nelle App per iOS 9 e OS X 10.11 (El Capitan), tutte le connessioni usando la ATS [NSUrlConnection](xref:Foundation.NSUrlConnection), [CFUrl](xref:CoreFoundation.CFUrl) oppure [NSUrlSession](xref:Foundation.NSUrlSession) sarà soggetto a Requisiti di sicurezza ATS. Se le connessioni non soddisfano questi requisiti, si avrà esito negativo con un'eccezione.

Per altre informazioni sulle ATS, vedere la [App Transport Security](~/ios/app-fundamentals/ats.md) Guida.

<a name="multitasking" />

## <a name="multitasking-for-ipad"></a>Multitasking per iPad

Con iOS 9, Apple ha aggiunto il supporto di multitasking per l'esecuzione di due App nello stesso momento su iPad specifiche hardware. Di conseguenza, le app xamarin. IOS non possono più presupporre che sono l'unica app in esecuzione in un determinato momento o che abbiano accesso alle risorse del dispositivo o schermo intero.

Multitasking per iPad è supportato tramite le funzionalità seguenti:

- **Diapositiva su** -consente all'utente di eseguire temporaneamente una seconda app di iOS in una diapositiva pannello (sia sul lato destro o sinistro dello schermo in base alla direzione language) che copre circa il 25% dell'app principale attualmente in esecuzione. Far scorrere in è disponibile solo in un iPad Pro, iPad Air, iPad Air 2, iPad 2 Mini, iPad Mini 3 o 4 Mini iPad.
- **Doppia visualizzazione** -in componenti hardware supportati iPad (iPad Air 2, 4 Mini iPad e iPad Pro solo), l'utente può selezionare una seconda app ed eseguirlo side-by-side con l'app attualmente in esecuzione in modalità a schermo intero una divisione. L'utente può controllare la percentuale della schermata principale che occupa ogni app.
- **Picture in Picture** : per le app che riprodurre contenuto video, i video possono ora essere riprodotti in una finestra mobile e ridimensionabile mobile rispetto alle altre App attualmente in esecuzione nel dispositivo iOS. L'utente dispone del controllo completo sulla dimensione e posizione della finestra. Immagine nell'immagine è disponibile solo in un iPad Pro, iPad Air, iPad Air 2, iPad 2 Mini, iPad Mini 3 o 4 Mini iPad.

Per altre informazioni sulle nuove funzionalità il multitasking di iOS 9, vedere la [Multitasking per iPad](~/ios/platform/multitasking.md) Guida.

## <a name="new-contacts-and-contacts-ui-frameworks"></a>Nuovi contatti e i framework dell'interfaccia utente di contatti

Con l'introduzione di iOS 9, Apple ha rilasciato due nuovi Framework, [contatti](xref:Contacts) e [ContactsUI](xref:ContactsUI), che sostituisce la Rubrica esistente e Framework dell'interfaccia utente della Rubrica indirizzi usati da iOS 8 e versioni precedenti.

Questi Framework nuovo e orientato a forniscono gli elementi seguenti:

- **Contatti** : xamarin. IOS fornisce accesso alle informazioni di contatto dell'utente. Poiché la maggior parte delle App richiedono solo accesso in sola lettura, questo framework è stato ottimizzato per l'accesso thread-safe di sola lettura.
- **ContactsUI** : interfaccia utente di xamarin. IOS offre elementi da visualizzare, modificare, selezionare e creare contatti nei dispositivi iOS.

Per altre informazioni, vedere la [Contacts e l'interfaccia utente di contatti](~/ios/platform/contacts.md) documentazione.


## <a name="new-search-apis"></a>Nuove API di ricerca

Ricerca è stata estesa in iOS 9 per offrire eccezionali nuovi modi per accedere alle informazioni all'interno di app xamarin. IOS. Usa le nuove API di ricerca, è possibile rendere il contenuto dell'app disponibili per la ricerca tramite Spotlight e Safari risultati della ricerca, passaggio di consegne e Siri promemoria e suggerimenti. Ciò consente di accedere rapidamente agli utenti per le attività e le informazioni complete all'interno dell'app.

Inoltre, le nuove API di ricerca rendono più semplice integrare la ricerca nella tua app senza esperienza di implementazione di ricerca precedenti. Per questo motivo, Apple richiede che richiede in genere alcune ore per rendere il contenuto di un'app di iOS 9 universalmente disponibili per la ricerca con ricerca di App.

Per altre informazioni, vedere la [miglioramenti della ricerca](~/ios/platform/search/index.md) documentazione.

## <a name="new-stack-view"></a>Nuova visualizzazione Stack

Il controllo di visualizzazione dello Stack ([UIStackView](xref:UIKit.UIStackView) sfrutta la potenza del Layout automatico e le classi di dimensioni per gestire una pila di visualizzazioni secondarie (orizzontale o verticale) che risponde dinamicamente alle dimensioni dello schermo e l'orientamento del dispositivo iOS.

Con controllo di visualizzazione dello Stack, la quantità di lavoro necessari per layout di che un'interfaccia utente è notevolmente ridotte. Il layout di tutte le visualizzazioni secondarie associata a una visualizzazione dello Stack vengono gestite automaticamente in base alle proprietà definite per gli sviluppatori, ad esempio asse, distribuzione, allineamento e spaziatura.

Per altre informazioni, vedere la [Introduzione alla visualizzazione Stack](~/ios/user-interface/controls/uistackview.md) documentazione.


## <a name="collection-view-changes"></a>Modifiche alla vista raccolta

In iOS 9, la visualizzazione di raccolta ([UICollectionView](xref:UIKit.UICollectionView) supporta trascina ora il riordinamento degli elementi predefiniti aggiungendo un nuovo riconoscitore di movimento predefiniti e diversi nuovi metodi di supporti.

Mediante questi nuovi metodi, è possibile facilmente implementare il trascinamento-a-riordinamento nella propria visualizzazione di raccolta e hanno la possibilità di personalizzazione dell'aspetto degli elementi in qualsiasi fase del processo di riordinamento.

Per altre informazioni sulle modifiche di visualizzazione raccolta IOS 9, vedere la [le modifiche alla vista raccolta](~/ios/user-interface/controls/uicollectionview.md) Guida.

## <a name="game-enhancements"></a>Miglioramenti del gioco

Con iOS 9, Apple ha apportato diversi miglioramenti tecnologici per le API di giochi che rendono più semplice implementare grafiche di gioco e audio nell'app xamarin. IOS. Questi includono sia facilità di sviluppo tramite Framework di alto livello e sfruttare la potenza di GPU del dispositivo iOS per la maggiore velocità e dalla capacità di graphic grazie ai miglioramenti apportati a basso livello.

Insieme a funzionalità nuove e migliorate di bare Metal, SceneKit e SpriteKit inclusi GameplayKit, ReplayKit, i/o del modello, MetalKit e shader di Metal delle prestazioni.

Per altre informazioni, vedere la [miglioramenti di gioco](~/ios/platform/gaming/index.md) documentazione.

## <a name="homekit-framework-changes"></a>Modifiche del Framework HomeKit

Il [HomeKit](xref:HomeKit) framework, introdotto in iOS 8, offre la possibilità di configurare e controllare varie accessori HomeKit abilitato (ad esempio luci automatizzate, serrature e ciò sportello garage) da un'app xamarin. IOS. Oltre a essere facile da installare e configurare, accessori HomeKit possono essere controllati tramite i comandi vocali Siri.

In iOS 9 Apple ha semplificato il programma di installazione, espansi i tipi di accessori supportati e altre interazioni degli accessori (ad esempio il controllo un accessorio in modalità remota tramite iCloud) fornito.

Per altre informazioni, vedere la [Introduzione a HomeKit](~/ios/platform/homekit.md), [HomeKitIntro iOS App di esempio](https://developer.xamarin.com/samples/monotouch/HomeKit/HomeKitIntro/) e Apple [HomeKit](https://developer.apple.com/homekit/) documentazione.

## <a name="handoff-framework-changes"></a>Modifiche del Framework handoff

Esegui handoff (noto anche come continuità) è stato introdotto da Apple in iOS 8 e OS X Yosemite (10.10) come un modo per l'utente avviare un'attività in uno dei propri dispositivi (iOS o Mac) e continuare a quella stessa attività in un'altra dei propri dispositivi (come identificato dalla iClou dell'utente p Account).

Esegui handoff è stato ampliato in iOS 9 supporti anche nuove, migliorate funzionalità di ricerca. Per altre informazioni, vedere la [miglioramenti della ricerca](~/ios/platform/search/index.md) documentazione. Per altre informazioni sull'uso di consegna, vedere la [Introduzione a Handoff](~/ios/platform/handoff.md) documentazione.

## <a name="new-extension-points"></a>Nuovi punti di estensione

In iOS 8, Apple ha introdotto le estensioni, ovvero le librerie che vengono presentate dal sistema operativo in contesti standard, ad esempio il centro notifiche, quando l'utente richiede una tastiera o quando si modifica una foto.

Con iOS 9, Apple estende il supporto delle estensioni, fornendo diversi nuovi _punti di estensione_ che definiscono i criteri di utilizzo e forniscono le API per l'utilizzo all'interno di una determinata area, come indicato di seguito:

- **Nuovo punto di estensione per unità Audio** : utilizzo del punto di estensione per fornire effetti audio, strumenti musicali, i generatori di audio e così via da utilizzare all'interno di altre applicazioni host di unità Audio (ad esempio GarageBand). Questo punto di estensione consente anche di vendere _unità Audio_ (audio plug-in) su Store l'App.
- **Nuovo punto di estensione di manutenzione dell'indice** : utilizzo del punto di estensione per supportare la reindicizzazione dei dati delle app senza richiedere riavvia un'app.
- **Nuovi punti di estensione rete** (che richiedono un'autorizzazione speciale da Apple):
    - **Provider di estensione dell'App Proxy** : utilizzo del punto di estensione per implementare un proxy personalizzato trasparente rete sul lato client.
    - **Filtrare i Provider di dati / filtro di estensione per Provider di controllo** -usare questi punti di estensione per implementare il contenuto di rete dinamico filtro sul dispositivo.
    - **Estensione per Provider di pacchetti Tunnel** : utilizzo del punto di estensione per implementare una tunneling protocollo sul lato client VPN personalizzata.
- **Nuovi punti di estensione Safari**:
    - **Estensione per blocco contenuto** : utilizzo del punto di estensione per definire un elenco di contenuti bloccati che non verrà visualizzato quando l'utente Esplora il web.
    - **Estensione per collegamenti condivisi** : utilizzo del punto di estensione per abilitare la visualizzazione del contenuto dell'app nei collegamenti condivisi di Safari.

Per altre informazioni, vedere la [Introduzione alle estensioni](~/ios/platform/extensions.md) e di Apple [Guida alla programmazione di estensione App](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214) documentazione.

## <a name="keychain-enhancements"></a>Miglioramenti di Keychain

In iOS 9, Apple ha migliorato il Keychain per fornire un nuovo tipo di chiave di crittografia per l'Enclave protetta e altre opzioni di protezione dati di elemento, come indicato di seguito:

- Un nuovo vincolo di ID tocco che invalida gli elementi di Keychain quando viene modificato il database di impronta digitale.
- Nuovi vincoli che consente la creazione solo le voci dell'elenco di controllo di accesso con ID tocco o Passcode.
- Un nuovo contesto di autenticazione che consente di richiamare l'autenticazione separato dal `SecItem` chiamate.
- Accedere a entropia di elenco di controllo (usando l'opzione Password applicazione) per la crittografia di elemento fornita dall'app keychain.
- Supporto per la generazione e utilizzo di chiavi all'interno dell'enclave protetta (tramite il `kSecAttrTokenIDSecureEnclave` attributo).

Per altre informazioni, vedere la [Introduzione a Touch ID](~/ios/platform/touchid.md) documentazione.


## <a name="right-to-left-language-support"></a>Supporto di lingue da destra a sinistra

In iOS 9 Apple ha introdotto presentare un'interfaccia utente capovolta più facile che mai, fornendo il supporto completo per lingue da destra a sinistra. Il comportamento predefinito include quanto segue:

- Standard [UIKit](xref:UIKit) controlli capovolgerà automaticamente le impostazioni di lingua e i dispositivi iOS in base a destra a sinistra.
- Il [UIView](xref:UIKit.UIView) classe fornisce attributi che consentono di definire come devono essere visualizzate quando una determinata visualizzazione capovolto a destra a sinistra.
- La possibilità di capovolge un'immagine a livello di codice usando il [FlipsForRightToLeftLayoutDirection](xref:UIKit.UIImage.FlipsForRightToLeftLayoutDirection) proprietà delle [UIImage](xref:UIKit.UIImage) classe.

Per altre informazioni, vedere di Apple [linguaggi che supportano Right-to-Left](https://developer.apple.com/library/prerelease/ios/documentation/MacOSX/Conceptual/BPInternational/SupportingRight-To-LeftLanguages/SupportingRight-To-LeftLanguages.html#//apple_ref/doc/uid/10000171i-CH17) documentazione.



## <a name="additional-framework-changes"></a>Modifiche di altri Framework

Oltre alle modifiche principali che sono stati descritti sopra, Apple ha apportate le modifiche e miglioramenti per diversi Framework esistenti per iOS 9, inclusi i seguenti:

- Framework AV Foundation
- Framework AVKit
- Framework CloudKit
- Framework Foundation
- Framework handoff
- Framework di HealthKit
- Framework HomeKit
- Framework di autenticazione locale
- Framework MapKit
- Framework di PassKit
- Framework di servizi di Safari
- Framework UIKit

Per altre informazioni, vedere la [modifiche aggiuntive iOS 9 Framework](~/ios/platform/introduction-to-ios9/additional-framework-changes.md) documentazione.

## <a name="deprecated-apis-and-functions"></a>Funzioni e le API deprecate

Apple ha deprecato funzioni in iOS 9 e le API seguenti:

- **Indirizzo dell'interfaccia utente della Rubrica indirizzi & libro** -queste API sono state sostituite dai framework di contatto e l'interfaccia utente di contatto. Per altre informazioni, vedere la [Contacts e l'interfaccia utente di contatti](~/ios/platform/contacts.md) documentazione.
- **CBCentralManager** - il `RetrievePeripherals` e `RetrieveConnectedPeripherals` metodi del `CBCentralManager` classe sono state rimosse in iOS 9. Chiamata di questi metodi causerà un'app per arresto anomalo del sistema quando si associa un accessorio o all'avvio dell'app.
- **FetchAllChanges** - il `FetchAllChanges` del `CKFetchRecordChangesOperation` classe è stato rimosso e verranno rimossi in iOS 9.
- **Media Player** -framework The Media Player è stata deprecata in iOS 9. Usare invece AVKit o AV Foundation API.

Per un elenco completo di deprecati API specifiche, vedere di Apple [iOS 9.0 API diff](https://developer.apple.com/library/prerelease/ios/releasenotes/General/iOS90APIDiffs/index.html#//apple_ref/doc/uid/TP40016222) documentazione.

## <a name="ios-9-sample-apps"></a>App di esempio iOS 9

Alcune [esempi di iOS 9 specifiche](https://developer.xamarin.com/samples/ios/iOS9/) per iniziare:

- [AstroLayout](https://github.com/xamarin/monotouch-samples/tree/master/ios9/AstroLayout)
- [CollectionView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/CollectionView)
- [MetalPerformanceShadersHelloWorld](https://developer.xamarin.com/samples/monotouch/ios9/MetalPerformanceShadersHelloWorld/)
- [MusicMotion](https://developer.xamarin.com/samples/monotouch/ios9/MusicMotion/)
- [PhotoProgress](https://developer.xamarin.com/samples/monotouch/ios9/PhotoProgress/)
- [SegueCatalog](https://developer.xamarin.com/samples/monotouch/ios9/SegueCatalog/)
- [StackView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StackView)
- [StickyCorners](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StickyCorners)

Vedere anche le parti di iOS di questi esempi (complementare Mac OS X versioni presto!):

- [AgentsCatalog](https://github.com/xamarin/mac-ios-samples/tree/master/AgentsCatalog)
- [MetalKitEssentials](https://github.com/xamarin/mac-ios-samples/tree/master/MetalKitEssentials)



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Introduzione a 3D Touch](~/ios/platform/3d-touch.md)
- [ATS (App Transport Security)](~/ios/app-fundamentals/ats.md)
- [Multitasking per iPad](~/ios/platform/multitasking.md)
- [Contatti e interfaccia utente](~/ios/platform/contacts.md)
- [Nuove API di ricerca](~/ios/platform/search/index.md)
- [Introduzione alla visualizzazione Stack](~/ios/user-interface/controls/uistackview.md)
- [Modifiche alla vista raccolta](~/ios/user-interface/controls/uicollectionview.md)
- [Miglioramenti della modalità di gioco](~/ios/platform/gaming/index.md)
- [Introduzione a HomeKit](~/ios/platform/homekit.md)
- [Introduzione a Handoff](~/ios/platform/handoff.md)
- [Modifiche aggiuntive del framework iOS 9](~/ios/platform/introduction-to-ios9/additional-framework-changes.md)
- [Risoluzione dei problemi](~/ios/platform/introduction-to-ios9/troubleshooting.md)
- [per gli sviluppatori iOS 9](https://developer.apple.com/ios/pre-release/)
- [What ' s New in iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [L'aggiornamento delle App xamarin. IOS per iOS9 (video)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
