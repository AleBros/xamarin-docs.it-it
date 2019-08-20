---
title: Introduzione a iOS 9
description: Questo articolo presenta tutte le API e le funzionalità nuove e modificate disponibili in iOS 9 per gli sviluppatori Novell. iOS.
ms.prod: xamarin
ms.assetid: 4D71BBD9-B948-4B59-9AF5-F199C51CBEB3
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/19/2017
ms.openlocfilehash: 69a599b6a7534cb77dd9e023d937f10a6b0523fb
ms.sourcegitcommit: 0df727caf941f1fa0aca680ec871bfe7a9089e7c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69620664"
---
# <a name="introduction-to-ios-9"></a>Introduzione a iOS 9

_Questo articolo presenta tutte le API e le funzionalità nuove e modificate disponibili in iOS 9 per gli sviluppatori Novell. iOS._

![](images/ios9-sml.png "Logo iOS 9")

Apple ha aggiunto diverse nuove API e servizi in iOS 9 insieme a numerosi miglioramenti apportati alle funzionalità esistenti.

## <a name="3d-touch"></a>3D Touch

Una novità di iOS 9 e di iPhone 6s e iPhone 6s Plus, 3D touch aggiunge movimenti sensibili alla pressione alle app iOS. Con il tocco 3D, un'app iPhone è ora in grado di non solo indicare che l'utente sta toccando lo schermo del dispositivo, ma può anche rilevare la quantità di pressione che l'utente sta impiegando e rispondere ai diversi livelli di pressione.

3D touch offre all'app le funzionalità seguenti:

- **Sensibilità alla pressione** : le app possono ora misurare il livello di rigidezza o di luminosità dell'utente che tocca lo schermo e sfruttare tali informazioni. Ad esempio, un'app di disegno può rendere una linea più spessa o più sottile a seconda della difficoltà con cui l'utente tocca lo schermo.
- **Peek e pop** : l'app può ora consentire all'utente di interagire con i dati senza dover uscire dal contesto corrente. Premendo hard sullo schermo, è possibile visualizzare l' elemento a cui si è interessati, ad esempio l'anteprima di un messaggio. Premendo più difficile, è possibile visualizzare l'elemento.
- **Azioni rapide** : si pensi a azioni rapide come i menu contestuali che possono essere visualizzati quando un utente fa clic con il pulsante destro del mouse su un elemento in un'app desktop. Con azioni rapide, è possibile aggiungere collegamenti comuni, rapidi e facili da accedere alle funzioni nell'app dall'icona della schermata iniziale sul dispositivo iOS.

Per ulteriori informazioni, vedere la Guida [introduttiva a 3D Touch](~/ios/platform/3d-touch.md) .

## <a name="app-transport-security"></a>ATS (App Transport Security)

Una novità di iOS 9, la sicurezza del trasporto app (ATS) impone connessioni sicure tra le risorse Internet, ad esempio il server back-end dell'app, e l'app. ATS garantisce che tutte le comunicazioni Internet siano conformi alle procedure consigliate per la connessione sicura, impedendo in tal modo la divulgazione accidentale di informazioni riservate direttamente tramite l'app o una libreria che sta utilizzando.

Poiché ATS è abilitato per impostazione predefinita nelle app compilate per iOS 9 e OS X 10,11 (El Capitan), tutte le connessioni che usano [NSUrlConnection](xref:Foundation.NSUrlConnection), [CFUrl](xref:CoreFoundation.CFUrl) o [NSUrlSession](xref:Foundation.NSUrlSession) saranno soggette ai requisiti di sicurezza ATS. Se le connessioni non soddisfano questi requisiti, avranno esito negativo con un'eccezione.

Per ulteriori informazioni su ATS, vedere la guida alla [sicurezza del trasporto delle app](~/ios/app-fundamentals/ats.md) .

<a name="multitasking" />

## <a name="multitasking-for-ipad"></a>Multitasking per iPad

Con iOS 9, Apple ha aggiunto il supporto multitasking per l'esecuzione di due app allo stesso tempo su hardware iPad specifico. Di conseguenza, le app Novell. iOS non possono più presumere che siano l'unica app in esecuzione in un determinato momento o che abbiano accesso allo schermo intero o alle risorse del dispositivo.

Il multitasking per iPad è supportato tramite le funzionalità seguenti:

- **Scorri su** -consente all'utente di eseguire temporaneamente una seconda app iOS in un pannello scorrevole (sul lato destro o sinistro dello schermo in base alla direzione della lingua) che copre circa il 25% dell'app principale attualmente in esecuzione. La funzionalità di scorrimento è disponibile solo in un iPad Pro, iPad Air, iPad Air 2, iPad mini 2, iPad mini 3 o iPad Mini 4.
- **Split View** : su hardware iPad supportato (solo iPad Air 2, iPad Mini 4 e iPad Pro), l'utente può selezionare una seconda app ed eseguirla side-by-side con l'app attualmente in esecuzione in modalità split screen. L'utente può controllare la percentuale della schermata principale occupata da ogni app.
- **Immagine nell'immagine** : per le app che eseguono la riproduzione di contenuto video, il video può essere ora riprodotto in una finestra spostabile e ridimensionabile che è in grado di spostarsi sulle altre app attualmente in esecuzione nel dispositivo iOS. L'utente ha il controllo completo sulle dimensioni e la posizione di questa finestra. Immagine in immagine è disponibile solo in un iPad Pro, iPad Air, iPad Air 2, iPad mini 2, iPad mini 3 o iPad Mini 4.

Per altre informazioni sulle nuove funzionalità multitasking di iOS 9, vedere la guida per il multitasking [per iPad](~/ios/platform/multitasking.md) .

## <a name="new-contacts-and-contacts-ui-frameworks"></a>Nuovi framework dell'interfaccia utente contatti e contatti

Con l'introduzione di iOS 9, Apple ha rilasciato due nuovi Framework, [contatti](xref:Contacts) e [ContactsUI](xref:ContactsUI), che sostituiscono i Framework dell'interfaccia utente della Rubrica e della rubrica esistente usati da iOS 8 e versioni precedenti.

Questi nuovi Framework orientati a oggetti forniscono gli elementi seguenti:

- **Contatti** : fornisce l'accesso a Novell. iOS alle informazioni di contatto dell'utente. Poiché la maggior parte delle app richiede solo l'accesso in sola lettura, questo Framework è stato ottimizzato per l'accesso thread-safe di sola lettura.
- **ContactsUI** : fornisce gli elementi dell'interfaccia utente di Novell. iOS per visualizzare, modificare, selezionare e creare contatti nei dispositivi iOS.

Per ulteriori informazioni, vedere la documentazione [dell'interfaccia utente contatti e contatti](~/ios/platform/contacts.md) .


## <a name="new-search-apis"></a>Nuove API di ricerca

La ricerca è stata espansa in iOS 9 per offrire nuovi modi per accedere alle informazioni all'interno dell'app Novell. iOS. Usando le nuove API di ricerca, è possibile eseguire ricerche nel contenuto dell'app tramite i risultati della ricerca di Spotlight e Safari, i promemoria e i suggerimenti per la consegna e la Siri. Questo consente agli utenti di accedere rapidamente alle attività e alle informazioni in modo approfondito nell'app.

Inoltre, le nuove API di ricerca facilitano l'integrazione della ricerca nell'app senza l'esperienza di implementazione della ricerca precedente. Per questo motivo, Apple dichiara che richiede in genere alcune ore per rendere il contenuto di un'app iOS 9 universalmente ricercabile usando la ricerca di app.

Per ulteriori informazioni, vedere la documentazione relativa ai miglioramenti per la [ricerca](~/ios/platform/search/index.md) .

## <a name="new-stack-view"></a>Nuova visualizzazione stack

Il controllo di visualizzazione dello stack ([UIStackView](xref:UIKit.UIStackView) sfrutta la potenza del layout automatico e delle classi di dimensioni per gestire uno stack di visualizzazioni (orizzontalmente o verticalmente) che risponde dinamicamente all'orientamento e alle dimensioni dello schermo del dispositivo iOS.

Con il controllo di visualizzazione stack, la quantità di lavoro necessaria per il layout di un'interfaccia utente è notevolmente ridotta. Il layout di tutte le sottoviste associate a una visualizzazione stack viene gestito automaticamente in base alle proprietà definite dallo sviluppatore, ad esempio asse, distribuzione, allineamento e spaziatura.

Per ulteriori informazioni, vedere la documentazione [introduttiva di visualizzazione stack](~/ios/user-interface/controls/uistackview.md) .


## <a name="collection-view-changes"></a>Modifiche della visualizzazione raccolta

In iOS 9, la visualizzazione della raccolta ([UICollectionView](xref:UIKit.UICollectionView) supporta ora il trascinamento del riordinamento degli elementi predefiniti aggiungendo un nuovo riconoscimento di movimento predefinito e diversi nuovi metodi di supporto.

Usando questi nuovi metodi, è possibile implementare facilmente la funzione di trascinamento nella visualizzazione della raccolta e scegliere di personalizzare l'aspetto degli elementi durante qualsiasi fase del processo di riordinamento.

Per ulteriori informazioni sulle modifiche apportate alla visualizzazione della raccolta per iOS 9, vedere la Guida alle [modifiche della visualizzazione raccolta](~/ios/user-interface/controls/uicollectionview.md) .

## <a name="game-enhancements"></a>Miglioramenti del gioco

Con iOS 9, Apple ha apportato diversi miglioramenti tecnologici alle API per i giochi che semplificano l'implementazione di grafica e audio dei giochi nell'app Novell. iOS. Sono inclusi sia la facilità di sviluppo che i Framework di alto livello, sfruttando la potenza della GPU del dispositivo iOS per migliorare la velocità e le funzionalità grafiche con miglioramenti di basso livello.

Sono inclusi GameplayKit, ReplayKit, Model I/O, MetalKit e Metal Performance shader insieme alle nuove funzionalità migliorate di Metal, SceneKit e SpriteKit.

Per ulteriori informazioni, vedere la documentazione relativa ai miglioramenti apportati ai [giochi](~/ios/platform/gaming/index.md) .

## <a name="homekit-framework-changes"></a>Modifiche al Framework di HomeKit

Il Framework [HomeKit](xref:HomeKit) , introdotto in iOS 8, offre la possibilità di configurare e controllare vari accessori abilitati per HomeKit (ad esempio, luci automatiche, blocchi di porte e Open Door garage) da un'app Novell. iOS. Oltre a semplificare l'installazione e la configurazione, è possibile controllare gli accessori di HomeKit tramite comandi vocali Siri.

In iOS 9, Apple ha semplificato la configurazione, ha ampliato i tipi di accessori supportati e ha fornito più interazioni accessorie, ad esempio il controllo di un accessorio in modalità remota tramite iCloud.

Per altre informazioni, vedere l' [Introduzione all'app di esempio HomeKit](~/ios/platform/homekit.md), [HomeKitIntro iOS](https://docs.microsoft.com/samples/xamarin/ios-samples/homekit-homekitintro) e la documentazione di Apple [HomeKit](https://developer.apple.com/homekit/) .

## <a name="handoff-framework-changes"></a>Modifiche del Framework uniforme

La consegna, nota anche come continuità, è stata introdotta da Apple in iOS 8 e OS X Yosemite (10,10) come metodo per l'avvio di un'attività su uno dei dispositivi (iOS o Mac) e continuare la stessa attività in un altro dispositivo (come identificato dal iClou dell'utente Account d).

La consegna è stata espansa in iOS 9 per supportare anche nuove funzionalità di ricerca migliorate. Per ulteriori informazioni, vedere la documentazione relativa ai miglioramenti per la [ricerca](~/ios/platform/search/index.md) . Per altre informazioni sull'uso della consegna, vedere l' [Introduzione alla](~/ios/platform/handoff.md) documentazione.

## <a name="new-extension-points"></a>Nuovi punti di estensione

In iOS 8 Apple ha introdotto le estensioni, ovvero le librerie presentate dal sistema operativo nei contesti standard, ad esempio all'interno del centro notifiche, quando l'utente richiede una tastiera o quando modifica una foto.

Con iOS 9, Apple sta estendendo il supporto dell'estensione fornendo diversi nuovi _punti di estensione_ che definiscono i criteri di utilizzo e forniscono le API per lavorare all'interno di una determinata area, come indicato di seguito:

- **Nuovo punto di estensione unità audio** : usare questo punto di estensione per fornire effetti audio, strumenti musicali, generatori di suoni e così via, da usare in altre app host di unità audio (ad esempio, GarageBand). Questo punto di estensione consente anche di vendere _unità audio_ (plug-in audio) nell'App Store.
- **Nuovo punto di estensione** per la manutenzione degli indici: usare questo punto di estensione per supportare la reindicizzazione dei dati dell'app senza richiedere il riavvio dell'app.
- **Nuovi punti di estensione di rete** (è necessario disporre di un'autorizzazione speciale da Apple):
  - **Estensione del provider del proxy applicazione** : usare questo punto di estensione per implementare un proxy di rete trasparente sul lato client personalizzato.
  - **Filtrare l'estensione del provider del controllo provider di dati/filtro** : usare questi punti di estensione per implementare il filtro del contenuto della rete dinamica sul dispositivo.
  - **Estensione del provider** di tunneling di pacchetti: usare questo punto di estensione per implementare un protocollo di tunneling VPN personalizzato sul lato client.
- **Nuovi punti di estensione Safari**:
  - **Estensione** per il blocco del contenuto: usare questo punto di estensione per definire un elenco di contenuto bloccato che non verrà visualizzato quando l'utente sta esplorando il Web.
  - **Estensione per i collegamenti condivisi** : usare questo punto di estensione per abilitare la visualizzazione del contenuto dell'app nei collegamenti condivisi di Safari.

Per altre informazioni, vedere la documentazione relativa [all'introduzione alle estensioni e alla guida alla](~/ios/platform/extensions.md) programmazione delle estensioni per le [app](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/ExtensibilityPG/index.html#//apple_ref/doc/uid/TP40014214) di Apple.

## <a name="keychain-enhancements"></a>Miglioramenti del keychain

In iOS 9, Apple ha migliorato il keychain per fornire un nuovo tipo di chiave di crittografia per l'enclave sicura e altre opzioni di protezione degli elementi, come indicato di seguito:

- Nuovo vincolo Touch ID che invalida gli elementi Keychain quando viene modificato il database delle impronte digitali.
- Nuovi vincoli che consentono la creazione di voci dell'elenco di controllo di accesso con ID tocco o solo codice.
- Nuovo contesto di autenticazione che consente di richiamare l'autenticazione separatamente dalle `SecItem` chiamate.
- Entropia dell'elenco di controllo di accesso (usando l'opzione password applicazione) per la crittografia dell'elemento Keychain fornita dall'app.
- Supporto per la generazione e l'uso di chiavi all'interno dell'enclave protetta (tramite l' `kSecAttrTokenIDSecureEnclave` attributo).

Per ulteriori informazioni, vedere la documentazione [introduttiva a Touch ID](~/ios/platform/touchid.md) .


## <a name="right-to-left-language-support"></a>Supporto per le lingue da destra a sinistra

In iOS 9, Apple ha reso più semplice che mai l'interfaccia utente capovolta offrendo supporto completo per le lingue da destra a sinistra. Sono inclusi gli elementi seguenti:

- I controlli [UIKit](xref:UIKit) standard verranno automaticamente invertiti da destra a sinistra in base alle impostazioni locali e della lingua dei dispositivi iOS.
- La classe [UIView](xref:UIKit.UIView) fornisce attributi che consentono di definire la modalità di visualizzazione di una determinata visualizzazione quando viene capovolta da destra a sinistra.
- Possibilità di capovolgere un'immagine a livello di codice usando la proprietà [FlipsForRightToLeftLayoutDirection](xref:UIKit.UIImage.FlipsForRightToLeftLayoutDirection) della classe [UIImage](xref:UIKit.UIImage) .

Per ulteriori informazioni, vedere la documentazione di [supporto per le lingue da destra a sinistra](https://developer.apple.com/library/prerelease/ios/documentation/MacOSX/Conceptual/BPInternational/SupportingRight-To-LeftLanguages/SupportingRight-To-LeftLanguages.html#//apple_ref/doc/uid/10000171i-CH17) di Apple.



## <a name="additional-framework-changes"></a>Modifiche aggiuntive del Framework

Oltre alle principali modifiche descritte in precedenza, Apple ha apportato modifiche e miglioramenti a diversi Framework esistenti per iOS 9, inclusi i seguenti:

- Framework di AV Foundation
- Framework AVKit
- Framework CloudKit
- Framework di base
- Framework uniforme
- Framework HealthKit
- Framework HomeKit
- Framework di autenticazione locale
- Framework MapKit
- Framework PassKit
- Framework dei Servizi Safari
- Framework UIKit

Per ulteriori informazioni, vedere la documentazione [aggiuntiva per le modifiche del Framework iOS 9](~/ios/platform/introduction-to-ios9/additional-framework-changes.md) .

## <a name="deprecated-apis-and-functions"></a>API e funzioni deprecate

Apple ha deprecato le API e le funzioni seguenti in iOS 9:

- Rubrica **& interfaccia utente** di Rubrica: queste API sono state sostituite dai framework di contatto e contatto dell'interfaccia utente. Per ulteriori informazioni, vedere la documentazione [dell'interfaccia utente contatti e contatti](~/ios/platform/contacts.md) .
- **CBCentralManager** : i `RetrievePeripherals` metodi `RetrieveConnectedPeripherals` edellaclassesonostatirimossiiniOS9.`CBCentralManager` La chiamata di questi metodi provocherà l'arresto anomalo di un'app durante l'associazione di un accessorio o l'avvio dell'app.
- **FetchAllChanges** -la `FetchAllChanges` della `CKFetchRecordChangesOperation` classe è stata ammortizzata e verrà rimossa in iOS 9.
- **Media Player** : il Framework Media Player è stato deprecato in iOS 9. Usare invece le API AVKit o AV Foundation.

Per un elenco completo di deprecazioni API specifiche, vedere la documentazione relativa alle [differenze dell'API iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/iOS90APIDiffs/index.html#//apple_ref/doc/uid/TP40016222) di Apple.

## <a name="ios-9-sample-apps"></a>App di esempio iOS 9

Per iniziare, sono disponibili alcuni [esempi specifici di iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9) :

- [AstroLayout](https://github.com/xamarin/monotouch-samples/tree/master/ios9/AstroLayout)
- [CollectionView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/CollectionView)
- [MetalPerformanceShadersHelloWorld](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-metalperformanceshadershelloworld)
- [MusicMotion](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-musicmotion)
- [PhotoProgress](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-photoprogress)
- [SegueCatalog](https://docs.microsoft.com/samples/xamarin/ios-samples/ios9-seguecatalog)
- [StackView](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StackView)
- [StickyCorners](https://github.com/xamarin/monotouch-samples/tree/master/ios9/StickyCorners)

Vedere anche le parti di questi esempi per iOS (le versioni Mac OS X complementari in arrivo):

- [AgentsCatalog](https://github.com/xamarin/mac-ios-samples/tree/master/AgentsCatalog)
- [MetalKitEssentials](https://github.com/xamarin/mac-ios-samples/tree/master/MetalKitEssentials)



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di iOS 9](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+iOS9)
- [Introduzione a 3D touch](~/ios/platform/3d-touch.md)
- [ATS (App Transport Security)](~/ios/app-fundamentals/ats.md)
- [Multitasking per iPad](~/ios/platform/multitasking.md)
- [Interfaccia utente contatti e contatti](~/ios/platform/contacts.md)
- [Nuove API di ricerca](~/ios/platform/search/index.md)
- [Introduzione alla visualizzazione dello stack](~/ios/user-interface/controls/uistackview.md)
- [Modifiche della visualizzazione raccolta](~/ios/user-interface/controls/uicollectionview.md)
- [Miglioramenti ai giochi](~/ios/platform/gaming/index.md)
- [Introduzione a HomeKit](~/ios/platform/homekit.md)
- [Introduzione alla consegna](~/ios/platform/handoff.md)
- [Modifiche aggiuntive del framework iOS 9](~/ios/platform/introduction-to-ios9/additional-framework-changes.md)
- [Risoluzione dei problemi](~/ios/platform/introduction-to-ios9/troubleshooting.md)
- [iOS 9 per sviluppatori](https://developer.apple.com/ios/pre-release/)
- [Novità di iOS 9,0](https://developer.apple.com/library/prerelease/ios/releasenotes/General/WhatsNewIniOS/Articles/iOS9.html)
- [Aggiornamento delle app Novell. iOS in iOS9 (video)](https://university.xamarin.com/lightninglectures/Updating-your-XamariniOS-apps-to-iOS9)
