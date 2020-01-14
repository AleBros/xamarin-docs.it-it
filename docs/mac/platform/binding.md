---
title: Binding di librerie Mac per Xamarin.Mac
description: Questo documento include collegamenti a guide che descrivono come usare le associazioni Objective-C in un'applicazione Xamarin.Mac, tra cui Objective Sharpie e codice di esempio.
ms.prod: xamarin
ms.assetid: 521707CD-79D3-488A-84CB-A37EBF93AC94
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 01/13/2017
ms.openlocfilehash: c478437a9c84475e8c31484523db16336f8808e6
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029924"
---
# <a name="binding-mac-libraries-for-xamarinmac"></a>Binding di librerie Mac per Xamarin.Mac

Seguire questi collegamenti per informazioni sull'associazione di librerie Objective-C in Xamarin.Mac:

- [**Panoramica**](~/cross-platform/macios/binding/overview.md) -
  descrive il funzionamento dell'associazione.
- [**Binding di librerie Objective-c**](~/cross-platform/macios/binding/objective-c-libraries.md) -
  istruzioni su come associare le librerie Objective-c per l'uso nei progetti Xamarin.
- [**Guida di riferimento**](~/cross-platform/macios/binding/binding-types-reference.md) per la definizione dei tipi -
  descrive tutti gli attributi disponibili per l'associazione tra autori e il processo di generazione del binding.

## <a name="objective-sharpiecross-platformmaciosbindingobjective-sharpieindexmd"></a>[Objective Sharpie](~/cross-platform/macios/binding/objective-sharpie/index.md)

Objective Sharpie è uno strumento da riga di comando che consente di avviare il primo passaggio di un'associazione.
Funziona analizzando i file di intestazione di una libreria nativa per eseguire il mapping dell'API pubblica nella [definizione di binding](~/cross-platform/macios/binding/binding-types-reference.md) (un processo che altrimenti viene eseguito manualmente). L'obiettivo Sharpie non crea un'associazione autonomamente, ma può essere utile per iniziare.

## <a name="examples"></a>Esempi

Vedere l' [esempio di XMBindingExample Mac](https://github.com/xamarin/mac-samples/tree/master/XMBindingExample) per informazioni su come creare un'associazione Mac usando i progetti di associazione.

## <a name="related-links"></a>Collegamenti correlati

- [Binding di Objective-C](~/cross-platform/macios/binding/index.md)
- [Associazione di librerie iOS](~/ios/platform/binding-objective-c/index.md)
