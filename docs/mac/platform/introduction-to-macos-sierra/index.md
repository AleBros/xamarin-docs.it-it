---
title: Introduzione a macOS Sierra
description: Questo articolo descrive tutte le nuove e modificate le API e le funzionalità disponibili in macOS Sierra per gli sviluppatori Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 71A8A737-F310-4320-BD23-743AA1E9033C
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/14/2017
ms.openlocfilehash: 5ab373d708d47ad7c3dbbf4507284be04a1f9934
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-macos-sierra"></a>Introduzione a macOS Sierra

Con il nuovo macOS Sierra, lo sviluppatore può sfruttare nuove API che consentono all'utente finale interagire con le applicazioni e siti Web in modalità disponibile in precedenza. Ad esempio, Apple consente ora i siti Web per fornire ai clienti la possibilità di pagamento in modo sicuro tramite Apple Pay e i miglioramenti per l'aumento di priorità framework Metal grafica di un'app e calcolo di potenziali. 

Per ulteriori informazioni su macOS Sierra, vedere Apple [macOS + app](https://developer.apple.com/macos/) documentazione.

<a name="Whats-New-in-macOS-Sierra" />

## <a name="whats-new-in-macos-sierra"></a>Novità in macOS Sierra

Apple ha aggiunto diversi servizi e le API di nuovo macOS Sierra insieme a molti miglioramenti alle funzionalità esistenti, tra cui:

<a name="Apple-File-System" />

### <a name="apple-file-system"></a>File System di Apple

Con macOS Sierra, Apple ha rilasciato il nuovo File System di Apple come moderni file system per iOS, Mac OS, tvOS e watchOS. Il File System di Apple è stato ottimizzato per l'archiviazione Flash e SSD e offre le seguenti funzionalità: crittografia avanzata, copy-on-write metadati, lo spazio di condivisione, la clonazione dei file e directory, gli snapshot, ridimensionamento directory veloce e atomiche safe Salva primitive.

Per ulteriori informazioni, vedere Apple [Guida di Apple File System](https://developer.apple.com/library/prerelease/content/documentation/FileManagement/Conceptual/APFS_Guide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40016999).

<a name="Apple-Pay-Enhancements" />

### <a name="apple-pay-enhancements"></a>Miglioramenti di pagamento di Apple

Apple ha apportato numerosi miglioramenti a Apple Pay macOS Sierra che consentono all'utente di effettuare i pagamenti protetti da siti Web.

Con macOS Sierra, diverse nuove API sono state aggiunte che funzionano con macOS Sierra, iOS e watchOS per supportare le reti a pagamento dinamico e un nuovo ambiente di test di sandbox.

macOS Sierra include il nuovo framework ApplePay Javascript che consente allo sviluppatore di incorporare Apple Pay direttamente iOS e macOS siti Web basati su Safari. Per i siti Web che supportano Apple Pay, l'utente può autorizzare il pagamento tramite i relativi iPhone o Apple Watch.

Per ulteriori informazioni, vedere Apple [ApplePay JS Framework](https://developer.apple.com/reference/applepayjs) riferimento.

<a name="Building-Modern-macOS-Apps" />

### <a name="building-modern-macos-apps"></a>Compilazione di moderne macOS App

App moderne macOS quali browser web Safari di Apple, pagine elaboratore di testo e numeri molto foglio utilizzate numerose nuove tecnologie per presentare un'interfaccia utente sensibile al contesto unificato che consente agli elementi dell'interfaccia utente tradizionali, ad esempio pannelli mobili e più aprire Windows.

[![Un esempio di una finestra a schede Mac](images/content08.png)](images/content08.png#lightbox)

Il nostro [macOS compilazione di moderne app](~/mac/platform/introduction-to-macos-sierra/modern-cocoa-apps.md) nella Guida sono illustrati alcuni suggerimenti, funzionalità e tecniche di uno sviluppatore può utilizzare per compilare un'app moderne macOS in Xamarin.Mac.

<a name="CloudKit-Data-Sharing" />

### <a name="cloudkit-data-sharing"></a>Condivisione dei dati CloudKit

Il framework CloudKit macOS Sierra per consentire l'utente di condividere facilmente e rapidamente i record o set di record dai relativi database iCloud privata è stata espansa.

CloudKit fornisce un'interfaccia utente completa per l'invio e di accettare gli inviti record condivisi e l'utente disponga di controllo completo, lettura/scrittura le persone che hanno accesso ai record.

Per ulteriori informazioni, vedere Apple [CloudKit Framework riferimento](https://developer.apple.com/reference/clockkit) e [CloudKit JS Framework riferimento](https://developer.apple.com/reference/cloudkitjs).

<a name="Safari-App-Extensions-Support" />

### <a name="safari-app-extensions-support"></a>Supporto delle estensioni App Safari

Le estensioni App Safari consentono all'app di estendere il comportamento del browser web Safari, strettamente integrati con macOS Sierra. Poiché le estensioni Safari macOS funzionano in modo analogo iOS Safari estensioni dell'App, sono facili per la porta da un sistema a un altro.

Per ulteriori informazioni, vedere Apple [Guida per programmatori Safari App estensione](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternetWeb/Conceptual/SafariAppExtension_PG/index.html#//apple_ref/doc/uid/TP40017319).

<a name="Security-and-Privacy-Enhancements" />

### <a name="security-and-privacy-enhancements"></a>Miglioramenti di Privacy e sicurezza

Apple ha migliorato notevolmente sia sicurezza e privacy in macOS Sierra app utili per migliorare la sicurezza dell'applicazione e garantire la privacy dell'utente finale, incluse le seguenti:

- Il nuovo `NSAllowsArbitraryLoadsInWebContent` aggiungere una chiave per l'app `Info.plist` file e consentirà di pagine web caricare correttamente durante la protezione di sicurezza di trasporto di Apple (AT) è ancora abilitata per il resto dell'applicazione.
- Comuni dati sicurezza architettura (CDSA) API è obsoleta e deve essere sostituito con l'API SecKey per generare le chiavi asimmetriche.
- Per tutte le connessioni SSL/TLS, la crittografia simmetrica RC4 è disabilitata per impostazione predefinita. Inoltre, l'API di trasporto protetto non supporta più crittografia SSLv3 e, è consigliabile che l'app interrompere l'uso della crittografia SHA-1 e 3DES appena possibile.
- Poiché negli Appunti di nuovo in iOS 10 e macOS Sierra consente all'utente di copiare e incollare dati tra i dispositivi, l'API è stato espanso per consentire un blocco per Appunti essere limitato a un dispositivo specifico e timestamp da cancellare automaticamente in un determinato punto. Inoltre, denominata pasteboards non sono più persistenti e deve essere sostituito con i contenitori tavolo di montaggio condivisi.
- Se l'applicazione accede ai dati protetti (ad esempio, del calendario dell'utente), si _deve_ dichiarare tale intento con la chiave di valore di stringa scopo corretto nel relativo `Info.plist` file (`NSCalendarUsageDescription` nel caso del calendario).
- App firmato per sviluppatori che non vengono recapitate tramite Mac App Store possono ora sfruttare CloudKit, iCloud Keychain, iCloud unità, le notifiche push remoto, i diritti MapKit e VPN.
- macOS Sierra non supporta più recapito codice esterno o dati insieme all'app codice firmatario nel relativo archivio zip o immagine disco senza segno, come il percorso di runtime non è noto prima dell'esecuzione.

Inoltre, le applicazioni in esecuzione su macOS Sierra (o versioni successive) necessario dichiarare in modo statico l'intenzione di accedere a funzionalità specifiche o informazioni utente immettendo una o più Privacy chiavi specifiche in loro `Info.plist` file segnalare all'utente perché si desidera ottenere l'app accesso.

Poiché macOS Sierra condivide queste modifiche con iOS 10, consultare il nostro iOS 10 [miglioramenti di Privacy e sicurezza](~/ios/app-fundamentals/security-privacy.md) Guida per ulteriori informazioni.

<a name="Smart-Card-Driver-Extension-Support" />

### <a name="smart-card-driver-extension-support"></a>Supporto delle estensioni del Driver di Smart Card

Con macOS Sierra, è possibile creare l'app `NSExtension` basato su driver di smart card che consente l'accesso in sola lettura per il contenuto da determinati tipi di smart card. Le informazioni vengono quindi visualizzate all'interno di sistema keychain (sostituendo il metodo obsoleto architettura di protezione dati comune).

Per ulteriori informazioni, del Pleas vedere Apple [CryptoTokenKit Framework riferimento](https://developer.apple.com/reference/cryptotokenkit).

<a name="Unified-Logging" />

### <a name="unified-logging"></a>Registrazione unificata

La registrazione unificata offre l'app con una singola API per la messaggistica efficiente per tutti i livelli del sistema. Registrazione universale l'app con un controllo accurato più livelli di registrazione che includono controlli sulla privacy e rilevamento per semplificare il debug di attività. 

La registrazione fornisce correlazione automatica dei messaggi quando l'attività di rilevamento e la registrazione vengono usati insieme.

macOS Sierra include una nuova Console di App (in applicazioni/utilità) è in grado di visualizzare i dati di log da più origini, tra cui i dispositivi connessi. Inoltre, supporta le ricerche in formato token e viene salvate e visualizza le connessioni tra i messaggi correlati tra più processi.

Inoltre, i messaggi di log possono essere visualizzati e gestite mediante gli strumenti da riga di comando.

Per ulteriori informazioni, vedere Apple [registrazione riferimento](https://developer.apple.com/reference/os/1891852-logging).

<a name="Wide-Color" />

### <a name="wide-color"></a>Colore Wide

macOS Sierra estende il supporto per i formati di pixel di intervallo esteso e spazi colore ampia in tutto il sistema incluso Framework, ad esempio grafica di base, l'immagine di base, Metal e AVFoundation. Supporto per i dispositivi dotati di schermi di colori ampia ulteriormente è agevolato fornendo questo comportamento in tutto lo stack dell'intero grafico.

Inoltre, `AppKit` è stato modificato per funzionare nel nuovo estesi **sRGB** spazio colore, rendendo più semplice la combinazione di colori in gamme di colori ampia senza perdita significativa delle prestazioni.

Apple offre le seguenti procedure consigliate quando si lavora con colori wide:

- `NSColor` ora utilizza sRGB spazio colore e non verranno più clamp valori per il `0.0` a `1.0` intervallo. Se l'applicazione si basa sul comportamento clamp precedente, è necessario per macOS Sierra sono state modificate.
- Quando si utilizza un'API di basso livello, ad esempio grafica di base o Metal per fornire l'elaborazione di immagini, l'applicazione deve utilizzare un intervallo esteso colore pixel e spazio formato che supporta valori a virgola mobile a 16 bit. Se necessario, l'app dovrà clamp manualmente i valori dei componenti di colore.
- Grafica di base, immagine di Core e Metal prestazioni shader fornire nuovi metodi per la conversione tra gli spazi di due colori.

Per ulteriori dettagli, vedere il nostro [Introduzione ai colori ampia](~/ios/platform/wide-color.md) Guida.

<a name="Additional-Framework-Changes" />

## <a name="additional-framework-changes"></a>Modifiche di .NET Framework

Oltre alle modifiche framework principali e le aggiunte elencate in precedenza, Apple ha molte modifiche aggiuntive framework secondaria macOS Sierra.

Per ulteriori dettagli, consultare il nostro [ulteriori modifiche di Framework](~/mac/platform/introduction-to-macos-sierra/additional-framework-changes.md) Guida.

<a name="Deprecated-APIs" />

## <a name="deprecated-apis"></a>API deprecate

Le seguenti API sono state deprecate in macOS Sierra:

- Il File System HFS Standard non è più supportato.

Vedere Apple [v10.12 OS X API differenze](https://developer.apple.com/library/prerelease/content/releasenotes/Miscellaneous/APIDiffsMacOS10_12/index.html) documentazione per un elenco completo delle deprecations e delle modifiche.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di Mac](https://developer.xamarin.com/samples/mac/)
- [Novità di OS X 10.12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
