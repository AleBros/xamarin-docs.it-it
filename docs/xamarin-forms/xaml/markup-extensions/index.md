---
title: Estensioni di Markup XAML
description: L'articolo illustra come usare le estensioni di markup XAML di xamarin. Forms per estendere la potenza e flessibilità di XAML, consentendo di attributi dell'elemento da impostare da origini diverse dalle stringhe di testo letterale.
ms.prod: xamarin
ms.assetid: EB06C8B7-3FD5-47B7-A09C-A13063BD110F
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 01/05/2018
ms.openlocfilehash: d507ff3c74de6bb4ea36c1a7b7dc2cd5dd60823b
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38996740"
---
# <a name="xaml-markup-extensions"></a>Estensioni di Markup XAML

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

Tutte queste opzioni sono possibili utilizzando le estensioni di markup XAML. Ma non lasciare la frase "estensioni di markup" spaventare: le estensioni di markup XAML vengono *non* XML estensioni. Anche con le estensioni di markup XAML, XAML è sempre XML validi.

Un'estensione di markup è semplicemente un modo per esprimere un attributo di un elemento diverso. Le estensioni di markup XAML sono in genere identificabili da un'impostazione dell'attributo che è racchiuso tra parentesi graffe:

```xaml
<BoxView Color="{StaticResource themeColor}" />
```

Qualsiasi impostazione dell'attributo tra parentesi graffe *sempre* un'estensione di markup XAML. Tuttavia, come si vedrà, le estensioni di markup XAML possono anche essere richiamate senza l'uso delle parentesi graffe.

Questo articolo è diviso in due parti:

## <a name="consuming-xaml-markup-extensionsconsumingmd"></a>[Utilizzo di estensioni di markup XAML](consuming.md)  

Usare le estensioni di markup XAML definite in xamarin. Forms.

## <a name="creating-xaml-markup-extensionscreatingmd"></a>[Creazione di estensioni di markup XAML](creating.md)

Scrittura delle estensioni di markup XAML personalizzate.



## <a name="related-links"></a>Collegamenti correlati

- [Estensioni di markup (esempio)](https://developer.xamarin.com/samples/xamarin-forms/XAML/MarkupExtensions/)
- [Capitolo di estensioni di markup XAML dal libro di xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md)
- [Dizionari di risorse](~/xamarin-forms/xaml/resource-dictionaries.md)
- [Stili dinamici](~/xamarin-forms/user-interface/styles/dynamic.md)
- [Data binding](~/xamarin-forms/app-fundamentals/data-binding/index.md)
