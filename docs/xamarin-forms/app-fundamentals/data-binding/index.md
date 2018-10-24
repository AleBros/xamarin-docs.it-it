---
title: Associazione dati di xamarin. Forms
description: Associazione dati è la tecnica di collegamento tra le proprietà di due oggetti in modo che una proprietà vengono automaticamente riflesse in altra proprietà. Associazione dati è parte integrante dell'architettura dell'applicazione Model-View-ViewModel (MVVM).
ms.prod: xamarin
ms.assetid: 938E85C8-521D-43B9-92CB-D591A06D98A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 08/16/2018
ms.openlocfilehash: 60bf0bdc5f1a4472dfd12424c3cc0375d3f34c24
ms.sourcegitcommit: 11c1df7594064e4e141470e092e55cc50c138068
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2018
ms.locfileid: "35240353"
---
# <a name="xamarinforms-data-binding"></a>Associazione dati di xamarin. Forms

_Associazione dati è la tecnica di collegamento tra le proprietà di due oggetti in modo che una proprietà vengono automaticamente riflesse in altra proprietà. Associazione dati è parte integrante dell'architettura dell'applicazione Model-View-ViewModel (MVVM)._

## <a name="the-data-linking-problem"></a>Il problema di collegamento dati

Un'applicazione xamarin. Forms è costituito da una o più pagine, ognuna delle quali contiene in genere più oggetti dell'interfaccia utente denominati *viste*. Una delle attività principale del programma consiste nel mantenere queste viste sincronizzate e per tenere traccia dei diversi valori o selezioni che rappresentano. Spesso le viste rappresentano i valori da un'origine dati sottostante e l'utente modifica queste viste per modificare i dati. Quando si modifica la visualizzazione, i dati sottostanti devono riflettere tale modifica, e in modo analogo, quando i dati sottostanti cambiano, tale modifica deve essere riflessa nella visualizzazione.

Per gestire correttamente il processo, il programma deve essere notificato delle modifiche in queste viste o i dati sottostanti. La soluzione comune consiste nel definire gli eventi che indicano quando si verifica una modifica. Quindi è possibile installare un gestore eventi che riceve una notifica di queste modifiche. Il servizio risponde al trasferimento dei dati da un oggetto a un altro. Tuttavia, quando sono presenti numerose visualizzazioni, deve anche esserci molti gestori degli eventi e una grande quantità di codice viene coinvolto.

## <a name="the-data-binding-solution"></a>La soluzione di Data Binding

Consente di automatizzare questo processo il data binding e rende inutile i gestori di eventi. (Gli eventi sono ancora necessari, tuttavia, poiché li usa l'infrastruttura di data binding). Le associazioni dati possono essere implementate nel codice o in XAML, ma sono molto più comune in XAML in cui contribuiscono a ridurre le dimensioni del file code-behind. Sostituendo il codice procedurale nei gestori eventi con codice dichiarativo o markup, l'applicazione è semplificato e spiegazione.

Uno dei due oggetti coinvolti in un data binding è quasi sempre un elemento che deriva da `View` e fa parte dell'interfaccia visiva di una pagina. L'altro oggetto è:

- Un altro `View` derivativo, in genere nella stessa pagina.
- Un oggetto in un file di codice.

Nei programmi di dimostrazione, ad esempio quelli di [ **DataBindingDemos** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) di esempio, le associazioni di dati tra due `View` derivazioni vengono spesso visualizzati ai fini della semplicità e chiarezza. Tuttavia, gli stessi principi valgono possono essere applicati alle associazioni di dati tra un `View` e altri oggetti. Quando viene compilata un'applicazione usando l'architettura Model-View-ViewModel (MVVM), la classe con dati di sottostante è spesso definita un elemento ViewModel.

Le associazioni dati sono illustrate nelle serie di articoli seguenti:

## <a name="basic-bindingsbasic-bindingsmd"></a>[Binding di base](basic-bindings.md)

Informazioni sulla differenza tra origine e destinazione di associazione dati e visualizzare dati semplici binding nel codice e XAML.

## <a name="binding-modebinding-modemd"></a>[Modalità di binding](binding-mode.md)

Scopri come la modalità di associazione può controllare il flusso di dati tra i due oggetti.

## <a name="string-formattingstring-formattingmd"></a>[Formattazione delle stringhe](string-formatting.md)

Usare un data binding per formattare e visualizzare gli oggetti come stringhe.

## <a name="binding-pathbinding-pathmd"></a>[Percorso di binding](binding-path.md)

Approfondita la `Path` proprietà dell'associazione dati per accedere alle proprietà secondarie e i membri della raccolta.

## <a name="binding-value-convertersconvertersmd"></a>[Convertitori di valori per i binding](converters.md)

Per modificare i valori all'interno dell'associazione di dati, usare convertitori di valori di associazione.

## <a name="binding-fallbacksbinding-fallbacksmd"></a>[Fallback associazione](binding-fallbacks.md)

Rendere più affidabili i data binding mediante la definizione di valori di fallback da usare se il processo di associazione ha esito negativo.

## <a name="the-command-interfacecommandingmd"></a>[Interfaccia di comando](commanding.md)

Implementare il `Command` proprietà con il data binding.

## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capitolo di associazione di dati dal libro di xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
- [Estensioni di markup XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
