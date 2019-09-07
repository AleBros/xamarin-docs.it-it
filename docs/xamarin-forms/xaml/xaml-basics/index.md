---
title: Nozioni fondamentali di XAML di xamarin. Forms
description: Questa guida illustra come iniziare a usare XAML lo sviluppo multipiattaforma per dispositivi mobili. XAML consente agli sviluppatori di definire le interfacce utente nelle applicazioni xamarin. Forms tramite markup anziché codice.
ms.prod: xamarin
ms.assetid: 67CC2CD6-D10A-4B14-9696-1D3A410EFFBF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: 0b30a0167b72ebeb4c53481c6d2d9d9344296894
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70767357"
---
# <a name="xamarinforms-xaml-basics"></a>Nozioni fondamentali di XAML di xamarin. Forms

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

XAML (eXtensible Application Markup Language) consente agli sviluppatori di definire le interfacce utente nelle applicazioni Xamarin.Forms tramite markup anziché codice. XAML non è mai obbligatorio in un programma di xamarin. Forms, ma spesso è più conciso e visivamente più coerenti rispetto al codice equivalente e potenzialmente ricco di strumenti. XAML è particolarmente adatto per l'uso con l'architettura dell'applicazione MVVM (Model-View-ViewModel) più diffusa: XAML definisce la vista collegata al codice ViewModel tramite associazioni dati basate su XAML.

## <a name="xaml-basics-contents"></a>Contenuto di nozioni di base XAML

- [Panoramica](#Overview)
- [Parte 1. Introduzione a XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
- [Parte 2. Sintassi XAML essenziale](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
- [Parte 3. Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
- [Parte 4. Nozioni di base sul data binding](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
- [Parte 5. Da Data Binding a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)

Oltre a questi articoli nozioni fondamentali di XAML, è possibile scaricare capitoli del libro [creazione di App per dispositivi mobili con xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md):

[![](images/cover-sml.png "Copertina")](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)

XAML argomenti sono trattati in modo più approfondito in molte capitoli del libro, tra cui:

| Capitolo | Download | Riepilogo |
|---------|---------|---------|
| Capitolo 7. Visual Studio XAML. Codice | [Scaricare PDF](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf) | [Riepilogo](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md) |
| Capitolo 8. Codice e XAML in armonia | [Scaricare PDF](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf) | [Riepilogo](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter08.md) |
| Capitolo 10. Estensioni di Markup XAML | [Scaricare PDF](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf) | [Riepilogo](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md) |
| Capitolo 18. MVVM | [Scaricare PDF](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf) | [Riepilogo](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter18.md) |

Possono essere questi capitoli [scaricati gratuitamente](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md).

<a name="Overview" />

## <a name="overview"></a>Panoramica

XAML è un linguaggio basato su XML creato da Microsoft come alternativa al codice di programmazione per la creazione di istanze e inizializzazione di oggetti e per organizzare gli oggetti nelle gerarchie padre-figlio. XAML è stato adattato alle tecnologie diverse all'interno di .NET framework, ma è stato trovato l'utilità maggiore nella definizione del layout di interfacce utente all'interno di Windows Presentation Foundation (WPF), Silverlight, il Runtime di Windows e di Windows universale Platform (UWP).

XAML fa anche parte di xamarin. Forms, lo sviluppo multipiattaforma in modo nativo basato su interfaccia di programmazione per iOS, Android e UWP i dispositivi mobili. All'interno del file XAML, lo sviluppatore di xamarin. Forms può definire le interfacce utente usando tutti di xamarin. Forms viste, layout e pagine, come le classi personalizzate. Il file XAML può essere compilato o incorporato nell'eseguibile. In entrambi i casi, le informazioni XAML viene analizzate in fase di compilazione per individuare gli oggetti denominati e nuovamente in fase di esecuzione per creare un'istanza e inizializzare gli oggetti e per stabilire collegamenti tra questi oggetti e il codice di programmazione.

XAML presenta diversi vantaggi rispetto al codice equivalente:

- XAML è spesso più conciso e leggibile rispetto al codice equivalente.
- La gerarchia padre-figlio intrinseca in XML consente di simulare itsc visual la gerarchia padre-figlio degli oggetti dell'interfaccia utente XAML.
- XAML può essere facilmente scritti a mano dai programmatori, ma anche si presta ricco di strumenti e generato dagli strumenti di progettazione visiva.

Naturalmente, esistono anche svantaggi, per lo più correlati a limitazioni intrinseche di linguaggi di markup:

- XAML non può contenere codice. Tutti i gestori eventi devono essere definiti in un file di codice.
- XAML non può contenere cicli per l'elaborazione ripetitiva. (Tuttavia, alcuni oggetti visivi di xamarin. Forms, in particolare [ `ListView` ](xref:Xamarin.Forms.ListView) , ovvero può generare più elementi figlio in base agli oggetti in relativo `ItemsSource` raccolta.)
- XAML non può contenere un'elaborazione condizionale (tuttavia, un'associazione di dati può fare riferimento a un convertitore di associazioni basate su codice che consente un'elaborazione condizionale.)
- XAML a livello generale non è possibile creare un'istanza di classi che non si definiscono un costruttore senza parametri. (Tuttavia, vi è a volte come risolvere questa limitazione).
- XAML in genere non è possibile chiamare i metodi. (Anche in questo caso, questa restrizione può talvolta essere risolti.)

Non è stato ancora una finestra di progettazione visiva per la generazione di XAML in applicazioni xamarin. Forms. XAML tutti devono essere scritti a mano, ma è presente una [Visualizzatore anteprima XAML](~/xamarin-forms/xaml/xaml-previewer/index.md). I programmatori di nuovo a XAML potrebbero voler spesso compilare ed eseguire le applicazioni, in particolare dopo tutto ciò che potrebbe non essere ovviamente corretto. Anche gli sviluppatori con molta esperienza in XAML sapere che sperimentazione è gratificante.

XAML è fondamentalmente XML, ma XAML ha alcune funzionalità della sintassi univoco. I più importanti sono:

- Elementi di proprietà
- Proprietà associate
- Estensioni di markup

Queste funzionalità sono *non* XML estensioni. XAML è interamente legali XML. Ma queste funzionalità della sintassi XAML utilizzare XML in modo univoco. Vengono descritti in dettaglio negli articoli seguenti, che terminano con un'introduzione all'uso di XAML per l'implementazione del modello MVVM.

## <a name="requirements"></a>Requisiti

Questo articolo presuppone una conoscenza pratica di xamarin. Forms. Questo articolo presuppone anche una certa conoscenza di XML, incluse informazioni sull'utilizzo di dichiarazioni dello spazio dei nomi XML e i termini *elemento*, *tag*, e *attributo*.

Quando si ha familiarità con xamarin. Forms e XML, iniziare a leggere [parte 1. Introduzione a XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md).

## <a name="related-links"></a>Collegamenti correlati

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Creazione manuale di App per dispositivi mobili](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)
- [Esempi di Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)
