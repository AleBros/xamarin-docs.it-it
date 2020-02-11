---
title: Limitazioni dell'incorporamento di .NET
description: In questo documento vengono descritte le limitazioni dell'incorporamento di .NET, lo strumento che consente di utilizzare il codice .NET in altri linguaggi di programmazione.
ms.prod: xamarin
ms.assetid: EBBBB886-1CEF-4DF4-AFDD-CA96049F878E
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: a8b63638861e8d44deb4ea72959d7461190f7713
ms.sourcegitcommit: 6266ef043ae0289f174e901f204f2a280a53c071
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/30/2019
ms.locfileid: "75545806"
---
# <a name="net-embedding-limitations"></a>Limitazioni dell'incorporamento di .NET

In questo documento vengono illustrate le limitazioni dell'incorporamento di .NET e, laddove possibile, le soluzioni alternative.

## <a name="general"></a>Generale

### <a name="use-more-than-one-embedded-library-in-a-project"></a>Usare più di una libreria incorporata in un progetto

Non è possibile avere due runtime di mono coesistenti all'interno della stessa applicazione. Ciò significa che non è possibile usare due diverse librerie generate per l'incorporamento di .NET all'interno della stessa applicazione.

**Soluzione alternativa:** È possibile usare il generatore per creare una singola libreria che includa diversi assembly (da progetti diversi).

### <a name="subclassing"></a>Una sottoclasse

L'incorporamento di .NET semplifica l'integrazione del runtime di mono all'interno delle applicazioni esponendo un set di API pronte per l'uso per il linguaggio e la piattaforma di destinazione.

Tuttavia, non si tratta di un'integrazione bidirezionale, ad esempio non è possibile eseguire la sottoclasse di un tipo gestito e si prevede che il codice gestito richiami all'interno del codice nativo, perché il codice gestito non è a conoscenza di questa coesistenza.

A seconda delle esigenze, potrebbe essere possibile aggirare le parti di questa limitazione, ad esempio

* il codice gestito può p/invoke nel codice nativo. Questa operazione richiede la personalizzazione del codice gestito per consentire la personalizzazione dal codice nativo;

* usare prodotti come Xamarin.iOS ed esporre una libreria gestita che consentirebbe a Objective-C (in questo caso) di sottoporre a sottoclasse alcune sottoclassi NSObject gestite.

## <a name="objective-c-generated-code"></a>Codice generato da Objective-C

### <a name="nullability"></a>Supporto di valori Null

In .NET non sono presenti metadati che indicano se un riferimento null è accettabile o meno per un'API. La maggior parte delle API genererà `ArgumentNullException` se non è in grado di gestire un argomento `null`. Questa situazione è problematica poiché la gestione delle eccezioni in Objective-C è una soluzione migliore.

Poiché non è possibile generare annotazioni accurate di valori null nei file di intestazione e si desidera ridurre al minimo le eccezioni gestite, per impostazione predefinita gli argomenti non null (`NS_ASSUME_NONNULL_BEGIN`) e aggiungono alcune specifiche, quando la precisione è possibile, le annotazioni dei valori null.

### <a name="bitcode-ios"></a>Bitcode (iOS)

Attualmente l'incorporamento .NET non supporta bitcode in iOS, che è abilitato per alcuni modelli di progetto Xcode. Questa operazione dovrà essere disabilitata per collegare correttamente i Framework generati.

* Per iOS, bitcode è facoltativo per inviare app ad AppStore di Apple. Xamarin.iOS non lo supporta per iOS poiché il bitcode generato è "assembly inline". Questo non offre alcun vantaggio sulla piattaforma iOS perché non può essere ottimizzata sul lato server, ma rende i file binari più grandi e tempi di compilazione più lunghi.

* Per tvOS e watchos, bitcode è necessario per l'invio di app ad AppStore di Apple. Xamarin.iOS supporta bitcode in tvOS (come "assembly inline") e watchos (come "LLVM/IR") per soddisfare questo requisito.

* Per macOS, il supporto per bitcode non è attualmente necessario né supportato da Xamarin.Mac.

![Bitcode-opzione](images/ios-bitcode-option.png)
