---
title: Introduzione a iOS 9
description: "Questo articolo descrive tutte le nuove e modificate le API e le funzionalità disponibili in iOS 9 per gli sviluppatori di xamarin. IOS."
ms.topic: article
ms.prod: xamarin
ms.assetid: 4D71BBD9-B948-4B59-9AF5-F199C51CBEB3
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: 5b26989603695cfb309fba5a5318f7ef4d2460e2
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="introduction-to-ios-9"></a>Introduzione a iOS 9

_Questo articolo descrive tutte le nuove e modificate le API e le funzionalità disponibili in iOS 9 per gli sviluppatori di xamarin. IOS._

![](images/ios9-sml.png "Il logo di iOS 9")

Apple iOS 9 e molti miglioramenti alle funzionalità esistenti è aggiunto diversi servizi e le API di nuovo.

## <a name="3d-touch"></a>3D Touch

Nuovi utenti di iOS 9 e iPhone 6s e iPhone 6s Plus, 3D Touch aggiunge i movimenti di pressione sensibili per le app iOS. 3D Touch un'app iPhone è ora possibile stabilire che l'utente tocca dello schermo del dispositivo, non solo può inoltre rilevare quanto pressione è esercitando l'utente e rispondere ai livelli diversi di pressione.

3D Touch offre le seguenti funzionalità all'App:

- **Utilizzo di sensibilità** - App a questo punto è possono misurare la difficoltà o chiaro l'utente tocca il vantaggio dello schermo e intraprendere delle informazioni. Ad esempio, un'applicazione di disegno può rendere una linea spessa o sottili in base a come disco rigido l'utente tocca lo schermo.
- **Visualizza e Pop** -l'app può ora consentire all'utente di interagire con i relativi dati senza dover passare fuori il contesto corrente. Premendo rigido sullo schermo, questi possono *Visualizza* nell'elemento di cui sono interessati (ad esempio la visualizzazione in anteprima un messaggio). Premendo più difficile, questi possono *Pop* nell'elemento.
- **Azioni rapide** -pensare di azioni rapide come menu di scelta rapida che può essere estratto verticale quando l'utente fa clic su un elemento in un'applicazione desktop. Con azioni rapide, è possibile aggiungere più comuni, semplice e rapido ai collegamenti di accesso alle funzioni nell'app dall'icona schermata Home nel dispositivo iOS.

Per ulteriori dettagli, vedere il nostro [introduzione 3D Touch](~/ios/platform/3d-touch.md) Guida.

## <a name="app-transport-security"></a>Sicurezza del trasporto di App

Nuovo a iOS 9, sicurezza trasporto App (AT) applica connessioni protette tra le risorse internet (ad esempio i server back-end dell'app) e l'app. AT garantisce che tutte le comunicazioni internet siano conformi per le procedure consigliate, connessione sicura impedendo in tal modo la diffusione accidentale di informazioni riservate direttamente tramite l'app o una libreria che sta utilizzando.

Poiché AT è attivata per impostazione predefinita nelle App per iOS 9 e OS X 10.11 (montagna di El Capitan), tutte le connessioni utilizzando [NSUrlConnection](https://developer.xamarin.com/api/type/Foundation.NSUrlConnection/), [CFUrl](https://developer.xamarin.com/api/type/CoreFoundation.CFUrl/) o [NSUrlSession](https://developer.xamarin.com/api/type/Foundation.NSUrlSession/) sarà soggetto a Requisiti di sicurezza AT. Se le connessioni non soddisfano questi requisiti, non riuscirà con un'eccezione.

Per ulteriori informazioni su AT, vedere il nostro [la sicurezza del trasporto App](~/ios/app-fundamentals/ats.md) Guida.

<a name="multitasking" />

## <a name="multitasking-for-ipad"></a>Multitasking per iPad

Con iOS 9, Apple ha aggiunto il supporto di multitasking per le due applicazioni in esecuzione nello stesso momento su iPad specifiche hardware. Di conseguenza, le app xamarin non è più possano presupporre che sono l'unico app in esecuzione in qualsiasi momento o che hanno accesso alle risorse del dispositivo o schermo intero.

Il multitasking per iPad è supportato tramite le funzionalità seguenti:

- **Diapositiva su** -consente all'utente eseguire temporaneamente un'app iOS di secondo in una diapositiva out pannello (sia sul lato destro o sinistro dello schermo in base alla direzione language) che copre circa il 25% dell'app principale attualmente in esecuzione. Sposta su è disponibile solo in un iPad Pro, iPad Air, iPad 2 aria, iPad 2 Mini, iPad Mini 3 o 4 Mini iPad.
- **Doppia visualizzazione** -sull'hardware supportato iPad (iPad aria 2, 4 Mini iPad e iPad Pro solo), l'utente può selezionare un'app secondo ed eseguito side-by-side con l'app in esecuzione in modalità a schermo intero una divisione. L'utente può controllare la percentuale della schermata principale che occupa ogni app.
- **Immagine in immagine** : per App di riprodurre contenuto video, il video può ora essere riprodotto in una finestra mobile e ridimensionabile mobile tramite le altre applicazioni in esecuzione nel dispositivo iOS. L'utente abbia il pieno controllo le dimensioni e posizione di questa finestra. Immagine nell'immagine è disponibile solo in un iPad Pro, iPad Air, iPad 2 aria, iPad 2 Mini, iPad Mini 3 o 4 Mini iPad.

Per ulteriori dettagli sulle nuove funzionalità multitasking di iOS 9, vedere il nostro [Multitasking per iPad](~/ios/platform/multitasking.md) Guida.

## <a name="new-contacts-and-contacts-ui-frameworks"></a>Nuovi contatti e i framework dell'interfaccia utente di contatti

Con l'introduzione di iOS 9, Apple ha rilasciato due nuovi Framework, [contatti](https://developer.xamarin.com/api/namespace/Contacts/) e [ContactsUI](https://developer.xamarin.com/api/namespace/ContactsUI/), che sostituisce la Rubrica esistente e Framework dell'interfaccia utente della Rubrica indirizzi usati da iOS 8 e versioni precedenti.

Questi Framework nuovo orientato a predisporre quanto segue:

- **Contatti** – xamarin fornisce accesso alle informazioni di contatto dell'utente. Poiché la maggior parte delle applicazioni richiedono solo accesso in sola lettura, questo framework è stato ottimizzato per l'accesso thread-safe di sola lettura.
- **ContactsUI** – fornisce xamarin elementi dell'interfaccia utente da visualizzare, modificare, selezionare e creare contatti nei dispositivi iOS.

Per ulteriori informazioni, vedere il nostro [contatti e l'interfaccia utente di contatti](~/ios/platform/contacts.md) documentazione.


## <a name="new-search-apis"></a>Nuove API di ricerca

Ricerca è stata espansa in iOS 9 per offrire grandi nuovi modi per accedere alle informazioni all'interno dell'app xamarin. IOS. Utilizzando le nuove API di ricerca, è possibile rendere contenuto dell'app disponibile per la ricerca tramite Spotlight e Safari risultati della ricerca, uniforme e promemoria Siri e suggerimenti. In questo modo l'accesso rapido alle attività e informazioni complete all'interno dell'app.

Inoltre, le nuove API di ricerca semplificano l'integrazione di ricerca nell'app senza un'esperienza di implementazione di ricerca precedente. Per questo motivo, le attestazioni Apple richiede in genere alcune ore per rendere il contenuto di un'app di iOS 9 universalmente ricercabili mediante ricerca di App.

Per ulteriori informazioni, vedere il nostro [miglioramenti della ricerca](~/ios/platform/search/index.md) documentazione.

## <a name="new-stack-view"></a>Nuova visualizzazione Stack

Controllo di visualizzazione dello Stack ([UIStackView](https://developer.xamarin.com/api/type/UIKit.UIStackView/)) si avvale di Layout automatico e le classi di dimensioni per gestire una pila di sottoviste (orizzontalmente o verticalmente) che risponde in modo dinamico alle dimensioni di schermata e orientamento del dispositivo iOS.

Usando il controllo di visualizzazione dello Stack, la quantità di lavoro richiesto al layout di che un'interfaccia utente è notevolmente ridotte. Il layout di tutti i sottoviste associata a una visualizzazione dello Stack vengono gestiti automaticamente in base alle proprietà di sviluppatore definiti, ad esempio asse, distribuzione, l'allineamento e spaziatura.

Per ulteriori informazioni, vedere il nostro [Introduzione alla visualizzazione Stack](~/ios/user-interface/controls/uistackview.md) documentazione.


## <a name="collection-view-changes"></a>Visualizzazione di raccolta

In iOS 9, la visualizzazione della raccolta ([UICollectionView](https://developer.xamarin.com/api/type/UIKit.UICollectionView/)) trascina ora supporta riordinamento degli elementi predefinita aggiungendo un nuovo riconoscitore di movimento predefinito e nuovi metodi di supporto.

Usa questi nuovi metodi, è facilmente possibile implementare per trascinare-Riordina nella visualizzazione raccolta e hanno la possibilità di personalizzazione dell'aspetto di elementi in qualsiasi fase del processo di riordinamento.

Per ulteriori informazioni sulle modifiche di visualizzazione di raccolta per iOS 9, vedere il nostro [raccolta Visualizza modifiche](~/ios/user-interface/controls/uicollectionview.md) Guida.

## <a name="game-enhancements"></a>Miglioramenti di gioco

Con iOS 9, Apple ha diversi miglioramenti tecnologici alle API di gioco che rendono più semplice implementare grafica di giochi e audio nell'app xamarin. IOS. Questi includono entrambi semplicità di sviluppo tramite il framework di alto livello e sfruttare le potenzialità di GPU del dispositivo iOS per una migliore velocità e le capacità di grafico con i miglioramenti di basso livello.

Insieme a nuove funzionalità migliorate di metallo, SceneKit e SpriteKit inclusi GameplayKit, ReplayKit, i/o del modello, MetalKit e Metal prestazioni shader.

Per ulteriori informazioni, vedere il nostro [miglioramenti di gioco](~/ios/platform/gaming/index.md) documentazione.

## <a name="homekit-framework-changes"></a>Modifiche di Framework HomeKit

Il [HomeKit](https://developer.xamarin.com/api/namespace/HomeKit/) framework, introdotto in iOS 8, offre la possibilità di configurare e controllare vari accessori HomeKit abilitato (ad esempio luci automatizzate, serrature e ciò porta garage) da un'app xamarin. Oltre a essere facile installazione e configurazione, accessori HomeKit possono essere controllati tramite i comandi vocali Siri.

In iOS 9, Apple ha semplificato il programma di installazione, espansi i tipi di accessori supportati e altre interazioni degli accessori (ad esempio, il controllo di un accessorio in modalità remota tramite iCloud) fornito.

Per ulteriori informazioni, vedere il nostro [Introduzione a HomeKit](~/ios/platform/homekit.md), [HomeKitIntro iOS App di esempio](https://developer.xamarin.com/samples/monotouch/HomeKit/HomeKitIntro/) e Apple [HomeKit](https://developer.apple.com/homekit/) documentazione.

## <a name="handoff-framework-changes"></a>Modifiche di Framework handoff

Handoff (noto anche come continuità) è stato introdotto da Apple in iOS 8 e OS X Yosemite (10.10) in modo all'utente di avviare un'attività su uno dei propri dispositivi (iOS o Mac) e continuare a quella stessa attività in un altro dei propri dispositivi (come identificato dalla iClou dell'utente p Account).

Handoff espanso in iOS 9 per supportano anche nuove funzionalità di ricerca avanzate. Per ulteriori informazioni, vedere il nostro [miglioramenti della ricerca](~/ios/platform/search/index.md) documentazione. Per ulteriori informazioni sull'utilizzo di continuità, consultare il nostro [Introduzione a Handoff](~/ios/platform/handoff.md) documentazione.

## <a name="new-extension-points"></a>Nuovi punti di estensione

In iOS 8, Apple ha introdotto le estensioni, librerie presentate dal sistema operativo in contesti standard, ad esempio all'interno del centro di notifica, quando l'utente richiede una tastiera o quando si modifica una foto.

Con iOS 9, Apple si estende a supporto delle estensioni fornendo diversi nuovi _punti di estensione_ che definiscono i criteri di utilizzo e forniscono le API per l'utilizzo all'interno di una determinata area, come indicato di seguito:

- **Nuovo punto di estensione unità Audio** : utilizzo del punto di estensione per fornire gli effetti audio musicali, audio, generatori e così via. da utilizzare all'interno di altre applicazioni host unità Audio (ad esempio GarageBand). Questo punto di estensione consente inoltre di vendere _unità Audio_ (plug-in) dell'App Store.
- **Nuovo punto di estensione di manutenzione dell'indice** : utilizzo del punto di estensione per supportare la reindicizzazione di dati dell'applicazione senza che ne consegue un app.
- **Nuovi punti di estensione di rete** (che richiedono autorizzazioni speciali di Apple):
    - **Estensione del Provider Proxy app** : utilizzo del punto di estensione per implementare un proxy di rete sul lato client trasparente personalizzata.
    - **Filtrare i Provider di dati o estensione del Provider controllo filtro** -utilizzare questi punti di estensione per implementare il contenuto di rete dinamici filtro sul dispositivo.
    - **Estensione del Provider di pacchetti Tunnel** : utilizzo del punto di estensione per implementare una protocolli client-side di tunneling VPN personalizzata.
- **Nuovi punti di estensione Safari**:
    - **Estensione per il blocco di contenuto** : utilizzo del punto di estensione per definire un elenco di contenuti bloccati che non verrà visualizzato quando l'utente, è esplorare il web.
    - **Condiviso collegamenti estensione** : utilizzo del punto di estensione per abilitare la visualizzazione del contenuto dell'applicazione nei collegamenti condiviso di Safari.

Per ulteriori informazioni, vedere il nostro [Introduzione alle estensioni](~/ios/platform/extensions.md) e Apple [Guida per programmatori estensione App](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214) documentazione.

## <a name="keychain-enhancements"></a>Miglioramenti di portachiavi

In iOS 9, Apple ha migliorato il portachiavi per fornire un nuovo tipo di chiave di crittografia per il Enclave Secure e altre opzioni di protezione dati di elemento, come indicato di seguito:

- Un nuovo vincolo di ID tocco per invalidare portachiavi elementi quando viene modificato il database di impronta digitale.
- Nuovi vincoli che consentono la creazione di voci dell'elenco di controllo di accesso con l'ID tocco o Passcode solo.
- Un nuovo contesto di autenticazione che consente di richiamare l'autenticazione separato dal `SecItem` chiamate.
- Accedere a entropia controllo elenco (utilizzando l'opzione Password applicazione) per la crittografia elemento keychain fornita dall'app.
- Supporto per la generazione e l'uso di chiavi all'interno di enclave protetto (tramite il `kSecAttrTokenIDSecureEnclave` attributo).

Per ulteriori informazioni, vedere il nostro [introduzione Touch ID](~/ios/platform/touchid.md) documentazione.


## <a name="right-to-left-language-support"></a>Supporto delle lingue da destra a sinistra

In iOS 9, Apple ha apportato la presentazione di un'interfaccia utente capovolta più semplice che mai, fornendo il supporto completo per le lingue da destra a sinistra. Il comportamento predefinito include quanto segue:

- Standard [UIKit](https://developer.xamarin.com/api/namespace/UIKit/) controlli verranno automaticamente capovolgere destra a sinistra in base alle impostazioni di lingua e i dispositivi di iOS.
- Il [UIView](https://developer.xamarin.com/api/type/UIKit.UIView/) classe fornisce gli attributi che consentono di definire come devono essere visualizzate quando una determinata visualizzazione capovolto da destra a sinistra.
- La possibilità di capovolge un'immagine a livello di codice utilizzando il [FlipsForRightToLeftLayoutDirection](https://developer.xamarin.com/api/property/UIKit.UIImage.FlipsForRightToLeftLayoutDirection/) proprietà del [UIImage](https://developer.xamarin.com/api/type/UIKit.UIImage/) classe.

Per ulteriori informazioni, vedere Apple [supporto destra-sinistra](https://developer.apple.com/library/prerelease/ios/documentation/MacOSX/Conceptual/BPInternational/SupportingRight-To-LeftLanguages/SupportingRight-To-LeftLanguages.html#//apple_ref/doc/uid/10000171i-CH17) documentazione.



## <a name="additional-framework-changes"></a>Modifiche di .NET Framework

Oltre alle modifiche principali che sono stati illustrati in precedenza, Apple ha apportate le modifiche e miglioramenti per diversi Framework esistenti per iOS 9, inclusi i seguenti:

- Framework AV Foundation
- Framework AVKit
- CloudKit Framework
- Framework di base
- Framework handoff
- Framework HealthKit
- HomeKit Framework
- Framework di autenticazione locale
- Framework MapKit
- PassKit Framework
- Framework servizi Safari
- Framework UIKit

Per ulteriori informazioni, vedere il nostro [modifiche Framework aggiuntive iOS 9](~/ios/platform/introduction-to-ios9/additional-framework-changes.md) documentazione.

## <a name="deprecated-apis-and-functions"></a>Funzioni e API deprecate

Apple è deprecato funzioni in iOS 9 e le API seguenti:

- **Indirizzo libro & dell'interfaccia utente della Rubrica indirizzi** -queste API sono state sostituite da Framework di contatto e l'interfaccia utente di contatto. Per ulteriori informazioni, vedere il nostro [contatti e l'interfaccia utente di contatti](~/ios/platform/contacts.md) documentazione.
- **CBCentralManager** - `RetrievePeripherals` e `RetrieveConnectedPeripherals` metodi la `CBCentralManager` classe sono state rimosse in iOS 9. Chiamata di questi metodi causerà un'app di arresto anomalo del sistema quando l'associazione di un accessorio o app viene avviata.
- **FetchAllChanges** - `FetchAllChanges` del `CKFetchRecordChangesOperation` classe è stato rimosso e verrà rimossa in iOS 9.
- **Media Player** -la Media Player framework è stato deprecato in iOS 9. Utilizzare invece AVKit o AV Foundation API.

Per un elenco completo di deprecations API specifiche, vedere Apple [iOS 9.0 API differenze](https://developer.apple.com/library/prerelease/ios/releasenotes/General/iOS90APIDiffs/index.html#//apple_ref/doc/uid/TP40016222) documentazione.

## <a name="ios-9-sample-apps"></a>App di esempio di iOS 9

Alcuni [esempi di iOS 9 specifiche](https://developer.xamarin.com/samples/ios/iOS9/) per iniziare:

- [AstroLayout](https://github.com/xamarin/monotouch-samples/tree/master/ios9/AstroLayout)
- [Visualizzazione di raccolta](https://github.com/xamarin/monotouch-samples/tree/master/ios9/CollectionView)
- [MetalPerformanceShadersHelloWorld](https://developer.xamarin.com/samples/monotouch/ios9/MetalPerformanceShadersHelloWorld/)
- [MusicMotion](https://developer.xamarin.com/samples/monotouch/ios9/MusicMotion/)
- [PhotoProgress](https://developer.xamarin.com/samples/monotouch/ios9/PhotoProgress/)
- [SegueCatalog](https://developer.xamarin.com/samples/monotouch/ios9/SegueCatalog/)
- [StackView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StackView)
- [StickyCorners](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StickyCorners)

Vedere anche le parti iOS di questi esempi (complementare Mac OS X versioni presto!):

- [AgentsCatalog](https://github.com/xamarin/mac-ios-samples/tree/master/AgentsCatalog)
- [MetalKitEssentials](https://github.com/xamarin/mac-ios-samples/tree/master/MetalKitEssentials)



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://developer.xamarin.com/samples/ios/iOS9/)
- [Introduzione a 3D Touch](~/ios/platform/3d-touch.md)
- [ATS (App Transport Security)](~/ios/app-fundamentals/ats.md)
- [Multitasking per iPad](~/ios/platform/multitasking.md)
- [Contatti e dell'interfaccia utente](~/ios/platform/contacts.md)
- [Nuove API di ricerca](~/ios/platform/search/index.md)
- [Introduzione alla visualizzazione Stack](~/ios/user-interface/controls/uistackview.md)
- [Visualizzazione di raccolta](~/ios/user-interface/controls/uicollectionview.md)
- [Miglioramenti di gioco](~/ios/platform/gaming/index.md)
- [Introduzione a HomeKit](~/ios/platform/homekit.md)
- [Introduzione a Handoff](~/ios/platform/handoff.md)
- [Modifiche aggiuntive del framework iOS 9](~/ios/platform/introduction-to-ios9/additional-framework-changes.md)
- [Risoluzione dei problemi](~/ios/platform/introduction-to-ios9/troubleshooting.md)
- [iOS 9 per gli sviluppatori](https://developer.apple.com/ios/pre-release/)
- [Novità in iOS 9.0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Aggiornare le app xamarin a iOS9 (video)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
