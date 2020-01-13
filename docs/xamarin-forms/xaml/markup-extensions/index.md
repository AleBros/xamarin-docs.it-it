---
title: Estensioni di Markup XAML
description: L'articolo illustra come usare le estensioni di markup XAML di Xamarin.Forms per estendere la potenza e flessibilità di XAML, consentendo di attributi dell'elemento da impostare da origini diverse dalle stringhe di testo letterale.
ms.prod: xamarin
ms.assetid: EB06C8B7-3FD5-47B7-A09C-A13063BD110F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 40a50ecf93c8b77577e8e74df2e134b0ce3f80b6
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70767548"
---
# <a name="xaml-markup-extensions"></a>Estensioni di Markup XAML

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)

Le estensioni di markup XAML consentono ad ampliare la potenza e flessibilità di XAML, consentendo di attributi dell'elemento da impostare da origini diverse dalle stringhe di testo letterale.

Ad esempio, in genere si imposta la `Color` proprietà di `BoxView` simile al seguente:

```xaml
<BoxView Color="Blue" />
```

In alternativa, è possibile impostarlo su un valore di colore RGB esadecimale:

```xaml
<BoxView Color="#FF0080" />
```

In entrambi i casi, la stringa di testo impostato sul `Color` attributo viene convertito in un `Color` del valore di [ `ColorTypeConverter` ](xref:Xamarin.Forms.ColorTypeConverter) classe.

È preferibile invece impostare il `Color` attributo da un valore archiviato in un dizionario risorse, o dal valore di una proprietà statica di una classe che è stata creata oppure da una proprietà di tipo `Color` di un altro elemento nella pagina o costruiti da separare i valori di tonalità, saturazione e luminosità.

Tutte queste opzioni sono possibili utilizzando le estensioni di markup XAML. Ma non lasciare la frase "estensioni di markup": Le estensioni di markup XAML *non* sono estensioni per XML. Anche con le estensioni di markup XAML, XAML è sempre XML validi.

Un'estensione di markup è semplicemente un modo per esprimere un attributo di un elemento diverso. Le estensioni di markup XAML sono in genere identificabili da un'impostazione dell'attributo che è racchiuso tra parentesi graffe:

```xaml
<BoxView Color="{StaticResource themeColor}" />
```

Qualsiasi impostazione dell'attributo tra parentesi graffe *sempre* un'estensione di markup XAML. Tuttavia, come si vedrà, le estensioni di markup XAML possono anche essere richiamate senza l'uso delle parentesi graffe.

Questo articolo è diviso in due parti:

## <a name="consuming-xaml-markup-extensionsconsumingmd"></a>[Utilizzo di estensioni di markup XAML](consuming.md)  

Usare le estensioni di markup XAML definite in Xamarin.Forms.

## <a name="creating-xaml-markup-extensionscreatingmd"></a>[Creazione di estensioni di markup XAML](creating.md)

Scrittura delle estensioni di markup XAML personalizzate.

## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xaml-markupextensions)
- [Capitolo di estensioni di markup XAML dal libro di Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Dizionari di risorse](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Stili dinamici](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Data binding](~/xamarin-forms/app-fundamentals/data-binding/index.md)
