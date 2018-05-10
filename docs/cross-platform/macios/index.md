---
title: Piattaforma di Apple (iOS e Mac)
description: In questa sezione sono illustrate le strategie per condividere il codice tra i progetti di xamarin e Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 67246203-D78E-4DCC-9E55-7D3D93968E54
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 3c01ff4af699dd0374729b638470d1ef34aa7022
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="apple-platform-ios-and-mac"></a>Piattaforma di Apple (iOS e Mac)

_In questa sezione sono illustrate le strategie per condividere il codice tra i progetti di xamarin e Xamarin.Mac._

## <a name="code-sharing"></a>La condivisione del codice

Per gli elementi del codice che non dispongono di alcun elemento dell'interfaccia utente il modo migliore per condividere codice tra iOS e Mac è comunque l'uso di [librerie di classi portabili](~/cross-platform/app-fundamentals/pcl.md).

Per il codice che è necessario eseguire alcune operazioni di interfaccia utente e ancora, con cui si desidera condividere, è consigliabile utilizzare [progetti condivisi](~/cross-platform/app-fundamentals/shared-projects.md) che consentono di inserire il codice per condividere un singolo progetto e compilati con Mac e iOS quando si fa riferimento.

##  <a name="unified-apiunifiedindexmd"></a>[API unificata](unified/index.md)

L'API unificata per iOS e progetti Mac utilizza gli stessi spazi dei nomi per Framework affinché possa essere utilizzato lo stesso file di codice su entrambe le piattaforme, per condividere codice trasparente. Consente inoltre le compilazioni bit sia a 32 e 64. Unified API sin 2015 anticipata ha ricevuto il modello come predefinito ed è consigliata per tutti i nuovi progetti - *solo* progetti Unified API possono essere inviati all'App Store.

### <a name="classic-apis"></a>API classiche

> [!NOTE]
> **Deprecazione di profilo classico:** con l'aggiunta di nuove piattaforme in xamarin. IOS, si inizierà gradualmente deprecare funzionalità dal profilo classico (monotouch.dll). Ad esempio, l'opzione non NRC (nuovo-ref-count) è stato rimosso. NRC è sempre stato abilitato per unificata tutte le applicazioni (ad esempio NRC non è stato mai un'opzione) e sono presenti problemi noti. Nelle versioni future rimuoverà la possibilità di usare Boehm come il garbage collector. Questo è anche un'opzione non disponibile alle applicazioni unificate. La rimozione completa del supporto classico è pianificata per autunno 2016 con la versione di xamarin. IOS 10.0.

L'originale (non-unificato) xamarin. IOS e Xamarin.Mac APIs ha la condivisione del codice più difficile perché dispone Framework nativo `MonoTouch.` o `MonoMac.` i prefissi dello spazio dei nomi.  È stata fornita alcuni spazi dei nomi vuoto che consente agli sviluppatori di condividere il codice aggiungendo `using` istruzioni che fanno riferimento a spazi dei nomi MonoMac sia MonoTouch su nello stesso file, ma è stato un po' inutile. L'API classica solo continueranno a essere utilizzata nelle applicazioni legacy che vengono distribuite internamente (aggiornamento per l'API unificata è consigliato).


### <a name="updating-from-classic-to-the-unified-api"></a>L'aggiornamento dalla versione classica all'API unificata

Sono disponibili istruzioni dettagliate per l'aggiornamento di qualsiasi applicazione dalla classica all'API unificata.

## <a name="binding-objective-c-librariesbindingindexmd"></a>[Binding di librerie Objective-C](binding/index.md)

Xamarin consente di rendere le app librerie native con associazioni. Questa sezione viene illustrato:

- funzionano delle associazioni,
- come creare manualmente un progetto di associazione che consente di inserire codice Objective-C, Xamarin e
- come utilizzare il nostro **Sharpie obiettivo** strumento per automatizzare il processo.

## <a name="native-referencesnative-referencesmd"></a>[Riferimenti nativi](native-references.md)



##  <a name="macios-native-typesnativetypesmd"></a>[Tipi nativi Mac/iOS](nativetypes.md)

Per supportare 32 e 64 bit di codice in modo trasparente da c# e F #, Microsoft sta introducendo nuovi tipi di dati.   Informazioni su di essi qui.

##  <a name="building-32-and-64-bit-apps32-and-64indexmd"></a>[Compilazione di applicazioni a 32 e 64 bit](32-and-64/index.md)

Cosa è necessario conoscere per supportare applicazioni a 32 e 64 bit.

## <a name="working-with-native-types-in-cross-platform-appsnative-types-cross-platformmd"></a>[Utilizzo di tipi nativi nelle app multipiattaforma](native-types-cross-platform.md)

In questo articolo viene illustrato l'utilizzo di nuovi tipi Unified API Native iOS (`nint`, `nuint`, `nfloat`) in un'applicazione multipiattaforma in cui il codice è condiviso con i dispositivi non iOS, ad esempio Android o sistemi operativi Windows Phone.
Fornisce informazioni su quando usare i tipi nativi e offre diverse soluzioni possibili per i casi in cui il nuovo tipo deve essere utilizzato con codice multipiattaforma.


## <a name="httpclient-stack-and-ssltls-implementation-selectorhttp-stackmd"></a>[Stack HttpClient e selettore dell'implementazione di SSL/TLS](http-stack.md)

Il nuovo selettore Stack HttpClient controlla quale implementazione HttpClient da usare in app xamarin, Xamarin.tvOS e Xamarin.Mac. È ora possibile passare a un'implementazione che utilizza di iOS, del tvOS o trasporti native OS x (`NSUrlSession` o `CFNetwork` a seconda del sistema operativo).

SSL (Secure Socket Layer) e il corrispondente successore, TLS (Transport Layer Security), fornire il supporto per HTTP e le altre connessioni di rete tramite `System.Net.Security.SslStream`. La nuova opzione di compilazione implementazione di SSL/TLS passa tra stack TLS del Mono e una basata su stack TLS Apple presente nel Mac e iOS.
