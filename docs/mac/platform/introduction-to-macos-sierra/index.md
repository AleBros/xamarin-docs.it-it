---
title: Introduzione a macOS Sierra
description: Questo articolo presenta tutte le API e le funzionalità nuove e modificate disponibili in macOS Sierra per gli sviluppatori Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 71A8A737-F310-4320-BD23-743AA1E9033C
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 31aa4d1fb8b77b88fc6b6790147409ff8ec13145
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029843"
---
# <a name="introduction-to-macos-sierra"></a>Introduzione a macOS Sierra

Con il nuovo macOS Sierra, lo sviluppatore può sfruttare le nuove API che consentono all'utente finale di interagire con le app e i siti Web in modo non disponibile in precedenza. Ad esempio, Apple ora consente ai siti Web di offrire ai clienti la possibilità di pagare in modo sicuro tramite Apple Pay e i miglioramenti apportati al Framework di Metal potenziare la grafica e il potenziale di elaborazione di un'app. 

Per altre informazioni su macOS Sierra, vedere la documentazione di Apple [MacOS + Apps](https://developer.apple.com/macos/) .

<a name="Whats-New-in-macOS-Sierra" />

## <a name="whats-new-in-macos-sierra"></a>Novità di macOS Sierra

Apple ha aggiunto diverse nuove API e servizi in macOS Sierra insieme a numerosi miglioramenti apportati alle funzionalità esistenti, tra cui:

<a name="Apple-File-System" />

### <a name="apple-file-system"></a>File system Apple

Con macOS Sierra, Apple ha rilasciato il nuovo file system Apple come file system moderno per iOS, macOS, tvOS e watchos. Il file system Apple è stato ottimizzato per l'archiviazione flash e SSD e offre le funzionalità seguenti: crittografia avanzata, metadati copy-on-Write, condivisione dello spazio, clonazione per file e directory, snapshot, ridimensionamento rapido delle directory e primitive atomiche per il salvataggio.

Per altre informazioni, vedere la Guida di Apple [file System](https://developer.apple.com/library/prerelease/content/documentation/FileManagement/Conceptual/APFS_Guide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40016999)di Apple.

<a name="Apple-Pay-Enhancements" />

### <a name="apple-pay-enhancements"></a>Miglioramenti di Apple Pay

Con Apple sono stati apportati numerosi miglioramenti Apple Pay in macOS Sierra che consentono all'utente di effettuare pagamenti sicuri da siti Web.

Con macOS Sierra sono state aggiunte diverse nuove API che funzionano con macOS Sierra, iOS e watchos per supportare le reti di pagamento dinamiche e un nuovo ambiente di test sandbox.

macOS Sierra include il nuovo Framework JavaScript ApplePay che consente allo sviluppatore di incorporare Apple Pay direttamente nei siti Web iOS e macOS basati su Safari. Per i siti Web che supportano Apple Pay, l'utente può autorizzare il pagamento usando l'iPhone o Apple Watch.

Per altre informazioni, vedere la Guida di riferimento a [APPLEPAY JS framework](https://developer.apple.com/reference/applepayjs) di Apple.

<a name="Building-Modern-macOS-Apps" />

### <a name="building-modern-macos-apps"></a>Creazione di app macOS moderne

Le app macOS moderne, ad esempio il Web browser Safari di Apple, le pagine di Elaboratore di testo e dei numeri, usano molte nuove tecnologie per presentare un'interfaccia utente unificata e sensibile al contesto che si distingue dagli elementi dell'interfaccia utente tradizionali, come i pannelli mobili e più aperti Windows.

[![un esempio di finestra Mac a schede](images/content08.png)](images/content08.png#lightbox)

La guida per la [creazione di app MacOS moderne](~/mac/platform/introduction-to-macos-sierra/modern-cocoa-apps.md) illustra diversi suggerimenti, funzionalità e tecniche che uno sviluppatore può usare per creare un'app MacOS moderna in Xamarin.Mac.

<a name="CloudKit-Data-Sharing" />

### <a name="cloudkit-data-sharing"></a>Condivisione dei dati di CloudKit

Il Framework CloudKit è stato espanso in macOS Sierra per consentire agli utenti di condividere in modo semplice e rapido record o set di record dai rispettivi database iCloud privati.

CloudKit fornisce un'interfaccia utente completa per l'invio e l'accettazione degli inviti ai record condivisi e l'utente dispone di un controllo completo in lettura/scrittura per gli utenti che hanno accesso ai record.

Per altre informazioni, vedere la Guida di [riferimento a CloudKit Framework](https://developer.apple.com/reference/clockkit) di Apple e il [riferimento a CloudKit JS framework](https://developer.apple.com/reference/cloudkitjs).

> [!IMPORTANT]
> Apple [fornisce strumenti](https://developer.apple.com/support/allowing-users-to-manage-data/) per aiutare gli sviluppatori a gestire correttamente il Regolamento generale sulla protezione dei dati (GDPR) dell'Unione Europea.

<a name="Safari-App-Extensions-Support" />

### <a name="safari-app-extensions-support"></a>Supporto delle estensioni delle app Safari

Le estensioni delle app Safari consentono all'app di estendere il comportamento del Web browser Safari, integrando in macOS Sierra. Poiché le estensioni dell'app macOS Safari funzionano in modo simile alle estensioni delle app iOS Safari, è facile trasferire da un sistema a un altro.

Per ulteriori informazioni, vedere la guida alla [programmazione dell'estensione Safari app](https://developer.apple.com/library/prerelease/content/documentation/NetworkingInternetWeb/Conceptual/SafariAppExtension_PG/index.html#//apple_ref/doc/uid/TP40017319)di Apple.

<a name="Security-and-Privacy-Enhancements" />

### <a name="security-and-privacy-enhancements"></a>Miglioramenti alla sicurezza e alla privacy

Apple ha apportato diversi miglioramenti alla sicurezza e alla privacy in macOS Sierra che consentiranno all'app di migliorare la sicurezza dell'app e di garantire la privacy degli utenti finali, inclusi i seguenti:

- La nuova chiave di `NSAllowsArbitraryLoadsInWebContent` può essere aggiunta al file di `Info.plist` dell'app e consentirà di caricare le pagine Web correttamente, mentre la protezione di Apple Transport Security (ATS) è ancora abilitata per il resto dell'app.
- L'API CDSA (Common Data Security Architecture) è stata deprecata e deve essere sostituita con l'API SecKey per generare chiavi asimmetriche.
- Per tutte le connessioni SSL/TLS, la crittografia simmetrica RC4 è ora disabilitata per impostazione predefinita. Inoltre, l'API trasporto sicuro non supporta più SSLv3 ed è consigliabile che l'app smetta di usare la crittografia SHA-1 e 3DES il prima possibile.
- Poiché i nuovi Appunti in iOS 10 e macOS Sierra consentono all'utente di copiare e incollare tra dispositivi, l'API è stata espansa in modo da consentire la limitazione di uno degli Appunti a un dispositivo specifico e il timestamp da cancellare automaticamente in un determinato punto. Inoltre, il nome Pasteboards non è più permanente e deve essere sostituito con i contenitori di montaggio condivisi.
- Se l'app accede ai dati protetti (ad esempio, il calendario dell'utente), _deve_ dichiarare tale finalità con la chiave del valore stringa per finalità corrette nel file `Info.plist` (`NSCalendarUsageDescription` nel caso del calendario).
- Le app firmate per sviluppatori che non vengono recapitate tramite Mac App Store possono ora sfruttare i vantaggi di CloudKit, keychain, unità iCloud, notifiche push remote, MapKit e diritti VPN.
- macOS Sierra non supporta più la distribuzione di codice o dati esterni insieme all'app code-Signer nell'archivio zip o nell'immagine disco senza segno perché il percorso di runtime non è noto prima del runtime.

Inoltre, le app in esecuzione in macOS Sierra (o versioni successive) devono dichiarare in modo statico l'intento di accedere a funzionalità specifiche o a informazioni utente immettendo una o più chiavi specifiche per la privacy nei file `Info.plist` che spieghino all'utente il motivo per cui l'app desidera ottenere l'accesso .

Poiché macOS Sierra condivide tali modifiche con iOS 10, per altre informazioni vedere la guida ai miglioramenti per la [sicurezza e la privacy](~/ios/app-fundamentals/security-privacy.md) di iOS 10.

<a name="Smart-Card-Driver-Extension-Support" />

### <a name="smart-card-driver-extension-support"></a>Supporto dell'estensione driver Smart Card

Con macOS Sierra, l'app può creare driver di smart card basati su `NSExtension` che consentono l'accesso in sola lettura al contenuto da determinati tipi di smart card. Queste informazioni vengono quindi presentate all'interno del keychain di sistema (sostituendo il metodo di architettura Common Data Security deprecato).

Per ulteriori informazioni, vedere la Guida di [riferimento a CryptoTokenKit Framework](https://developer.apple.com/reference/cryptotokenkit)di Apple.

<a name="Unified-Logging" />

### <a name="unified-logging"></a>Registrazione unificata

La registrazione unificata offre all'app un'unica API per la messaggistica efficiente in tutti i livelli del sistema. Con la registrazione unificata, l'app dispone di un controllo granulare su più livelli di registrazione che includono controlli sulla privacy e rilevamento delle attività per semplificare il debug. 

La registrazione fornisce la correlazione automatica dei messaggi quando vengono utilizzati insieme rilevamento e registrazione delle attività.

macOS Sierra include una nuova app console (in applicazioni/utilità) in grado di visualizzare i dati di log da più origini, inclusi i dispositivi connessi. Supporta inoltre le ricerche in formato token e salvate e visualizza le connessioni tra messaggi correlati tra più processi.

Inoltre, i messaggi di log possono essere visualizzati e gestiti tramite gli strumenti da riga di comando.

Per altre informazioni, vedere la Guida di [riferimento](https://developer.apple.com/documentation/os/logging)per la registrazione di Apple.

<a name="Wide-Color" />

### <a name="wide-color"></a>Wide Color

macOS Sierra estende il supporto per i formati di pixel estesi e gli spazi dei colori a gamma ampia in tutto il sistema, inclusi i Framework come la grafica principale, le immagini di base, i metal e AVFoundation. Il supporto per i dispositivi con schermi Wide Color è ulteriormente facilitato fornendo questo comportamento nell'intero stack di grafica.

Inoltre, `AppKit` è stato modificato per funzionare nel nuovo spazio dei colori di **sRGB** esteso, semplificando la combinazione di colori in gamme di colori estesi senza perdite di prestazioni significative.

Apple offre le seguenti procedure consigliate per l'utilizzo di colori estesi:

- `NSColor` USA ora lo spazio dei colori di sRGB e non blocca più i valori al `0.0` per `1.0` intervallo. Se l'app si basa sul comportamento del morsetto precedente, sarà necessario modificarlo per macOS Sierra.
- Quando si usa un'API di basso livello, ad esempio grafica di base o metal, per fornire l'elaborazione di immagini, l'app deve usare uno spazio dei colori con intervallo esteso e un formato pixel che supporti i valori a virgola mobile a 16 bit. Se necessario, l'app dovrà bloccare manualmente i valori dei componenti dei colori.
- I componenti grafici di base, le immagini di base e gli shader con prestazioni Metal forniscono tutti i nuovi metodi per la conversione tra i due spazi dei colori.

Per saperne di più, vedere la Guida [introduttiva a wide color](~/ios/platform/wide-color.md) .

<a name="Additional-Framework-Changes" />

## <a name="additional-framework-changes"></a>Modifiche aggiuntive del Framework

Oltre alle principali modifiche e aggiunte al Framework elencate in precedenza, Apple ha apportato molte modifiche di Framework secondarie aggiuntive in macOS Sierra.

Per ulteriori informazioni, vedere la Guida alle [modifiche aggiuntive del Framework](~/mac/platform/introduction-to-macos-sierra/additional-framework-changes.md) .

<a name="Deprecated-APIs" />

## <a name="deprecated-apis"></a>API deprecate

Le API seguenti sono state deprecate in macOS Sierra:

- Il file System standard HFS non è più supportato.

Per un elenco completo di deprecazioni e modifiche, vedere la documentazione relativa alle [differenze dell'API MacOS v 10.12](https://developer.apple.com/library/archive/releasenotes/General/APIDiffsMacOS10_12/index.html) di Apple.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di Mac](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Mac)
- [Novità di macOS 10,12](https://developer.apple.com/library/prerelease/content/releasenotes/MacOSX/WhatsNewInOSX/Articles/OSXv10.html#//apple_ref/doc/uid/TP40017145-SW1)
