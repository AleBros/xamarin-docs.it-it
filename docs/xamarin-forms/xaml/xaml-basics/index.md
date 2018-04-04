---
title: Nozioni fondamentali di xamarin. Forms XAML
description: Introduzione al markup multipiattaforma per dispositivi mobili
ms.prod: xamarin
ms.assetid: 67CC2CD6-D10A-4B14-9696-1D3A410EFFBF
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 10/25/2017
ms.openlocfilehash: 991d928c2c58f05098a41c84aba295a31636ab96
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="xamarinforms-xaml-basics"></a>Nozioni fondamentali di xamarin. Forms XAML

XAML (eXtensible Application Markup Language) consente agli sviluppatori di definire le interfacce utente nelle applicazioni Xamarin.Forms tramite markup anziché codice. XAML non è mai necessario in un programma xamarin. Forms, ma spesso è più conciso e visivamente più coerente rispetto al codice equivalente e potenzialmente ricco. XAML è particolarmente adatto per l'utilizzo con l'architettura dell'applicazione più diffuso MVVM (Model-View-ViewModel): codice XAML definisce la vista che è collegata al codice ViewModel le associazioni di dati basate su XAML.

## <a name="xaml-basics-contents"></a>Contenuto di nozioni di base XAML

* [Panoramica](#Overview)
* [Parte 1. Introduzione a XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md)
* [Parte 2. Sintassi XAML essenziale](~/xamarin-forms/xaml/xaml-basics/essential-xaml-syntax.md)
* [Parte 3. Estensioni di markup XAML](~/xamarin-forms/xaml/xaml-basics/xaml-markup-extensions.md)
* [Parte 4. Nozioni di base sul data binding](~/xamarin-forms/xaml/xaml-basics/data-binding-basics.md)
* [Parte 5. Da un'associazione dati a MVVM](~/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm.md)

Oltre a questi articoli XAML nozioni di base, è possibile scaricare capitoli del libro [la creazione di App per dispositivi mobili con xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md):

[![](images/cover-sml.png "Copertina")](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)

Vengono trattati argomenti XAML in modo più approfondito in molti capitoli del libro, tra cui:

<table style="border:0px; box-shadow:0 0px 0px" cellpadding="0" cellspacing="2" border="0" width="85%">
<tr style="background:#ecf0f1">
  <td style="border:0px;">
    <h4>Capitolo 7. Visual Studio XAML. Codice</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch07-Apr2016.pdf">Scarica il PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter07.md">Riepilogo</a></td>
</tr>
<tr style="background:#f8f9fa">
  <td style="border:0px;">
    <h4>Capitolo 8. Codice e XAML nel armonia</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch08-Apr2016.pdf">Scarica il PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter08.md">Riepilogo</a></td>
</tr>
<tr style="background:#f8f9fa">
  <td style="border:0px;">
    <h4>Capitolo 10. Estensioni di Markup XAML</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch10-Apr2016.pdf">Scarica il PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter10.md">Riepilogo</a></td>
</tr>
<tr style="background:#f8f9fa">
  <td style="border:0px;">
    <h4>Capitolo 18. MVVM</h4>
  </td>
  <td style="border:0px;" align="right"><a href="https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf">Scarica il PDF</a> </td>
  <td style="border:0px;" align="right"><a href="~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter18.md">Riepilogo</a></td></tr>
</table>

Possono essere questi capitoli [scaricati gratuitamente](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md).

<a name="Overview" />

## <a name="overview"></a>Panoramica

XAML è un linguaggio basato su XML creato da Microsoft come alternativa per il codice di programmazione per la creazione e inizializzazione di oggetti e per organizzare gli oggetti nelle gerarchie padre-figlio. XAML è stato adattato per diverse tecnologie in .NET framework, ma è stato trovato l'utilità maggiore nella definizione del layout di interfacce utente all'interno di Windows Presentation Foundation (WPF), Silverlight, Windows Runtime e di Windows universale Platform (UWP).

XAML fa anche parte di xamarin. Forms, multipiattaforma in modo nativo basato su interfaccia di programmazione per iOS, Android e UWP i dispositivi mobili. All'interno del file XAML, lo sviluppatore di xamarin. Forms può definire le interfacce utente utilizzando tutti xamarin. Forms visualizzazioni, layout e pagine, come le classi e come personalizzate. Il file XAML può essere compilato o incorporato nel file eseguibile. In entrambi i casi, le informazioni di XAML viene analizzate in fase di compilazione per individuare gli oggetti denominati e di nuovo in fase di esecuzione di un'istanza e inizializzare gli oggetti e per stabilire collegamenti tra questi oggetti e il codice di programmazione.

XAML presenta diversi vantaggi rispetto equivalente del codice:

-  XAML è spesso più conciso e leggibili equivalente del codice.
-  La gerarchia padre-figlio inerente XML consente di XAML per riprodurre con maggiore chiarezza, la gerarchia padre-figlio degli oggetti dell'interfaccia utente.
-  XAML può essere facilmente scritta manualmente dai programmatori, ma può anche essere applicato in modo da essere generati dagli strumenti di progettazione visiva e ricco.

Naturalmente, esistono anche gli svantaggi correlati prevalentemente ai limiti intrinseci linguaggi:

-  XAML non può contenere codice. Tutti i gestori eventi devono essere definiti in un file di codice.
-  XAML non può contenere cicli per l'elaborazione ricorrenti. (Tuttavia, diversi oggetti visivi di xamarin. Forms, in particolare [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) : possibile generazione di più figli basate sugli oggetti in relativo `ItemsSource` insieme.)
-  XAML non può contenere un'elaborazione condizionale (tuttavia, un'associazione dati fa riferimento un convertitore di associazione basata su codice che consente un'elaborazione condizionale).
-  XAML in genere non è possibile creare un'istanza di classi che non si definiscono un costruttore senza parametri. (Tuttavia, è talvolta possibile aggirare questa restrizione.)
-  XAML in genere non è possibile chiamare i metodi. (Nuovo, questa restrizione può talvolta essere aggirata.)

Non è ancora una finestra di progettazione visiva per la generazione di XAML in applicazioni di xamarin. Forms. XAML tutti deve essere scritta manualmente, ma è presente un [anteprima XAML](~/xamarin-forms/xaml/xaml-previewer.md). Ai programmatori di nuovo di XAML potrebbero voler spesso compilare ed eseguire le applicazioni, in particolare dopo tutto ciò che potrebbe non essere ovviamente corretto. Anche gli sviluppatori con molta esperienza in XAML sapere che è gratificante sperimentazione.

XAML è fondamentalmente XML, ma alcune funzionalità della sintassi univoco XAML. I più importanti sono:

- Elementi proprietà
- Proprietà associate
- Estensioni di markup

Queste funzionalità sono *non* estensioni XML. XAML è interamente legali XML. Ma queste funzionalità della sintassi XAML utilizzare XML in modo univoco. Vengono descritti in dettaglio negli articoli seguenti, che terminano con un'introduzione all'utilizzo di XAML per l'implementazione MVVM.

## <a name="requirements"></a>Requisiti

Questo articolo si presuppone una conoscenza del funzionamento con xamarin. Forms. Lettura [Introduzione a xamarin. Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md) è consigliato.

In questo articolo si presuppone inoltre avere familiarità con XML, incluse informazioni sull'utilizzo di dichiarazioni dello spazio dei nomi XML e le condizioni *elemento*, *tag*, e *attributo*.

Quando si ha familiarità con xamarin. Forms e XML, avviare la lettura [parte 1. Introduzione a XAML](~/xamarin-forms/xaml/xaml-basics/get-started-with-xaml.md).



## <a name="related-links"></a>Collegamenti correlati

- [XamlSamples](https://developer.xamarin.com/samples/xamarin-forms/XamlSamples/)
- [Introduzione a xamarin. Forms](~/xamarin-forms/get-started/introduction-to-xamarin-forms.md)
- [Creazione manuale di App per dispositivi mobili](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md)
- [Esempi di Xamarin.Forms](https://developer.xamarin.com/samples/xamarin-forms/all/)
