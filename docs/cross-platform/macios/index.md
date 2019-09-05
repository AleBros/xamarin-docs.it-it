---
title: Piattaforma Apple (iOS e Mac)
description: 'Questo documento descrive vari argomenti correlati allo sviluppo di Novell. iOS e Novell. Mac: condivisione del codice, API unificata, binding di librerie Objective-C, riferimenti nativi, tipi nativi e altro ancora.'
ms.prod: xamarin
ms.assetid: 67246203-D78E-4DCC-9E55-7D3D93968E54
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: b6ac960770320ed100e8b082cabf8240efed070b
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290841"
---
# <a name="apple-platform-ios-and-mac"></a>Piattaforma Apple (iOS e Mac)

## <a name="code-sharing"></a>Condivisione del codice

Per gli elementi del codice senza elementi dell'interfaccia utente, il modo migliore per condividere il codice tra iOS e Mac è ancora l'uso di [librerie di classi](~/cross-platform/app-fundamentals/pcl.md)portabili.

Per il codice che deve eseguire alcune operazioni dell'interfaccia utente e che tuttavia si vuole condividere, è necessario usare [progetti condivisi](~/cross-platform/app-fundamentals/shared-projects.md) che consentono di inserire il codice da condividere in un singolo progetto e di compilarlo con Mac e iOS quando viene fatto riferimento.

## <a name="unified-apiunifiedindexmd"></a>[API unificata](unified/index.md)

Il API unificata per i progetti iOS e Mac usa gli stessi spazi dei nomi per i Framework in modo che lo stesso file di codice possa essere usato in entrambe le piattaforme, per una condivisione di codice uniforme. Consente inoltre le compilazioni di bit 32 e 64. Il API unificata è stato il modello predefinito dal primo 2015 ed è consigliato per tutti i nuovi progetti. è possibile inviare *solo* i progetti API unificata all'App Store.

### <a name="classic-apis"></a>API classiche

> [!NOTE]
> **Deprecazione del profilo classico:** Man mano che vengono aggiunte nuove piattaforme in Novell. iOS, viene avviata la deprecazione graduale delle funzionalità del profilo classico (MonoTouch. dll). Ad esempio, l'opzione non-NRC (New-ref-Count) è stata rimossa. NRC è sempre stato abilitato per tutte le applicazioni unificate, ovvero non è mai stata un'opzione, ma non ha problemi noti. Nelle versioni future viene rimossa l'opzione di utilizzo di Boehm come Garbage Collector. Questa era anche un'opzione non disponibile per le applicazioni unificate. La rimozione completa del supporto classico è prevista per il calo 2016 con la versione di Novell. iOS 10,0.

Le API originali (non unificate) Novell. iOS e Novell. Mac hanno reso più difficile la condivisione del codice perché i Framework `MonoTouch.` nativi contengono prefissi di spazio dei nomi o. `MonoMac.`  Sono stati forniti alcuni spazi dei nomi vuoti che consentono agli sviluppatori di condividere `using` il codice aggiungendo istruzioni che fanno riferimento a spazi dei nomi MonoMac e MonoTouch nello stesso file, ma si tratta di un po' brutto. Il API classica deve continuare a essere usato solo nelle app legacy distribuite internamente (è consigliabile eseguire l'aggiornamento al API unificata).


### <a name="updating-from-classic-to-the-unified-api"></a>Aggiornamento dal modello classico al API unificata

Sono disponibili istruzioni dettagliate per l'aggiornamento di qualsiasi applicazione dal modello classico al API unificata.

## <a name="binding-objective-c-librariesbindingindexmd"></a>[Binding di librerie Objective-C](binding/index.md)

Novell consente di importare librerie native nelle app con binding. Questa sezione viene illustrato:

- funzionamento delle associazioni,
- come compilare manualmente un progetto di binding che consente di portare il codice Objective-C in Novell e
- come usare lo strumento **Objective Sharpie** per semplificare l'automazione del processo.

## <a name="native-referencesnative-referencesmd"></a>[Riferimenti nativi](native-references.md)

## <a name="macios-native-typesnativetypesmd"></a>[Tipi nativi Mac/iOS](nativetypes.md)

Per supportare il codice di bit 32 e 64 in modo C# trasparente F#da e, vengono introdotti nuovi tipi di dati.   Per informazioni su questi elementi, vedere qui.

## <a name="building-32-and-64-bit-apps32-and-64indexmd"></a>[Compilazione di app 32 e 64 bit](32-and-64/index.md)

Cosa è necessario sapere per supportare le applicazioni 32 e 64 bit.

## <a name="working-with-native-types-in-cross-platform-appsnative-types-cross-platformmd"></a>[Utilizzo di tipi nativi nelle app multipiattaforma](native-types-cross-platform.md)

Questo articolo illustra l'uso dei nuovi tipi nativi iOS API unificata`nint`( `nuint`, `nfloat`,) in un'applicazione multipiattaforma in cui il codice viene condiviso con dispositivi non iOS, ad esempio Android o Windows Phone sistemi operativi.
Fornisce informazioni dettagliate su quando usare i tipi nativi e fornisce diverse soluzioni possibili ai casi in cui il nuovo tipo deve essere usato con il codice multipiattaforma.

## <a name="httpclient-stack-and-ssltls-implementation-selectorhttp-stackmd"></a>[Stack HttpClient e selettore dell'implementazione di SSL/TLS](http-stack.md)

Il nuovo selettore dello stack HttpClient controlla quale implementazione di HttpClient usare nell'app Novell. iOS, Novell. tvOS e Novell. Mac. È ora possibile passare a un'implementazione che usa i trasporti nativi di iOS, tvOS o OS X (`NSUrlSession` o `CFNetwork` a seconda del sistema operativo).

SSL (Secure Socket Layer) e il suo successore, TLS (Transport Layer Security), forniscono il supporto per HTTP e altre connessioni `System.Net.Security.SslStream`di rete tramite. La nuova opzione di compilazione di implementazione SSL/TLS passa tra lo stack TLS di mono e uno basato sullo stack TLS di Apple presente in Mac e iOS.
