---
title: Introduzione a macOS Sierra
description: Questo articolo illustra tutte le nuove e modificate le API e funzionalità disponibili in macOS Sierra per gli sviluppatori di xamarin. Mac.
ms.prod: xamarin
ms.assetid: 71A8A737-F310-4320-BD23-743AA1E9033C
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 03/14/2017
ms.openlocfilehash: 5a944fd8f7dcfdcbb3f025c92b4afac35673416f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61031503"
---
# <a name="introduction-to-macos-sierra"></a>Introduzione a macOS Sierra

Con il nuovo macOS Sierra, lo sviluppatore può sfruttare i vantaggi delle nuove API che consentono all'utente finale di interagire con le App e siti Web in modi non erano disponibili. Ad esempio, Apple ora consente ai siti Web di offrire ai clienti la possibilità di pagare in modo sicuro tramite Apple Pay e i miglioramenti per l'aumento di priorità di framework Metal grafica di un'app e computing potenziali. 

Per altre informazioni su macOS Sierra, vedi di Apple [macOS + app](https://developer.apple.com/macos/) documentazione.

<a name="Whats-New-in-macOS-Sierra" />

## <a name="whats-new-in-macos-sierra"></a>What ' s New in macOS Sierra

Apple ha aggiunto diverse nuove API e servizi in macOS Sierra insieme a molti miglioramenti alle funzionalità esistenti, tra cui:

<a name="Apple-File-System" />

### <a name="apple-file-system"></a>Apple File System

Con macOS Sierra, Apple ha rilasciato il nuovo Apple File System come file moderne system per iOS, macOS, tvOS e watchOS. Il File System di Apple è stato ottimizzato per l'archiviazione Flash e SSD e offre le seguenti funzionalità: crittografia avanzata, i metadati, copy-on-write dello spazio di condivisione, la clonazione per file e directory, gli snapshot, ridimensionamento rapido directory e atomiche safe salvataggio primitive.

Per altre informazioni, vedere di Apple [Guida di Apple File System](https://developer.apple.com/library/prerelease/content/documentation/FileManagement/Conceptual/APFS_Guide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40016999).

<a name="Apple-Pay-Enhancements" />

### <a name="apple-pay-enhancements"></a>Miglioramenti di Apple Pay

Apple ha apportato diversi miglioramenti a Apple Pay macOS Sierra che consentono all'utente di effettuare pagamenti sicuri da siti Web.

Con macOS Sierra, diverse nuove API sono state aggiunte che funzionano con macOS Sierra, iOS e watchOS per supportare le reti a pagamento dinamico e un nuovo ambiente di test di sandbox.

macOS Sierra include il nuovo framework ApplePay Javascript che consente agli sviluppatori di incorporare Apple Pay direttamente in iOS e macOS di siti Web basati su Safari. Per i siti Web che supportano Apple Pay, l'utente può autorizzare pagamento usando le iPhone o Apple Watch.

Per altre informazioni, vedere di Apple [ApplePay JS Framework](https://developer.apple.com/reference/applepayjs) riferimento.

<a name="Building-Modern-macOS-Apps" />

### <a name="building-modern-macos-apps"></a>Creazione di app macOS moderne

App macOS moderne, ad esempio browser web Safari, pagine elaboratore di testo e numeri molto foglio utilizzate molte nuove tecnologie per presentare un'interfaccia utente unificata, sensibile al contesto che consente agli elementi dell'interfaccia utente tradizionali, ad esempio pannelli mobili e più aprire Apple Windows.

[![Un esempio di una finestra a schede Mac](images/content08.png)](images/content08.png#lightbox)

Nostri [App macOS moderne predefiniti](~/mac/platform/introduction-to-macos-sierra/modern-cocoa-apps.md) nella Guida sono illustrati alcuni suggerimenti, funzionalità e tecniche di uno sviluppatore può utilizzare per creare un'app macOS moderne in xamarin. Mac.

<a name="CloudKit-Data-Sharing" />

### <a name="cloudkit-data-sharing"></a>Condivisione dei dati CloudKit

Il framework CloudKit è stata estesa in macOS Sierra per consentire facilmente e rapidamente condividono record o i set di record dal database iCloud privata utente.

CloudKit fornisce un'interfaccia utente completa per l'invio e la possibile accettare inviti record condivisi e l'utente ha il controllo completo in lettura/scrittura tramite le persone che hanno accesso ai record.

Per altre informazioni, vedere di Apple [riferimento a Framework CloudKit](https://developer.apple.com/reference/clockkit) e [CloudKit JS Framework riferimento](https://developer.apple.com/reference/cloudkitjs).

> [!IMPORTANT]
> Apple [fornisce strumenti](https://developer.apple.com/support/allowing-users-to-manage-data/) per aiutare gli sviluppatori a gestire correttamente il Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea.

<a name="Safari-App-Extensions-Support" />

### <a name="safari-app-extensions-support"></a>Supporto delle estensioni di App di Safari

Le estensioni dell'App Safari consentire all'app estendere il comportamento del browser web Safari durante in corso strettamente integrato con macOS Sierra. Poiché le estensioni dell'App Safari macOS funzionano in modo analogo ai dispositivi iOS le estensioni dell'App Safari, sono facilmente alla porta da un sistema a un altro.

Per altre informazioni, vedere di Apple [Guida per programmatori estensione App Safari](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternetWeb/Conceptual/SafariAppExtension_PG/index.html#//apple_ref/doc/uid/TP40017319).

<a name="Security-and-Privacy-Enhancements" />

### <a name="security-and-privacy-enhancements"></a>Miglioramenti della protezione e Privacy

Apple ha apportato numerosi miglioramenti al sia sicurezza e privacy in macOS Sierra app utili per migliorare la sicurezza dell'app e garantire la privacy dell'utente finale, incluse le seguenti:

- Il nuovo `NSAllowsArbitraryLoadsInWebContent` aggiungibile chiave per l'app `Info.plist` file e consentirà alle pagine web di caricare correttamente mentre protezione Apple Transport Security (ATS) è ancora abilitata per il resto dell'app.
- Common Data Security Architecture (CDSA) API è obsoleta e deve essere sostituito con l'API SecKey per generare le chiavi asimmetriche.
- Per tutte le connessioni SSL/TLS, la crittografia simmetrica RC4 è disabilitata per impostazione predefinita. Inoltre, l'API di trasporto sicuro non supporta più SSLv3 ed è consigliabile che l'app interrompere l'uso della crittografia SHA-1 e 3DES appena possibile.
- Poiché nuovi negli Appunti in iOS 10 e macOS Sierra consente all'utente di copia e incolla tra i dispositivi, l'API è stata estesa per consentire gli Appunti essere limitata a un dispositivo specifico e che timestamp da cancellare automaticamente in un determinato punto. Inoltre, pasteboards denominati non sono più persistenti e devono essere sostituiti con i contenitori tavolo di montaggio condivisi.
- Se l'app accede a dati protetti (ad esempio, al calendario dell'utente), si _deve_ dichiarare tale intento con la chiave di valore di stringa scopo corretto nel relativo `Info.plist` file (`NSCalendarUsageDescription` nel caso del calendario).
- Le app firmato per gli sviluppatori che non vengono recapitate tramite il Store di App Mac possono ora sfruttare CloudKit, iCloud Keychain, iCloud Drive, notifiche push remoto, i diritti MapKit e VPN.
- macOS Sierra non supporta più recapito codice esterno o data e l'app di codice firmatario nel relativo archivio zip o un'immagine disco senza segno come il percorso di runtime non è noto prima della fase di esecuzione.

Inoltre, le App in esecuzione in macOS Sierra (o versioni successive) deve dichiarare in modo statico manifestato l'intenzione di accedere a funzionalità specifiche o informazioni utente immettendo una o più delle chiavi specifici sulla Privacy in loro `Info.plist` file in cui viene illustrato all'utente perché l'app desidera ottenere accesso.

Poiché macOS Sierra condivide queste modifiche con iOS 10, vedere di iOS 10 [miglioramenti della protezione e Privacy](~/ios/app-fundamentals/security-privacy.md) Guida per altre informazioni.

<a name="Smart-Card-Driver-Extension-Support" />

### <a name="smart-card-driver-extension-support"></a>Supporto delle estensioni del Driver delle Smart Card

Con macOS Sierra, è possibile creare l'app `NSExtension` basati i driver delle smart card che consenta l'accesso di sola lettura del contenuto da determinati tipi di smart card. Queste informazioni viene quindi presentate all'interno di sistema keychain (sostituendo il metodo di architettura di protezione dati comune deprecato).

per altre informazioni, del Pleas vedere Apple [riferimento a Framework CryptoTokenKit](https://developer.apple.com/reference/cryptotokenkit).

<a name="Unified-Logging" />

### <a name="unified-logging"></a>Registrazione unificata

La registrazione unificata fornisce l'app con un'unica API per la messaggistica efficiente per tutti i livelli del sistema. Con Unified Logging l'app ha il controllo con granularità fine su più livelli di registrazione che includono controlli sulla privacy e l'attività di rilevamento per semplificare il debug. 

La registrazione fornisce la correlazione automatica dei messaggi quando l'attività di rilevamento e la registrazione vengono usati insieme.

macOS Sierra include una nuova App Console (in applicazioni/utilità) che è in grado di visualizzare i dati di log da più origini, inclusi i dispositivi connessi. Inoltre, supporta le ricerche in formato token e viene salvate e visualizza le connessioni tra i messaggi correlati tra più processi.

Inoltre, i messaggi di log possono essere visualizzati e gestite mediante gli strumenti da riga di comando.

Per altre informazioni, vedere di Apple [registrazione riferimento](https://developer.apple.com/reference/os/1891852-logging).

<a name="Wide-Color" />

### <a name="wide-color"></a>Wide Color

macOS Sierra estende il supporto per formati pixel intervalli estesi e gli spazi colore a livello di gamma in tutto il sistema, tra cui Framework quali Core Graphics, immagine di base, bare Metal e AVFoundation. Supporto per i dispositivi dotati di schermi di ampia gamma di colori viene diminuito ulteriormente fornendo questo comportamento in tutto lo stack di grafica intero.

È inoltre `AppKit` è stato modificato a funzionare nella nuova esteso **sRGB** spazio colore, rendendo più semplice combinare i colori usati nel gamme di ampia gamma di colori senza riduzione significativa delle prestazioni.

Apple offre le seguenti procedure ottimali quando si lavora con i colori wide:

- `NSColor` a questo punto viene utilizzato il sRGB spazio colore e non sarà più limitare i valori per il `0.0` a `1.0` intervallo. Se l'app si basa sul comportamento del clamp precedente, devono essere modificate per macOS Sierra.
- Quando si usa un'API di basso livello, ad esempio Core Graphics o bare Metal per fornire l'elaborazione di immagini, l'app deve usare un intervallo esteso colore pixel e spazio di formato che supporta valori a virgola mobile a 16 bit. Se necessario, l'app dovrà limitare manualmente i valori dei componenti di colore.
- Grafica di base, immagine di base e gli shader di Metal prestazioni offrono nuovi metodi per la conversione tra gli spazi due colore.

Per altre informazioni, vedere la [Introduzione a ampia gamma di colori](~/ios/platform/wide-color.md) Guida.

<a name="Additional-Framework-Changes" />

## <a name="additional-framework-changes"></a>Modifiche di altri Framework

Oltre alle modifiche principali framework e componenti aggiuntivi elencati in precedenza, Apple molti altri framework minori modifiche apportate in macOS Sierra.

Per altre informazioni, vedere la [modifiche aggiuntive del Framework](~/mac/platform/introduction-to-macos-sierra/additional-framework-changes.md) Guida.

<a name="Deprecated-APIs" />

## <a name="deprecated-apis"></a>API deprecate

Le API seguenti sono state deprecate in macOS Sierra:

- Il File System HFS Standard non è più supportato.

Vedere di Apple [OS X v10.12 API diff](https://developer.apple.com/library/prerelease/content/releasenotes/Miscellaneous/APIDiffsMacOS10_12/index.html) documentazione per un elenco completo di modifiche e deprecati.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di Mac](https://developer.xamarin.com/samples/mac/)
- [Novità in OS X 10.12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
