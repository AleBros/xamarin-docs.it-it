---
title: Riepilogo del capitolo 1. Come si Xamarin.Forms adatta?
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 1. How does Xamarin.Forms fit in?''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 48b2fb429d206f6582886c94d4d99839d790dc8d
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136929"
---
# <a name="summary-of-chapter-1-how-does-xamarinforms-fit-in"></a>Riepilogo del capitolo 1. Come si Xamarin.Forms adatta?

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)

> [!NOTE]
> Le note in questa pagina indicano Xamarin.Forms le aree in cui è stato divergente rispetto al materiale presentato nel libro.

Uno dei processi più sgradevoli nella programmazione è il porting di una codebase da una piattaforma a un'altra, in particolare se tale piattaforma prevede un linguaggio di programmazione diverso. Si verifica una tentazione durante il porting del codice anche per eseguire il refactoring, ma se entrambe le piattaforme devono essere mantenute in parallelo, le differenze tra le due codebase saranno più complesse.

## <a name="cross-platform-mobile-development"></a>Sviluppo di app per dispositivi mobili multipiattaforma

Questo problema è comune quando la destinazione è la piattaforma per dispositivi mobili. Attualmente esistono due principali piattaforme per dispositivi mobili, la famiglia Apple di iPhone e iPad che eseguono il sistema operativo iOS e il sistema operativo Android eseguito su diversi telefoni e tablet. Un'altra piattaforma significativa è la piattaforma UWP (Universal Windows Platform) di Microsoft (UWP), che consente a un singolo programma di avere come destinazione entrambi Windows 10.

Un fornitore di software che desidera utilizzare queste piattaforme deve gestire paradigmi di interfaccia utente diversi, tre diversi ambienti di sviluppo, tre diverse interfacce di programmazione e &mdash; probabilmente &mdash; tre diversi linguaggi di programmazione: Objective-C per iPhone e iPad, Java per Android e C# per Windows.

## <a name="the-c-and-net-solution"></a>La soluzione C# e .NET

Sebbene Objective-C, Java e C# siano tutti derivati dal linguaggio di programmazione C, sono stati sviluppati da percorsi molto diversi. C# è la più recente di questi linguaggi ed è stata maturata in modi molto utili. Inoltre, C# è strettamente associato a un'intera infrastruttura di programmazione denominata .NET, che fornisce il supporto per la matematica, il debug, la reflection, le raccolte, la globalizzazione, l'I/O dei file, la rete, la sicurezza, il threading, I servizi Web, la gestione dei dati e la lettura e scrittura di XML e JSON.

Novell fornisce attualmente strumenti per la destinazione delle API Mac, iOS e Android Native con C# e .NET. Questi strumenti sono denominati Novell. Mac, Novell. iOS e Novell. Android, collettivamente noti come piattaforma Novell. Si tratta di librerie e associazioni che esprimono le API native di queste piattaforme con idiomi .NET.

Gli sviluppatori possono usare la piattaforma Novell per scrivere applicazioni in C# destinate a Mac, iOS o Android. Tuttavia, quando si fa riferimento a più di una piattaforma, è molto utile condividere parte del codice tra le piattaforme di destinazione. Questa operazione comporta la separazione del programma in codice dipendente dalla piattaforma (che in genere interessa l'interfaccia utente) e il codice indipendente dalla piattaforma, che in genere richiede solo .NET Framework di base. Questo codice indipendente dalla piattaforma può trovarsi in una libreria di classi portabile (PCL) o in un progetto condiviso, spesso denominato progetto asset condiviso o SAP.

> [!NOTE]
> Le librerie di classi portabili sono state sostituite dalle librerie .NET Standard. Tutto il codice di esempio del libro è stato convertito in modo da usare le librerie .NET standard.

## <a name="introducing-xamarinforms"></a>IntroduzioneXamarin.Forms

Quando si fa riferimento a più piattaforme mobili, Xamarin.Forms consente una maggiore condivisione del codice. Un singolo programma scritto per Xamarin.Forms può avere come destinazione le piattaforme seguenti:

- iOS per programmi eseguiti in iPhone, iPad e iPod touch
- Android per programmi eseguiti su telefoni e Tablet Android
- piattaforma UWP (Universal Windows Platform) di destinazione Windows 10

> [!NOTE]
> Xamarin.Formsnon supporta più Windows 8.1, Windows Phone 8,1 o Windows 10 Mobile, ma le Xamarin.Forms applicazioni vengono eseguite sul desktop di Windows 10. È disponibile anche il supporto in anteprima per le piattaforme [Mac](~/xamarin-forms/platform/other/mac.md), [WPF](~/xamarin-forms/platform/other/wpf.md), [GTK #](~/xamarin-forms/platform/other/gtk.md)e [Tizen](~/xamarin-forms/platform/other/tizen.md) .

La maggior parte di un Xamarin.Forms programma esiste in una libreria o in un SAP. Ogni piattaforma è costituita da uno stub di applicazioni di piccole dimensioni che chiama il codice condiviso.

Le Xamarin.Forms API vengono mappate ai controlli nativi in ogni piattaforma, in modo che ogni piattaforma mantenga l'aspetto caratteristico:

[![Schermata tripla della condivisione degli oggetti visivi della piattaforma](images/ch01fg03-small.png "[! OP. Controlli NO-LOC (Novell. Forms)] in ogni piattaforma")](images/ch01fg03-large.png#lightbox "[! OP. Controlli NO-LOC (Novell. Forms)] in ogni piattaforma")

Gli screenshot da sinistra a destra mostrano un iPhone e un telefono Android:

In ogni schermata la pagina contiene un oggetto Xamarin.Forms [`Label`](xref:Xamarin.Forms.Label) per la visualizzazione di testo, un [`Button`](xref:Xamarin.Forms.Button) per l'avvio di azioni, un [`Switch`](xref:Xamarin.Forms.Switch) per la scelta di un valore di attivazione/disattivazione e un oggetto [`Slider`](xref:Xamarin.Forms.Slider) per specificare un valore all'interno di un intervallo continuo. Tutte e quattro le visualizzazioni sono elementi figlio di un oggetto [`StackLayout`](xref:Xamarin.Forms.StackLayout) in un oggetto [`ContentPage`](xref:Xamarin.Forms.ContentPage) .

Collegato anche alla pagina è una Xamarin.Forms barra degli strumenti costituita da diversi [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) oggetti. Sono visibili come icone nella parte superiore degli schermi iOS e Android e nella parte inferiore della schermata di Windows 10 Mobile.

Xamarin.Formssupporta anche XAML, il Extensible Application Markup Language sviluppato presso Microsoft per diverse piattaforme applicative. Tutti gli oggetti visivi del programma illustrati in precedenza sono definiti in XAML, come illustrato nell'esempio [**PlatformVisuals**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01/PlatformVisuals) .

Un Xamarin.Forms programma può determinare la piattaforma in cui è in esecuzione ed eseguire codice diverso di conseguenza. Più efficacemente, gli sviluppatori possono scrivere codice personalizzato per le varie piattaforme ed eseguire il codice da un Xamarin.Forms programma in modo indipendente dalla piattaforma. Gli sviluppatori possono inoltre creare controlli aggiuntivi scrivendo renderer per ogni piattaforma.

Sebbene Xamarin.Forms sia un'ottima soluzione per le applicazioni line-of-business o per la creazione di prototipi, o una rapida dimostrazione del concetto, è meno ideale per le applicazioni che richiedono la grafica vettoriale o un'interazione di tocco complessa.

## <a name="your-development-environment"></a>Ambiente di sviluppo

L'ambiente di sviluppo dipende dalle piattaforme di destinazione e dai computer che si vuole usare.

Se si vuole usare iOS come destinazione, sarà necessario un Mac con Xcode e la piattaforma Novell installata. Per supportare Android è necessario installare anche Java e gli SDK necessari. È quindi possibile fare riferimento a iOS e Android usando Visual Studio per Mac.

L'installazione di Visual Studio consente al PC di fare riferimento a iOS, Android e a tutte le piattaforme Windows. Tuttavia, la destinazione di iOS da Visual Studio richiede ancora un Mac con Xcode e la piattaforma Novell installata.

È possibile testare i programmi in un dispositivo effettivo connesso tramite USB al computer o in un simulatore.

## <a name="installation"></a>Installazione

Prima di creare e Xamarin.Forms compilare un'applicazione, è consigliabile provare a creare e compilare separatamente un'applicazione iOS, un'applicazione Android e un'applicazione UWP, a seconda delle piattaforme di destinazione e dell'ambiente di sviluppo.

Il Novell e i siti Web Microsoft contengono informazioni su come eseguire questa operazione:

- [Introduzione a iOS](~/ios/get-started/index.md)
- [Introduzione ad Android](~/android/get-started/index.md)
- [Windows Dev Center](https://dev.windows.com)

Quando è possibile creare ed eseguire progetti per queste singole piattaforme, non è necessario creare ed eseguire un' Xamarin.Forms applicazione.

## <a name="related-links"></a>Collegamenti correlati

- [Testo completo del capitolo 1 (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch01-Apr2016.pdf)
- [Esempio del capitolo 1](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)
