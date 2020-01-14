---
title: Riepilogo del capitolo 1. In che modo si adatta Xamarin.Forms?
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 1. In che modo si adatta Xamarin.Forms?'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F3F864FF-EE70-49D0-90D1-388889037625
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 6dfa473bdfb4c1dd88ca833dbf5011a0bbdec42a
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032890"
---
# <a name="summary-of-chapter-1-how-does-xamarinforms-fit-in"></a>Riepilogo del capitolo 1. In che modo si adatta Xamarin.Forms?

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)

> [!NOTE]
> Le note in questa pagina indicano le aree in cui Xamarin.Forms è divergente rispetto al materiale presentato nel libro.

Uno dei processi più sgradevoli nella programmazione è il porting di una codebase da una piattaforma a un'altra, in particolare se tale piattaforma prevede un linguaggio di programmazione diverso. Si verifica una tentazione durante il porting del codice anche per eseguire il refactoring, ma se entrambe le piattaforme devono essere mantenute in parallelo, le differenze tra le due codebase saranno più complesse.

## <a name="cross-platform-mobile-development"></a>Sviluppo di app per dispositivi mobili multipiattaforma

Questo problema è comune quando la destinazione è la piattaforma per dispositivi mobili. Attualmente esistono due principali piattaforme per dispositivi mobili, la famiglia Apple di iPhone e iPad che eseguono il sistema operativo iOS e il sistema operativo Android eseguito su diversi telefoni e tablet. Un'altra piattaforma significativa è la piattaforma UWP (Universal Windows Platform) di Microsoft (UWP), che consente a un singolo programma di avere come destinazione entrambi Windows 10.

Un fornitore di software che desidera utilizzare queste piattaforme deve gestire paradigmi di interfaccia utente diversi, tre diversi ambienti di sviluppo, tre diverse interfacce di programmazione e&mdash;probabilmente&mdash;tre diverse linguaggi di programmazione: Objective-C per iPhone e iPad, Java per Android e C# per Windows.

## <a name="the-c-and-net-solution"></a>Soluzione C# e .NET

Sebbene Objective-C, Java e C# siano tutti derivati dal linguaggio di programmazione C, sono stati sviluppati da percorsi molto diversi. C#è la più recente di questi linguaggi ed è stata maturata in modo molto utile. Inoltre, C# è strettamente associato a un'intera infrastruttura di programmazione denominata .NET, che fornisce il supporto per Math, debug, Reflection, raccolte, globalizzazione, I/O di file, rete, sicurezza, Threading, servizi Web, gestione dei dati, e la lettura e la scrittura di XML e JSON.

Xamarin fornisce attualmente strumenti per la destinazione delle API Mac, iOS e Android Native usando C# e .NET. Questi strumenti sono denominati Xamarin.Mac, Xamarin.iOS e Xamarin.Android, collettivamente noti come piattaforma Xamarin. Si tratta di librerie e associazioni che esprimono le API native di queste piattaforme con idiomi .NET.

Gli sviluppatori possono usare la piattaforma Xamarin per scrivere applicazioni C# in destinate a Mac, iOS o Android. Tuttavia, quando si fa riferimento a più di una piattaforma, è molto utile condividere parte del codice tra le piattaforme di destinazione. Questa operazione comporta la separazione del programma in codice dipendente dalla piattaforma (che in genere interessa l'interfaccia utente) e il codice indipendente dalla piattaforma, che in genere richiede solo .NET Framework di base. Questo codice indipendente dalla piattaforma può trovarsi in una libreria di classi portabile (PCL) o in un progetto condiviso, spesso denominato progetto asset condiviso o SAP.

> [!NOTE]
> Le librerie di classi portabili sono state sostituite dalle librerie .NET Standard. Tutto il codice di esempio del libro è stato convertito in modo da usare le librerie .NET standard.

## <a name="introducing-xamarinforms"></a>Introduzione a Xamarin.Forms

Quando la destinazione è costituita da più piattaforme mobili, Xamarin.Forms consente di condividere ancora più codice. Un singolo programma scritto per Xamarin.Forms può avere come destinazione le piattaforme seguenti:

- iOS per programmi eseguiti in iPhone, iPad e iPod touch
- Android per programmi eseguiti su telefoni e Tablet Android
- piattaforma UWP (Universal Windows Platform) di destinazione Windows 10

> [!NOTE]
> Xamarin.Forms non supporta più Windows 8.1, Windows Phone 8,1 o Windows 10 Mobile, ma le applicazioni Xamarin.Forms vengono eseguite sul desktop di Windows 10. È disponibile anche il supporto in anteprima per le piattaforme [Mac](~/xamarin-forms/platform/other/mac.md), [WPF](~/xamarin-forms/platform/other/wpf.md), [GTK #](~/xamarin-forms/platform/other/gtk.md)e [Tizen](~/xamarin-forms/platform/other/tizen.md) .

La maggior parte di un programma Xamarin.Forms è presente in una libreria o in un SAP. Ogni piattaforma è costituita da uno stub di applicazioni di piccole dimensioni che chiama il codice condiviso.

Le API di Xamarin.Forms sono mappate ai controlli nativi in ogni piattaforma, in modo che ogni piattaforma mantenga l'aspetto caratteristico:

[![Schermata tripla della condivisione degli oggetti visivi della piattaforma](images/ch01fg03-small.png "Controlli Xamarin.Forms in ogni piattaforma")](images/ch01fg03-large.png#lightbox "Controlli Xamarin.Forms in ogni piattaforma")

Gli screenshot da sinistra a destra mostrano un iPhone e un telefono Android:

In ogni schermata la pagina contiene un [`Label`](xref:Xamarin.Forms.Label) Xamarin.Forms per la visualizzazione di testo, un [`Button`](xref:Xamarin.Forms.Button) per l'avvio di azioni, un [`Switch`](xref:Xamarin.Forms.Switch) per la scelta di un valore di attivazione/disattivazione e un [`Slider`](xref:Xamarin.Forms.Slider) per specificare un valore in un intervallo continuo . Tutte e quattro le visualizzazioni sono elementi figlio di un [`StackLayout`](xref:Xamarin.Forms.StackLayout) in un [`ContentPage`](xref:Xamarin.Forms.ContentPage).

Collegato anche alla pagina è una barra degli strumenti Xamarin.Forms costituita da diversi [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) oggetti. Sono visibili come icone nella parte superiore degli schermi iOS e Android e nella parte inferiore della schermata di Windows 10 Mobile.

Xamarin.Forms supporta anche XAML, il Extensible Application Markup Language sviluppato in Microsoft per diverse piattaforme applicative. Tutti gli oggetti visivi del programma illustrati in precedenza sono definiti in XAML, come illustrato nell'esempio [**PlatformVisuals**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01/PlatformVisuals) .

Un programma Xamarin.Forms può determinare la piattaforma in cui è in esecuzione ed eseguire codice diverso di conseguenza. Più efficacemente, gli sviluppatori possono scrivere codice personalizzato per le varie piattaforme ed eseguire il codice da un programma Xamarin.Forms in modo indipendente dalla piattaforma. Gli sviluppatori possono inoltre creare controlli aggiuntivi scrivendo renderer per ogni piattaforma.

Anche se Xamarin.Forms è un'ottima soluzione per le applicazioni line-of-business o per la creazione di prototipi o una rapida dimostrazione del concetto, è meno ideale per le applicazioni che richiedono grafica vettoriale o interazione con tocco complesso.

## <a name="your-development-environment"></a>Ambiente di sviluppo

L'ambiente di sviluppo dipende dalle piattaforme di destinazione e dai computer che si vuole usare.

Se si vuole usare iOS come destinazione, sarà necessario un Mac con Xcode e la piattaforma Xamarin installata. Per supportare Android è necessario installare anche Java e gli SDK necessari. È quindi possibile fare riferimento a iOS e Android usando Visual Studio per Mac.

L'installazione di Visual Studio consente al PC di fare riferimento a iOS, Android e a tutte le piattaforme Windows. Tuttavia, la destinazione di iOS da Visual Studio richiede ancora un Mac con Xcode e la piattaforma Xamarin installata.

È possibile testare i programmi in un dispositivo effettivo connesso tramite USB al computer o in un simulatore.

## <a name="installation"></a>Installazione

Prima di creare e compilare un'applicazione Xamarin.Forms, è consigliabile provare a creare e compilare separatamente un'applicazione iOS, un'applicazione Android e un'applicazione UWP, a seconda delle piattaforme di destinazione e dell'ambiente di sviluppo.

Il Xamarin e i siti Web Microsoft contengono informazioni su come eseguire questa operazione:

- [Introduzione con iOS](~/ios/get-started/index.md)
- [Introduzione con Android](~/android/get-started/index.md)
- [Windows Dev Center](https://dev.windows.com)

Quando è possibile creare ed eseguire progetti per queste singole piattaforme, non è necessario creare ed eseguire un'applicazione Xamarin.Forms.

## <a name="related-links"></a>Collegamenti correlati

- [Testo completo del capitolo 1 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch01-Apr2016.pdf)
- [Esempio del capitolo 1](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)
