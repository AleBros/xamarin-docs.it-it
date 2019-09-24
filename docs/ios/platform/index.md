---
title: Panoramica delle funzionalità della piattaforma iOS
description: Questo documento contiene collegamenti a diverse guide che descrivono le funzionalità introdotte in diverse versioni di iOS e altre funzionalità della piattaforma iOS.
ms.prod: xamarin
ms.assetid: 9F6A27E5-8A87-ADE2-D1EF-5684E7B8C999
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/25/2018
ms.openlocfilehash: c6385ff193c54fdab8f252c757cad810751b3f08
ms.sourcegitcommit: 09bc69d7119a04684c9e804c5cb113b8b1bb7dfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71206294"
---
# <a name="ios-platform-features-overview"></a>Panoramica delle funzionalità della piattaforma iOS

Questa pagina elenca le versioni recenti di iOS, oltre ad evidenziare alcuni dei framework Apple a cui è possibile accedere con Novell. iOS.

## <a name="ios-releases"></a>versioni di iOS

|  |  |
|-------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Introduzione a iOS 13](~/ios/platform/ios13/index.md) | Questo documento descrive Novell. iOS 13.|
| [Introduzione a iOS 12](~/ios/platform/introduction-to-ios12/index.md) | Questo documento descrive le funzionalità di iOS 12 disponibili per la compilazione di applicazioni Novell. iOS.|
| [Introduzione a iOS 11](~/ios/platform/introduction-to-ios11/index.md) | Questo documento descrive le funzionalità nuove e aggiornate in iOS 11 e Xcode 9, ad esempio ARKit, Core ML, Core NFC, trascinamento della selezione, MapKit, PDFKit, SiriKit e visione. Sono disponibili collegamenti a guide che descrivono come usare queste funzionalità con Novell. iOS. |
| [Introduzione a iOS 10](~/ios/platform/introduction-to-ios10/index.md) | iOS 10 include numerose nuove API e servizi che consentono di sviluppare app con nuove funzionalità e funzionalità. Con iOS 10, le app hanno nuove funzionalità, ad esempio l'estensione di mappe, messaggi, telefono e Siri. Questa sezione illustra come sfruttare i vantaggi di queste funzionalità in un'app Novell. iOS. |
| [Introduzione a iOS 9](~/ios/platform/introduction-to-ios9/index.md)   | Questa sezione definisce le modifiche apportate in iOS 9 quando si esegue l'aggiornamento da iOS 8 e come usare queste funzionalità in un'app Novell. iOS. |
| [Introduzione a iOS 8](~/ios/platform/introduction-to-ios8.md)         | iOS 8 ha apportato un numero elevato di modifiche al sistema operativo da iOS 7. Qui vengono illustrati gli elementi e le modalità d'uso. |
| [Introduzione a iOS 7](~/ios/platform/introduction-to-ios7/index.md)   | Informazioni sulle nuove API principali introdotte in iOS 7, incluse le transizioni del controller di visualizzazione, i miglioramenti apportati alle animazioni UIView, UIKit Dynamics e Text Kit. |
| [Introduzione a iOS 6](~/ios/platform/introduction-to-ios6/index.md)   | Spiegazioni delle funzionalità introdotte in iOS 6, incluse le visualizzazioni di raccolta, il kit di pass, il kit di eventi e il Framework di social networking. |

## <a name="apple-payiosplatformapple-paymd"></a>[Apple Pay](~/ios/platform/apple-pay.md)

Apple Pay è stato introdotto insieme a iOS 8, consentendo agli utenti di pagare beni fisici, ad esempio cibo, intrattenimento e appartenenze tramite i dispositivi iOS. È disponibile in iPhone 6 e iPhone 6 Plus e può anche essere abbinato al Apple Watch per gli acquisti in-Store. Quando viene usato in un iPhone, USA Touch ID per confermare e autorizzare le transazioni sulla carta di credito o debito di un utente.

## <a name="callkitiosplatformcallkitmd"></a>[CallKit](~/ios/platform/callkit.md)

La nuova API CallKit in iOS 10 consente alle app VOIP di integrarsi con l'interfaccia utente di iPhone e fornire all'utente finale un'interfaccia familiare ed esperienza. Con questa API gli utenti possono visualizzare e interagire con le chiamate VOIP dalla schermata di blocco del dispositivo iOS e gestire i contatti usando le visualizzazioni **Preferiti** e **recenti** dell'app per telefoni.

## <a name="contacts-and-contactsuiiosplatformcontactsmd"></a>[Contacts e ContactsUI](~/ios/platform/contacts.md)

Con l'introduzione di iOS 9, Apple ha rilasciato due nuovi Framework, `Contacts` e `ContactsUI`, che sostituiscono i Framework dell'interfaccia utente di Rubrica e rubrica esistenti usati da iOS 8 e versioni precedenti.

## <a name="document-pickeriosplatformdocument-pickermd"></a>[Selezione documenti](~/ios/platform/document-picker.md)

Il selettore documenti consente la condivisione dei documenti tra le app. Questi documenti possono essere archiviati in iCloud o in una directory di un'altra app. I documenti vengono condivisi tramite il set di [estensioni del provider di documenti](~/ios/platform/extensions.md) installato dall'utente sul dispositivo.

## <a name="eventkitiosplatformeventkitmd"></a>[EventKit](~/ios/platform/eventkit.md)

iOS include due applicazioni correlate al calendario: l'applicazione calendario e l'applicazione promemoria. È abbastanza semplice per comprendere in che modo l'applicazione Calendar gestisce i dati del calendario, ma l'applicazione promemoria è meno ovvia. I promemoria possono effettivamente avere date associate in termini di quando sono dovuti, quando vengono completati e così via. In questo modo, iOS archivia tutti i dati del calendario, sia che si tratti di eventi del calendario o promemoria, in un'unica posizione, denominato *database del calendario*.

## <a name="ios-extensionsiosplatformextensionsmd"></a>[estensioni iOS](~/ios/platform/extensions.md)

Le estensioni, come introdotte in iOS 8, sono `UIViewControllers` specializzate presentate da iOS all'interno di contesti standard, ad esempio all'interno del **Centro notifiche**, come tipi di tastiera personalizzati richiesti dall'utente per eseguire l'input specializzato o altri contesti Analogamente alla modifica di una foto in cui l'estensione può fornire filtri per effetti speciali.

## <a name="graphics-and-animation-in-iosiosplatformgraphics-animation-iosindexmd"></a>[Grafica e animazione in iOS](~/ios/platform/graphics-animation-ios/index.md)

Grafica e animazione in iOS illustra i concetti di base della grafica in iOS, ad esempio CoreImage, Core Graphics e Core Animation.

## <a name="handoffiosplatformhandoffmd"></a>[Handoff](~/ios/platform/handoff.md)

Apple ha introdotto la consegna in iOS 8 e OS X Yosemite (10,10) per fornire un meccanismo comune per l'utente per trasferire le attività avviate in uno dei loro dispositivi, a un altro dispositivo che esegue la stessa app o un'altra app che supporta la stessa attività.

## <a name="healthkitiosplatformhealthkitmd"></a>[HealthKit](~/ios/platform/healthkit.md)

Il kit di integrità fornisce un archivio dati protetto per le informazioni relative all'integrità dell'utente. Le app del kit di integrità possono, con l'autorizzazione esplicita dell'utente, leggere e scrivere in questo archivio dati e ricevere notifiche quando vengono aggiunti dati pertinenti. Le app possono presentare i dati oppure l'utente può usare l'app per l'integrità fornita da Apple per visualizzare un dashboard di tutti i relativi dati.

## <a name="homekitiosplatformhomekitmd"></a>[HomeKit](~/ios/platform/homekit.md)

Apple ha introdotto HomeKit in iOS 8 per fornire un Framework comune per l'individuazione e la comunicazione con i dispositivi di automazione domestica nella Home page di un utente. HomeKit fornisce una piattaforma comune per la configurazione dei dispositivi e la configurazione delle azioni per controllarli.

## <a name="in-app-purchasingiosplatformin-app-purchasingindexmd"></a>[Acquisto in-app](~/ios/platform/in-app-purchasing/index.md)

le applicazioni iOS possono vendere prodotti o servizi digitali usando StoreKit, un set di API fornite da iOS che comunicano con i server Apple per condurre transazioni finanziarie con l'utente tramite il proprio ID Apple. Le API StoreKit riguardano principalmente il recupero delle informazioni sul prodotto e l'esecuzione di transazioni. non esiste alcun componente dell'interfaccia utente. Le applicazioni che implementano l'acquisto in-app devono compilare la propria interfaccia utente e tenere traccia degli elementi acquistati con codice personalizzato per fornire all'utente i prodotti o i servizi richiesti.

## <a name="ios-gaming-apisiosplatformgamingindexmd"></a>[API per giochi iOS](~/ios/platform/gaming/index.md)

Apple ha apportato diversi miglioramenti tecnologici alle API di gioco in iOS 9 che semplificano l'implementazione di grafica e audio di giochi in un'app Novell. iOS. Sono inclusi sia la facilità di sviluppo che i Framework di alto livello, sfruttando la potenza della GPU del dispositivo iOS per migliorare la velocità e le funzionalità grafiche.

## <a name="message-app-integrationiosplatformmessage-app-integrationindexmd"></a>[Integrazione dell'app Message](~/ios/platform/message-app-integration/index.md)

Una novità di iOS 10, un'estensione di app per **i messaggi si** integra con l'app Messages e presenta nuove funzionalità all'utente. L'estensione può inviare testo, adesivi, file multimediali e messaggi interattivi.

## <a name="multitasking-for-ipadiosplatformmultitaskingmd"></a>[Multitasking per iPad](~/ios/platform/multitasking.md)

iOS 9 aggiunge il supporto multitasking per l'esecuzione di due app allo stesso tempo su hardware iPad specifico. Il multitasking per iPad è supportato tramite le funzionalità seguenti: Scorrere, suddividere la visualizzazione & immagine in figura.

## <a name="passkitiosplatformpasskitmd"></a>[PassKit](~/ios/platform/passkit.md)

Passbook è un'app per iPhone e iPod touch con iOS 6. Archivia e Visualizza i codici a barre e altre informazioni per collegare le transazioni dei clienti sul telefono con il "mondo reale". Le sessioni vengono generate dai commercianti e inviate al cliente tramite posta elettronica, URL o dall'interno di un'app iOS. Passbook archivia e organizza tutti i passaggi in un telefono e Visualizza i promemoria di pass per la schermata di blocco, a seconda della data/ora o della posizione del dispositivo.

Questo documento introduce Passbook, usando l'API pass kit con Novell. iOS e illustra come implementare i passaggi nel server.

## <a name="photokitiosplatformphotokitmd"></a>[PhotoKit](~/ios/platform/photokit.md)

Photo kit è un nuovo Framework che consente alle applicazioni di eseguire query nella libreria di immagini del sistema e creare interfacce utente personalizzate per visualizzarne e modificarne il contenuto. Include varie classi che rappresentano asset di immagini e video, nonché raccolte di asset come album e cartelle.

## <a name="request-app-reviewiosplatformrequest-app-reviewmd"></a>[Richiedi Revisione app](~/ios/platform/request-app-review.md)

Una novità di iOS 10,3, `RequestReview()` il metodo consente a un'app per iOS di richiedere all'utente di valutarla o esaminarla. Quando questo metodo viene chiamato in un'app di spedizione che l'utente ha installato dall'App Store, iOS 10 gestirà l'intero processo di valutazione e revisione per lo sviluppatore. Poiché questo processo è regolato dai criteri dell'app Store, è possibile che venga visualizzato un avviso.

## <a name="search-apisiosplatformsearchindexmd"></a>[API per la ricerca](~/ios/platform/search/index.md)

La ricerca è stata espansa in iOS 9 per offrire nuovi modi per accedere alle informazioni e alle funzionalità all'interno di un'app Novell. iOS. Usando le nuove API di ricerca per le app, il contenuto delle app viene reso ricercabile attraverso i risultati della ricerca in evidenza e Safari, i promemoria e i suggerimenti per la consegna e Siri. Ciò consente agli utenti di accedere rapidamente alle attività e alle informazioni in modo approfondito nell'app.

## <a name="sirikitiosplatformsirikitindexmd"></a>[SiriKit](~/ios/platform/sirikit/index.md)

Una novità di iOS 10, SiriKit consente a un'app per iOS di fornire servizi accessibili all'utente usando Siri e l'app Maps in un dispositivo iOS usando le estensioni delle app e **i nuovi Framework** **dell'interfaccia utente** Intent e Intent.

## <a name="social-frameworkiosplatformsocial-frameworkmd"></a>[Social Framework](~/ios/platform/social-framework.md)

Il Framework di social networking fornisce un'API unificata per interagire con i social network, inclusi _Twitter_ e _Facebook_, oltre a _SinaWeibo_ per gli utenti in Cina.

## <a name="speech-recognitioniosplatformspeechmd"></a>[Riconoscimento vocale](~/ios/platform/speech.md)

iOS 10 include un nuovo Speech API che consente all'app di supportare il riconoscimento vocale continuo e la traduzione vocale (da flussi audio in tempo reale o registrato).

## <a name="textkitiosplatformtextkitmd"></a>[TextKit](~/ios/platform/textkit.md)

Il kit di testo è una nuova API che offre funzionalità avanzate per il layout e il rendering del testo. Si basa sul Framework di testo principale di basso livello, ma è molto più semplice da usare rispetto al testo principale.

## <a name="3d-touchiosplatform3d-touchmd"></a>[3D Touch](~/ios/platform/3d-touch.md)

Questo articolo fornisce e introduce l'uso delle nuove API Touch 3D per aggiungere movimenti sensibili alla pressione alle app Novell. iOS in esecuzione nei nuovi dispositivi iPhone 6s e iPhone 6s Plus.

## <a name="touch-idiosplatformtouchidmd"></a>[ID tocco](~/ios/platform/touchid.md)

Touch ID è stato introdotto in iOS 7 come mezzo per l'autenticazione dell'utente, simile a un codice di accesso. Tuttavia, era limitato allo sblocco del dispositivo, usando l'App Store, usando iTunes e autenticando solo il keychain di iCloud.

## <a name="user-notificationsiosplatformuser-notificationsindexmd"></a>[Notifiche utente](~/ios/platform/user-notifications/index.md)

Una novità di iOS 10, il Framework di notifica utente consente la distribuzione e la gestione di notifiche locali e remote. Con questo Framework, l'estensione app o app può pianificare il recapito delle notifiche locali specificando un set di condizioni, ad esempio la posizione o l'ora del giorno.

## <a name="wide-coloriosplatformwide-colormd"></a>[Wide Color](~/ios/platform/wide-color.md)

iOS 10 e macOS Sierra migliorano il supporto per i formati pixel estesi e gli spazi dei colori a gamma ampia in tutto il sistema, inclusi i Framework come la grafica principale, le immagini di base, i metal e AVFoundation. Il supporto per i dispositivi con schermi Wide Color è ulteriormente facilitato fornendo questo comportamento nell'intero stack di grafica.

## <a name="binding-objective-cbinding-objective-cindexmd"></a>[Binding di Objective-C](binding-objective-c/index.md)

Quando si utilizza iOS, è possibile che si verifichino casi in cui si desidera utilizzare una libreria Objective-C di terze parti. In questi casi, è possibile usare i progetti di binding MonoTouch per creare C# un'associazione alle librerie Objective-C native. Il progetto usa gli stessi strumenti usati per portare le API iOS a C#. Questo documento descrive come associare le API Objective-C.

## <a name="referencing-native-librariesnative-interopmd"></a>[Riferimento a librerie native](native-interop.md)

Novell. iOS supporta il collegamento con le librerie C native e le librerie Objective-C. Questo documento illustra come collegare le librerie C native al progetto Novell. iOS.

## <a name="embedded-frameworksembedded-frameworksmd"></a>[Framework incorporati](embedded-frameworks.md)

Viene illustrato come incorporare i Framework utente Objective-C nelle app Novell. iOS.
