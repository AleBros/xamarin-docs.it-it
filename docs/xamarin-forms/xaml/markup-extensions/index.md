---
title: Estensioni di Markup XAML
description: Estendere l'intervallo di origini da cui XAML gli attributi sono impostati
ms.prod: xamarin
ms.assetid: EB06C8B7-3FD5-47B7-A09C-A13063BD110F
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 01/05/2018
ms.openlocfilehash: b81bc4b31edd1d8b8f5f43f97885c38e889dd32c
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="xaml-markup-extensions"></a>Estensioni di Markup XAML

Le estensioni di markup XAML prolungare la potenza e flessibilità di XAML, consentendo di attributi dell'elemento da impostare da origini diverse stringhe di testo letterale.

Ad esempio, in genere si imposta la `Color` proprietà `BoxView` come segue:

```xaml
<BoxView Color="Blue" />
```

In alternativa, è possibile impostare un valore di colore RGB esadecimale:

```xaml
<BoxView Color="#FF0080" />
```

In entrambi i casi, imposta la stringa di testo di `Color` attributo viene convertito in un `Color` del valore di [ `ColorTypeConverter` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ColorTypeConverter/) classe.

Potrebbe invece essere impostare il `Color` attributo da un valore archiviato in un dizionario risorse, o dal valore di una proprietà statica di una classe che è stato creato o da una proprietà di tipo `Color` di un altro elemento nella pagina o costruito da separare i valori di tonalità, saturazione e luminosità.

Tutte queste opzioni sono possibili utilizzando le estensioni di markup XAML. Ma non consentono la frase "estensioni di markup" spaventare: estensioni di markup XAML sono *non* estensioni XML. Anche con le estensioni di markup XAML, XAML è sempre XML valido. 

Un'estensione di markup è semplicemente un modo diverso per esprimere un attributo di un elemento. Le estensioni di markup XAML vengono in genere identificate da un'impostazione di attributo che è racchiuso tra parentesi graffe:

```xaml
<BoxView Color="{StaticResource themeColor}" />
```

Qualsiasi impostazione dell'attributo tra parentesi graffe *sempre* un'estensione di markup XAML. Tuttavia, come si vedrà, le estensioni di markup XAML possono anche riferimento senza utilizzare le parentesi graffe.

In questo articolo è suddivisa in due parti:

## <a name="consuming-xaml-markup-extensionsconsumingmd"></a>[Utilizzo di estensioni di markup XAML](consuming.md)  

Utilizzare le estensioni di markup XAML definite in xamarin. Forms.

## <a name="creating-xaml-markup-extensionscreatingmd"></a>[Creazione di estensioni di markup XAML](creating.md) 

Scrivere la propria estensioni di markup XAML personalizzate.



## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup (esempio)](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)
- [Capitolo di estensioni di markup XAML di libro xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Dizionari di risorse](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Stili dinamici](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Data binding](~/xamarin-forms/app-fundamentals/data-binding/index.md)
