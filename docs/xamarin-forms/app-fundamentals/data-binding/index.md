---
title: Data binding di Xamarin.Forms
description: Il data binding è la tecnica che consente di collegare le proprietà di due oggetti in modo che le modifiche apportate a una proprietà vengano automaticamente riflesse nell'altra proprietà. Il data binding è parte integrante dell'architettura dell'applicazione Model-View-ViewModel (MVVM).
ms.prod: xamarin
ms.assetid: 938E85C8-521D-43B9-92CB-D591A06D98A6
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 03/23/2020
ms.openlocfilehash: 9e3e602eda0d2fa78dd25905a2b6ccf3ce5a744d
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "80247600"
---
# <a name="xamarinforms-data-binding"></a>Data binding di Xamarin.Forms

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos)

_L'associazione dati è la tecnica di collegamento delle proprietà di due oggetti in modo che le modifiche in una proprietà vengano riflesse automaticamente nell'altra proprietà. L'associazione dati è parte integrante dell'architettura dell'applicazione Model-View-ViewModel (MVVM)._

## <a name="the-data-linking-problem"></a>Il problema del collegamento dei dati

Un'applicazione Xamarin.Forms è costituita da una o più pagine, ognuna delle quali in genere contiene più oggetti dell'interfaccia utente denominati *viste*. Una delle attività principali del programma consiste nel mantenere sincronizzate le viste e tenere traccia dei diversi valori o selezioni che rappresentano. Spesso le viste rappresentano i valori di un'origine dati sottostante e l'utente effettua operazioni sulle viste per modificare i dati. Quando una vista viene modificata, i dati sottostanti devono riflettere la modifica e, in modo analogo, quando i dati sottostanti cambiano, la modifica deve essere riflessa nella vista.

Per gestire correttamente il processo, è necessario segnalare al programma le modifiche apportate nelle viste o nei dati sottostanti. La soluzione comune consiste nel definire gli eventi che segnalano quando si verifica una modifica. Si può quindi installare un gestore dell'evento che riceve le notifiche relative alle modifiche. Il gestore risponde trasferendo i dati da un oggetto a un altro. Tuttavia, se sono presenti molte viste, è necessario usare anche molti gestori degli eventi ed è richiesta una grande quantità di codice.

## <a name="the-data-binding-solution"></a>La soluzione offerta dal data binding

Il data binding consente di automatizzare questo processo ed elimina la necessità di usare gestori degli eventi. I data binding possono essere implementati nel codice o in XAML, ma sono molto più comuni in XAML dove contribuiscono alla riduzione delle dimensioni del file code-behind. Sostituendo il codice procedurale nei gestori degli eventi con codice dichiarativo o markup, l'applicazione diventa più semplice e chiara.

Uno dei due oggetti coinvolti in un data binding è quasi sempre un elemento che deriva da `View` e fa parte dell'interfaccia visiva di una pagina. L'altro oggetto può essere:

- Un altro oggetto `View` derivato, in genere nella stessa pagina.
- Un oggetto in un file di codice.

Nei programmi di dimostrazione, ad esempio quelli dell'esempio [**DataBindingDemos**](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos), i data binding tra due `View` derivati vengono spesso visualizzati per motivi di semplicità e chiarezza. Tuttavia, gli stessi principi possono essere applicati ai data binding tra un oggetto `View` e altri oggetti. Quando un'applicazione viene compilata utilizzando l'architettura Model-View-ViewModel (MVVM), la classe con i dati sottostanti viene spesso definita un modello di visualizzazione.

I data binding sono illustrati nelle seguenti serie di articoli:

## <a name="basic-bindings"></a>[Binding di base](basic-bindings.md)

Informazioni sulla differenza tra l'origine e la destinazione del data binding con esempi di data binding semplici nel codice e in XAML.

## <a name="binding-mode"></a>[Modalità di binding](binding-mode.md)

Illustra in che modo la modalità di binding può controllare il flusso di dati tra i due oggetti.

## <a name="string-formatting"></a>[Formattazione delle stringhe](string-formatting.md)

Usare un data binding per formattare e visualizzare gli oggetti come stringhe.

## <a name="binding-path"></a>[Percorso di binding](binding-path.md)

Informazioni dettagliate sulla proprietà `Path` del data binding per l'accesso a proprietà secondarie e ai membri della raccolta.

## <a name="binding-value-converters"></a>[Convertitori di valori per i binding](converters.md)

Usare i convertitori di valori dei binding per modificare i valori all'interno del data binding.

## <a name="relative-bindings"></a>[Binding relativi](relative-bindings.md)

Usare associazioni relative per impostare l'origine di associazione rispetto alla posizione della destinazione di associazione.

## <a name="binding-fallbacks"></a>[Fallback di binding](binding-fallbacks.md)

Per rendere più solidi i data binding, definire i valori di fallback da usare se il processo di binding ha esito negativo.

## <a name="the-command-interface"></a>[Interfaccia di comando](commanding.md)

Implementare la proprietà `Command` con i data binding.

## <a name="compiled-bindings"></a>[Binding compilati](compiled-bindings.md)

Usare binding compilati per migliorare le prestazioni del data binding.

## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demos (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/databindingdemos) (Demo di data binding)
- [Capitolo sul data binding della documentazione di Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter16.md)
- [Estensioni di markup XAMLXAML Markup Extensions](~/xamarin-forms/xaml/markup-extensions/index.md)
