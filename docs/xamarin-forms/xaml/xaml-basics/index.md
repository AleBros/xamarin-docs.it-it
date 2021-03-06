---
title: Nozioni di base su XAML di Xamarin.Forms
description: Questa guida illustra come iniziare a usare XAML multipiattaforma per dispositivi mobili. XAML consente agli sviluppatori di definire interfacce utente nelle applicazioni Xamarin.Forms usando markup anziché codice.
ms.prod: xamarin
ms.custom: video
ms.assetid: 67CC2CD6-D10A-4B14-9696-1D3A410EFFBF
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/25/2017
ms.openlocfilehash: e8f5a083f49565a00a7ffe4c068d8dbd7815cc8b
ms.sourcegitcommit: efbc69acf4ea484d8815311b058114379c9db8a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73842850"
---
# <a name="xamarinforms-xaml-basics"></a>Nozioni di base su XAML di Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)

EXtensible Application Markup Language (XAML) è un linguaggio basato su XML creato da Microsoft come alternativa alla programmazione di codice per la creazione di istanze e l'inizializzazione di oggetti e l'organizzazione di tali oggetti nelle gerarchie padre-figlio. XAML è stato adattato a diverse tecnologie all'interno di .NET Framework, ma ha trovato la maggior utilità nella definizione del layout delle interfacce utente all'interno del Windows Presentation Foundation (WPF), di Silverlight, della Windows Runtime e delle finestre universali Platform (UWP).

XAML consente agli sviluppatori di definire interfacce utente nelle applicazioni Xamarin.Forms usando markup anziché codice. XAML non è mai necessario in un programma Xamarin.Forms, ma spesso è più conciso e visivamente coerente rispetto al codice equivalente e potenzialmente utilizzabile come strumento. XAML è particolarmente adatto per l'uso con l'architettura dell'applicazione MVVM (Model-View-ViewModel) più diffusa: XAML definisce la vista collegata al codice ViewModel tramite associazioni dati basate su XAML.

All'interno di un file XAML, lo sviluppatore Xamarin.Forms può definire interfacce utente usando tutte le visualizzazioni, i layout e le pagine di Xamarin.Forms, nonché le classi personalizzate. Il file XAML può essere compilato o incorporato nell'eseguibile. In entrambi i casi, le informazioni XAML vengono analizzate in fase di compilazione per individuare gli oggetti denominati e di nuovo in fase di esecuzione per creare un'istanza e inizializzare gli oggetti e per stabilire collegamenti tra questi oggetti e il codice di programmazione.

XAML presenta diversi vantaggi rispetto al codice equivalente:

- XAML è spesso più conciso e leggibile rispetto al codice equivalente.
- La gerarchia padre-figlio insita in XML consente a XAML di simulare una maggiore chiarezza visiva della gerarchia padre-figlio degli oggetti dell'interfaccia utente.
- XAML può essere scritto in modo semplice dai programmatori, ma si presta anche a essere strumenti e generato dagli strumenti di progettazione visivi.

Sono inoltre disponibili svantaggi, principalmente correlati a limitazioni intrinseche ai linguaggi di markup:

- XAML non può contenere codice. Tutti i gestori eventi devono essere definiti in un file di codice.
- XAML non può contenere cicli per l'elaborazione ripetitiva. Tuttavia, diversi oggetti visivi Xamarin.Forms, in particolare [`ListView`](xref:Xamarin.Forms.ListView) , possono generare più elementi figlio in base agli oggetti nella raccolta di `ItemsSource`.
- XAML non può contenere l'elaborazione condizionale (Tuttavia, un'associazione dati può fare riferimento a un convertitore di associazioni basato sul codice che consente efficacemente un'elaborazione condizionale).
- XAML non è in genere in grado di creare istanze di classi che non definiscono un costruttore senza parametri. In alcuni casi, tuttavia, è possibile aggirare questa restrizione.
- XAML non è in genere in grado di chiamare metodi. Questa restrizione può talvolta essere superata.

Non esiste ancora una finestra di progettazione visiva per la generazione di XAML nelle applicazioni Xamarin.Forms. Tutto il codice XAML deve essere scritto manualmente, ma è presente un visualizzatore [Anteprima XAML](~/xamarin-forms/xaml/xaml-previewer/index.md). I programmatori nuovi per XAML potrebbero voler compilare ed eseguire le applicazioni in modo frequente, in particolare dopo qualsiasi elemento che potrebbe non essere corretto. Anche gli sviluppatori con molta esperienza in XAML sanno che la sperimentazione è gratificante.

XAML è fondamentalmente XML, ma XAML presenta alcune funzionalità di sintassi univoche. I più importanti sono:

- Elementi Property
- Proprietà associate
- Estensioni di markup

Queste funzionalità *non* sono estensioni XML. Il codice XAML è interamente valido. Tuttavia, queste funzionalità di sintassi XAML utilizzano XML in modo univoco. Questi argomenti sono illustrati in dettaglio negli articoli seguenti, che concludono con un'introduzione all'uso di XAML per implementare MVVM.

## <a name="requirements"></a>Requisiti

Questo articolo presuppone una certa familiarità con Xamarin.Forms. Questo articolo presuppone inoltre una certa familiarità con XML, inclusa la comprensione dell'uso delle dichiarazioni dello spazio dei nomi XML e dei termini *elemento*, *tag*e *attributo*.

Quando si ha familiarità con Xamarin.Forms e XML, iniziare a leggere la [parte 1. Introduzione con XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md).

## <a name="related-links"></a>Collegamenti correlati

- [XamlSamples](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/xamlsamples)
- [Creazione di un libro di app per dispositivi mobili](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)
- [Esempi di Xamarin.Forms](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.Forms)

## <a name="related-video"></a>Video correlati

> [!Video https://channel9.msdn.com/Series/Xamarin-101/XamarinForms-UI-with-XAML-5-of-11/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
