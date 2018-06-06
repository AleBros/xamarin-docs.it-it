---
title: Limitazioni di incorporamento .NET
description: Questo documento descrive le limitazioni di incorporamento .NET, lo strumento che consente di usare codice .NET in altri linguaggi di programmazione.
ms.prod: xamarin
ms.assetid: EBBBB886-1CEF-4DF4-AFDD-CA96049F878E
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: fdd3ac4cd57ac7f79f9071d62e758625b30f05dd
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34794124"
---
# <a name="net-embedding-limitations"></a>Limitazioni di incorporamento .NET

Questo documento vengono descritte le limitazioni di incorporamento, .NET e, se possibile, vengono illustrate soluzioni alternative per tali.

## <a name="general"></a>Generale

### <a name="use-more-than-one-embedded-library-in-a-project"></a>Utilizzare più di una libreria incorporata in un progetto

Non è possibile avere due runtime Mono coesistente all'interno della stessa applicazione. Ciò significa che non è possibile utilizzare due librerie diverse generati durante l'incorporamento .NET all'interno della stessa applicazione.

**Soluzione alternativa:** è possibile utilizzare il generatore di creare una singola libreria che include diversi assembly (per progetti diversi).

### <a name="subclassing"></a>Creazione di sottoclassi

Durante l'incorporamento .NET semplifica l'integrazione del runtime Mono all'interno delle applicazioni che espongono un set di API pronti da usare per la piattaforma e linguaggio di destinazione.

Ma non è un'integrazione bidirezionale, ad esempio, non è possibile sottoclasse un tipo gestito e prevede che il codice gestito per richiamare all'interno del codice nativo, poiché il codice gestito è a conoscenza di questo coesistenza.

A seconda delle esigenze, è possibile a parti di soluzione di questa limitazione, ad esempio

* il codice gestito può p/invoke in codice nativo. È necessario personalizzare il codice gestito per consentire la personalizzazione dal codice nativo;

* utilizzo dei prodotti, ad esempio xamarin. IOS ed esporre una libreria gestita che permetterebbe che Objective-C (in questo caso) per creare una sottoclasse alcuni gestiti NSObject sottoclassi.

## <a name="objective-c-generated-code"></a>Codice generato Objective-C

### <a name="nullability"></a>Supporto di valori null

Non è senza metadati di .NET che indicano se un riferimento null è accettabile o meno per un'API. La maggior parte delle API genererà `ArgumentNullException` se non è possibile gestire un `null` argomento. Questo rappresenta un problema come Objective-C la gestione delle eccezioni è preferibile evitare.

Poiché è possibile generare annotazioni accurate ammissione di valori null nei file di intestazione e si desidera ridurre al minimo le eccezioni gestite è per impostazione predefinita gli argomenti non null (`NS_ASSUME_NONNULL_BEGIN`) e aggiungere alcune specifiche, quando la precisione è possibile, le annotazioni di supporto di valori null.

### <a name="bitcode-ios"></a>Bitcode (iOS)

Incorporamento di .NET non supporta attualmente bitcode in iOS, è abilitata per alcuni modelli di progetto Xcode. Ciò avrà deve essere disabilitata al framework di collegamento generato correttamente.

![Opzione Bitcode](images/ios-bitcode-option.png)
