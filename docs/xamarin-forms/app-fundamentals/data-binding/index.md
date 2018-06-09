---
title: Associazione dati di xamarin. Forms
description: Associazione dati è la tecnica di collegamento tra le proprietà di due oggetti in modo che le modifiche in una proprietà vengono applicate automaticamente l'altra proprietà. Associazione dati è parte integrante dell'architettura dell'applicazione Model-View-ViewModel (MVVM).
ms.prod: xamarin
ms.assetid: 938E85C8-521D-43B9-92CB-D591A06D98A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: a5ea5dcb5b108da52634f131fd36a91ba82f7da4
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/08/2018
ms.locfileid: "35240353"
---
# <a name="xamarinforms-data-binding"></a>Associazione dati di xamarin. Forms

_Associazione dati è la tecnica di collegamento tra le proprietà di due oggetti in modo che le modifiche in una proprietà vengono applicate automaticamente l'altra proprietà. Associazione dati è parte integrante dell'architettura dell'applicazione Model-View-ViewModel (MVVM)._

## <a name="the-data-linking-problem"></a>Il problema di collegamento dati

Un'applicazione di xamarin. Forms è costituita da una o più pagine, ognuna delle quali contiene in genere più oggetti dell'interfaccia utente chiamati *viste*. Una delle attività principale del programma è mantenere queste viste sincronizzate e di tenere traccia di diversi valori o le selezioni che rappresentano. Spesso le viste rappresentano i valori da un'origine dati sottostante e l'utente modifica queste viste per modificare i dati. Quando si modifica la visualizzazione, i dati sottostanti devono riflettere la modifica, e in modo analogo, quando i dati sottostanti vengono modificati, tale modifica deve essere riflessa nella visualizzazione.

Per gestire correttamente il processo, il programma deve essere notificato delle modifiche in queste viste o i dati sottostanti. La soluzione comune consiste nel definire gli eventi che indicano quando si verifica una modifica. Quindi è possibile installare un gestore eventi che riceve una notifica di queste modifiche. Il servizio risponde al trasferimento dei dati da un oggetto a un altro. Tuttavia, quando sono presenti numerose visualizzazioni, deve essere presente anche molti gestori di eventi e una grande quantità di codice ottiene coinvolti.

## <a name="the-data-binding-solution"></a>La soluzione di associazione dati

Associazione dati consente di automatizzare questo processo ed esegue il rendering i gestori eventi non necessari. (Gli eventi sono ancora necessari, tuttavia, perché li utilizza l'infrastruttura di associazione di dati). Le associazioni di dati possono essere implementate nel codice o in XAML, ma sono molto più comuni in XAML in cui contribuiscono a ridurre le dimensioni del file code-behind. Sostituendo il codice procedurale nei gestori eventi con markup o codice dichiarativo, l'applicazione è semplificato ed è stato chiarito.

Uno dei due oggetti coinvolti in un'associazione di dati è quasi sempre un elemento che deriva da `View` e fa parte dell'interfaccia visiva di una pagina. L'altro oggetto è:

- Un altro `View` derivati, in genere nella stessa pagina.
- Un oggetto in un file di codice.

Nei programmi di dimostrazione, ad esempio quelli di [ **DataBindingDemos** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) di esempio, le associazioni di dati tra due `View` derivati vengono spesso visualizzati ai fini di chiarezza e la semplicità. Tuttavia gli stessi principi possono essere applicati alle associazioni dati tra un `View` e altri oggetti. Quando un'applicazione viene compilata utilizzando l'architettura Model-View-ViewModel (MVVM), la classe con dati sottostanti è spesso definita un ViewModel.

Le associazioni dati sono descritte in serie di articoli seguenti:

## <a name="basic-bindingsbasic-bindingsmd"></a>[Binding di base](basic-bindings.md)

Informazioni sulla differenza tra il data binding origine e di destinazione e vedere simple data binding nel codice e XAML.

## <a name="binding-modebinding-modemd"></a>[Modalità di binding](binding-mode.md)

Individuazione come la modalità di associazione può controllare il flusso di dati tra i due oggetti.

## <a name="string-formattingstring-formattingmd"></a>[Formattazione delle stringhe](string-formatting.md)

Usare un'associazione dati per formattare e visualizzare gli oggetti come stringhe.

## <a name="binding-pathbinding-pathmd"></a>[Percorso di binding](binding-path.md)

Approfondimento ulteriormente il `Path` proprietà dell'associazione dati per accedere a sottoproprietà e i membri della raccolta.

## <a name="binding-value-convertersconvertersmd"></a>[Convertitori di valori per i binding](converters.md)

Utilizzare i convertitori di valori di binding per modificare i valori all'interno dell'associazione dati.

## <a name="the-command-interfacecommandingmd"></a>[Interfaccia di comando](commanding.md)

Implementare il `Command` proprietà con il data binding.



## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capitolo di associazione di dati dalla Rubrica di xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
- [Estensioni di markup XAML](~/xamarin-forms/xaml/markup-extensions/index.md)
