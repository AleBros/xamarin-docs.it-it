---
title: Limitazioni di incorporamento di .NET
description: Questo documento descrive le limitazioni di incorporamento, .NET, lo strumento che consente di usare codice .NET in altri linguaggi di programmazione.
ms.prod: xamarin
ms.assetid: EBBBB886-1CEF-4DF4-AFDD-CA96049F878E
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 7a162d632c98b4e412fa1b7b0c0c40ac945ff09f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60945782"
---
# <a name="net-embedding-limitations"></a>Limitazioni di incorporamento di .NET

Questo documento vengono illustrati i limiti di incorporamento, .NET e, se possibile, vengono illustrate soluzioni alternative per tali.

## <a name="general"></a>Generale

### <a name="use-more-than-one-embedded-library-in-a-project"></a>Usare più di una libreria incorporata in un progetto

Non è possibile avere due runtime di Mono condiviso esistente all'interno della stessa applicazione. Ciò significa che non è possibile usare due diverse librerie di .NET incorporamento generati all'interno della stessa applicazione.

**Soluzione alternativa:** È possibile utilizzare il generatore per creare una singola raccolta che include diversi assembly (da progetti diversi).

### <a name="subclassing"></a>Creazione di una sottoclasse

Incorporamento di .NET semplifica l'integrazione del runtime di Mono all'interno delle applicazioni tramite l'esposizione di un set di API pronti da usare per la piattaforma e linguaggio di destinazione.

Tuttavia, questo non è un'integrazione bidirezionale, ad esempio, non è possibile sottoclasse un tipo gestito e prevede che il codice gestito per la richiamata all'interno del codice nativo, poiché il codice gestito è a conoscenza di questo coesistenza.

A seconda delle esigenze, potrebbe essere possibile alle parti di soluzione alternativa di questa limitazione, ad esempio

* il codice gestito può p/invoke in codice nativo. È necessario personalizzare il codice gestito per consentire la personalizzazione dal codice nativo;

* utilizzo dei prodotti, ad esempio xamarin. IOS ed esporre una libreria gestita che consentirebbe di che Objective-C (in questo caso) per creare una sottoclasse alcuni gestiti NSObject sottoclassi.

## <a name="objective-c-generated-code"></a>Codice generato da Objective-C

### <a name="nullability"></a>supporto di valori null

Non sono presenti metadati di .NET che indica se un riferimento null è accettabile o meno per un'API. La maggior parte delle API genererà `ArgumentNullException` se non è possibile gestire un `null` argomento. Ciò risulta problematico come Objective-C gestione delle eccezioni è qualcosa di meglio evitare.

Poiché è possibile generare le annotazioni accurate ammissione di valori null nei file di intestazione e vuole ridurre al minimo le eccezioni gestite sono predefiniti per argomenti non null (`NS_ASSUME_NONNULL_BEGIN`) e aggiungere alcune specifiche, quando la precisione è possibile, le annotazioni di supporto di valori null.

### <a name="bitcode-ios"></a>Bitcode (iOS)

Incorporamento di .NET non supporta attualmente bitcode su iOS, che è abilitato per alcuni modelli di progetto Xcode. Questa dovrà essere disabilitato al framework di collegamento generato correttamente.

![Opzione Bitcode](images/ios-bitcode-option.png)
