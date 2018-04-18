---
title: Limitazioni di incorporamento .NET
ms.prod: xamarin
ms.assetid: EBBBB886-1CEF-4DF4-AFDD-CA96049F878E
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: d8995946966020955a1d9378dea631387ed5f4bd
ms.sourcegitcommit: 775a7d1cbf04090eb75d0f822df57b8d8cff0c63
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2018
---
# <a name="net-embedding-limitations"></a>Limitazioni di incorporamento .NET

Questo documento illustra le limitazioni di incorporamento di .NET (Embeddinator 4000) e, se possibile, vengono illustrate soluzioni alternative per tali.

## <a name="general"></a>Generale

### <a name="use-more-than-one-embedded-library-in-a-project"></a>Utilizzare più di una libreria incorporata in un progetto

Non è possibile avere due runtime mono condiviso esistente all'interno della stessa applicazione. Ciò significa che non è possibile utilizzare due librerie diverse embeddinator 4000 generate all'interno della stessa applicazione.

**Soluzione alternativa:** è possibile utilizzare il generatore di creare una singola libreria che include diversi assembly (per progetti diversi).

### <a name="subclassing"></a>Creazione di sottoclassi

Il embeddinator semplifica l'integrazione del runtime mono all'interno delle applicazioni che espongono un set di API pronti da usare per la piattaforma e linguaggio di destinazione.

Ma non è un'integrazione bidirezionale, ad esempio, non è possibile sottoclasse di un tipo gestito e codice gestito di chiamare nuovamente all'interno del codice nativo, poiché il codice gestito è a conoscenza di questo coesistenza prevede.

A seconda delle esigenze, è possibile a parti di soluzione di questa limitazione, ad esempio

* il codice gestito può p/invoke in codice nativo. È necessario personalizzare il codice gestito per consentire la personalizzazione dal codice nativo;

* utilizzo dei prodotti come xamarin ed esporre una libreria gestita che permetterebbero ObjC (in questo caso) per creare una sottoclasse alcune sottoclassi NSObject gestite.


## <a name="objc-generated-code"></a>Codice generato ObjC

### <a name="nullability"></a>Supporto di valori null

Non è metadati, in .NET, che indica se un riferimento null è accettabile o meno per un'API. La maggior parte delle API genererà `ArgumentNullException` se non è possibile gestire un `null` argomento. Questo rappresenta un problema come ObjC la gestione delle eccezioni è meglio evitare.

Poiché è possibile generare annotazioni accurate ammissione di valori null nei file di intestazione e si desidera ridurre al minimo le eccezioni gestite è per impostazione predefinita gli argomenti non null (`NS_ASSUME_NONNULL_BEGIN`) e aggiungere alcune specifiche, quando la precisione è possibile, le annotazioni di supporto di valori null.
