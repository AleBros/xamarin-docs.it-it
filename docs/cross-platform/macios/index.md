---
title: Piattaforma Apple (iOS e Mac)
description: "Questo documento descrive vari argomenti correlati allo sviluppo di xamarin. IOS e xamarin. Mac: codice di condivisione, l'API unificata, binding Objective-C librerie, i riferimenti nativi, i tipi nativi e altro ancora."
ms.prod: xamarin
ms.assetid: 67246203-D78E-4DCC-9E55-7D3D93968E54
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: b40758fa562e57415cd3c0818763ef0a7ce5dcca
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61199767"
---
# <a name="apple-platform-ios-and-mac"></a>Piattaforma Apple (iOS e Mac)

## <a name="code-sharing"></a>Condivisione del codice

Per gli elementi del codice non con alcun elemento dell'interfaccia utente il modo migliore per condividere codice tra iOS e Mac viene comunque l'uso di [librerie di classi portabili](~/cross-platform/app-fundamentals/pcl.md).

Per il codice che è necessario eseguire alcune operazioni dell'interfaccia utente e ancora, si desidera condividere, è consigliabile usare [progetti condivisi](~/cross-platform/app-fundamentals/shared-projects.md) che consentono di inserire il codice per condividere in un unico progetto che verrà compilato con Mac e iOS quando viene fatto riferimento.

##  <a name="unified-apiunifiedindexmd"></a>[API unificata](unified/index.md)

L'API unificata per progetti iOS e Mac Usa gli stessi spazi dei nomi per i Framework in modo che lo stesso file di codice può essere usato su entrambe le piattaforme, per facile condivisione del codice. Inoltre, consente le compilazioni di bit sia 32 e 64. L'API unificata anticipata 2015 è stato il modello come predefinito ed è consigliato per tutti i nuovi progetti - *solo* progetti API unificata è possono inviare l'App Store.

### <a name="classic-apis"></a>API classica

> [!NOTE]
> **Rimozione del profilo classico:** L'aggiunta di nuove piattaforme in xamarin. IOS è iniziata deprecare gradualmente le funzionalità dal profilo classico (monotouch). Ad esempio, l'opzione non NRC (nuova-ref-count) è stata rimossa. NRC sempre abilitato per applicazioni unificate tutto (vale a dire NRC non era mai un'opzione) e sono presenti problemi noti. Le versioni future rimuoverà la possibilità di usare Boehm come garbage collector. Questo era anche un'opzione mai disponibile per le applicazioni unificate. La rimozione completa di supporto classica è pianificata per autunno 2016 con la versione di xamarin. IOS 10.0.

Xamarin. IOS (non-unificato) originale e le Xamarin.Mac APIs apportate condivisione del codice più difficile Framework nativi risultavano uno `MonoTouch.` o `MonoMac.` prefissi dello spazio dei nomi.  Microsoft fornisce alcuni spazi dei nomi vuoto che consente agli sviluppatori di condividere il codice aggiungendo `using` istruzioni che fanno riferimento a spazi dei nomi sia MonoMac e MonoTouch per lo stesso file, ma questo è stato un po' poco eleganti. L'API classica solo continueranno a essere utilizzata nelle applicazioni legacy che sono distribuite internamente (l'aggiornamento all'API unificata è consigliato).


### <a name="updating-from-classic-to-the-unified-api"></a>L'aggiornamento dalla versione classica per l'API unificata

Sono disponibili istruzioni dettagliate per l'aggiornamento di qualsiasi applicazione dalla distribuzione classica all'API unificata.

## <a name="binding-objective-c-librariesbindingindexmd"></a>[Binding di librerie Objective-C](binding/index.md)

Xamarin ti permette di importare le librerie native nelle tue App con le associazioni. Questa sezione viene illustrato:

- come funzionano le associazioni,
- come creare manualmente un progetto di associazione che consente di trasferire codice Objective-C in Xamarin, e
- come usare nostri **obiettivo Sharpie** degli strumenti per automatizzare il processo.

## <a name="native-referencesnative-referencesmd"></a>[Riferimenti nativi](native-references.md)

##  <a name="macios-native-typesnativetypesmd"></a>[Tipi nativi di iOS/Mac](nativetypes.md)

Per supportare codice a bit 32 e 64 in modo trasparente da C# e F#, viene introdotto nuovi tipi di dati.   Informazioni sulle loro qui.

##  <a name="building-32-and-64-bit-apps32-and-64indexmd"></a>[Compilazione di applicazioni a 32 e 64 bit](32-and-64/index.md)

Quello che devi sapere per supportare applicazioni a 32 e 64 bit.

## <a name="working-with-native-types-in-cross-platform-appsnative-types-cross-platformmd"></a>[Utilizzo di tipi nativi nelle app multipiattaforma](native-types-cross-platform.md)

Questo articolo illustra l'uso di iOS nuovi tipi Unified API nativo (`nint`, `nuint`, `nfloat`) in un'applicazione multipiattaforma in cui il codice è condiviso con i dispositivi non iOS, ad esempio Android o sistemi operativi Windows Phone.
Fornisce informazioni su quando usare i tipi nativi e offre diverse soluzioni possibili per i casi in cui il nuovo tipo deve essere utilizzato con codice multipiattaforma.

## <a name="httpclient-stack-and-ssltls-implementation-selectorhttp-stackmd"></a>[Stack HttpClient e selettore dell'implementazione di SSL/TLS](http-stack.md)

Il nuovo selettore di HttpClient Stack controlla quale implementazione di HttpClient da usare nell'app xamarin. IOS, tvos e xamarin. Mac. È ora possibile passare a un'implementazione che usa di iOS, del tvOS o OS x native trasporti (`NSUrlSession` o `CFNetwork` a seconda del sistema operativo).

SSL (Secure Socket Layer) e il relativo successore, TLS (Transport Layer Security), forniscono il supporto per HTTP e altre connessioni di rete tramite `System.Net.Security.SslStream`. La nuova opzione di compilazione implementazione di SSL/TLS passa tra stack TLS di Mono e uno basato su stack TLS Apple presente nel Mac e iOS.
