---
title: Xamarin.FormsNozioni di base su XAML
description: Questa guida illustra come iniziare a usare XAML multipiattaforma per dispositivi mobili. XAML consente agli sviluppatori di definire interfacce utente nelle Xamarin.Forms applicazioni utilizzando markup anziché codice.
ms.prod: ''
ms.custom: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8f5f69038f7d5cfe7ad46f013acb1519c4e9569f
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84138619"
---
# <a name="xamarinforms-xaml-basics"></a>Xamarin.FormsNozioni di base su XAML

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

EXtensible Application Markup Language (XAML) è un linguaggio basato su XML creato da Microsoft come alternativa alla programmazione di codice per la creazione di istanze e l'inizializzazione di oggetti e l'organizzazione di tali oggetti nelle gerarchie padre-figlio. XAML è stato adattato a diverse tecnologie all'interno di .NET Framework, ma ha trovato la maggior utilità nella definizione del layout delle interfacce utente all'interno del Windows Presentation Foundation (WPF), Silverlight, della Windows Runtime e del piattaforma UWP (Universal Windows Platform) (UWP).

XAML consente agli sviluppatori di definire interfacce utente nelle Xamarin.Forms applicazioni utilizzando markup anziché codice. XAML non è mai necessario in un Xamarin.Forms programma, ma è spesso più conciso e visivamente coerente rispetto al codice equivalente e potenzialmente utilizzabile come strumento. XAML è particolarmente adatto per l'uso con l'architettura dell'applicazione MVVM (Model-View-ViewModel) più diffusa: XAML definisce la vista collegata al codice ViewModel tramite associazioni dati basate su XAML.

All'interno di un file XAML, lo Xamarin.Forms sviluppatore può definire interfacce utente usando tutte le Xamarin.Forms Visualizzazioni, i layout e le pagine, nonché le classi personalizzate. Il file XAML può essere compilato o incorporato nell'eseguibile. In entrambi i casi, le informazioni XAML vengono analizzate in fase di compilazione per individuare gli oggetti denominati e di nuovo in fase di esecuzione per creare un'istanza e inizializzare gli oggetti e per stabilire collegamenti tra questi oggetti e il codice di programmazione.

XAML presenta diversi vantaggi rispetto al codice equivalente:

- XAML è spesso più conciso e leggibile rispetto al codice equivalente.
- La gerarchia padre-figlio insita in XML consente a XAML di simulare una maggiore chiarezza visiva della gerarchia padre-figlio degli oggetti dell'interfaccia utente.
- XAML può essere scritto in modo semplice dai programmatori, ma si presta anche a essere strumenti e generato dagli strumenti di progettazione visivi.

Sono inoltre disponibili svantaggi, principalmente correlati a limitazioni intrinseche ai linguaggi di markup:

- XAML non può contenere codice. Tutti i gestori eventi devono essere definiti in un file di codice.
- XAML non può contenere cicli per l'elaborazione ripetitiva. Tuttavia, diversi Xamarin.Forms oggetti visivi, in particolare, [`ListView`](xref:Xamarin.Forms.ListView) possono generare più elementi figlio in base agli oggetti della relativa `ItemsSource` raccolta.
- XAML non può contenere l'elaborazione condizionale (Tuttavia, un'associazione dati può fare riferimento a un convertitore di associazioni basato sul codice che consente efficacemente un'elaborazione condizionale).
- XAML non è in genere in grado di creare istanze di classi che non definiscono un costruttore senza parametri. In alcuni casi, tuttavia, è possibile aggirare questa restrizione.
- XAML non è in genere in grado di chiamare metodi. Questa restrizione può talvolta essere superata.

Non esiste ancora una finestra di progettazione visiva per la generazione di XAML nelle Xamarin.Forms applicazioni. Tutto il codice XAML deve essere scritto manualmente, ma è presente un visualizzatore [Anteprima XAML](~/xamarin-forms/xaml/xaml-previewer/index.md). I programmatori nuovi per XAML potrebbero voler compilare ed eseguire le applicazioni in modo frequente, in particolare dopo qualsiasi elemento che potrebbe non essere corretto. Anche gli sviluppatori con molta esperienza in XAML sanno che la sperimentazione è gratificante.

XAML è fondamentalmente XML, ma XAML presenta alcune funzionalità di sintassi univoche. I più importanti sono:

- Elementi Property
- Proprietà associate
- Estensioni di markup

Queste funzionalità *non* sono estensioni XML. Il codice XAML è interamente valido. Tuttavia, queste funzionalità di sintassi XAML utilizzano XML in modo univoco. Questi argomenti sono illustrati in dettaglio negli articoli seguenti, che concludono con un'introduzione all'uso di XAML per implementare MVVM.

## <a name="requirements"></a>Requisiti

Questo articolo presuppone una certa familiarità con Xamarin.Forms . Questo articolo presuppone inoltre una certa familiarità con XML, inclusa la comprensione dell'uso delle dichiarazioni dello spazio dei nomi XML e dei termini *elemento*, *tag*e *attributo*.

Quando si ha familiarità con Xamarin.Forms e XML, iniziare a leggere la [parte 1. Introduzione con XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md).

## <a name="related-links"></a>Collegamenti correlati

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Creazione di un libro di app per dispositivi mobili](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)
- [Xamarin.FormsCampioni](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Series/Xamarin-101/XamarinForms-UI-with-XAML-5-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
