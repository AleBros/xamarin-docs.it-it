---
title: Riepilogo del capitolo 1. Xamarin. Forms?
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: Riepilogo del capitolo 1. Xamarin. Forms?'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F3F864FF-EE70-49D0-90D1-388889037625
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 58d3b3ae067913a85c3ada5f5b35e64511523ff8
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207908"
---
# <a name="summary-of-chapter-1-how-does-xamarinforms-fit-in"></a>Riepilogo del capitolo 1. Xamarin. Forms?

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)

> [!NOTE]
> Le note in questa pagina indicano le aree in cui xamarin. Forms è diversa dal materiale presentato nel libro.

Uno dei processi più spiacevoli nella programmazione consiste nel porting di un codice di base da una piattaforma a altra, in particolare se tale piattaforma prevede un linguaggio di programmazione diversi. È possibile quando il porting del codice per effettuare il refactoring, nonché, ma se entrambe le piattaforme devono essere mantenute in parallelo, quindi le differenze tra i due codebase renderà futura manutenzione più difficile.

## <a name="cross-platform-mobile-development"></a>Sviluppo di app per dispositivi mobili multipiattaforma

Questo problema è comune quando la destinazione di piattaforme per dispositivi mobili. Attualmente, esistono due principali piattaforme per dispositivi mobili, la famiglia di Apple iPhone e iPad che eseguono il sistema operativo iOS e il sistema operativo Android che viene eseguito su una vasta gamma di telefoni e Tablet. Un'altra piattaforma significativa è Microsoft Windows della piattaforma UWP (Universal), che consente a un singolo programma di destinazione sia Windows 10.

Un fornitore di software che vuole che per queste piattaforme di destinazione deve affrontare paradigmi di interfaccia utente diverso, tre ambienti di sviluppo diverso, tre interfacce di programmazione diversi, e&mdash;forse più considerato&mdash;tre linguaggi di programmazione diversi: Objective-C per iPhone e iPad, per Android, Java e C# per Windows.

## <a name="the-c-and-net-solution"></a>La soluzione C# e .NET

Sebbene Objective-C, Java e C# sono derivati dal linguaggio di programmazione C, essi si sono evolute dai percorsi molto diversi. C# è la più recente di questi linguaggi ed è stato in fase di sviluppo in modo molto utile. Inoltre, C# è strettamente associato a un'intera infrastruttura di programmazione denominata .NET, che fornisce il supporto per la matematica, il debug, la reflection, raccolte, globalizzazione, i/o file, rete, sicurezza, threading, servizi web, la gestione dei dati e XML e JSON la lettura e scrittura.

Attualmente, Xamarin offre strumenti per il Mac, iOS e le API di Android usando C# e .NET native di destinazione. Questi strumenti vengono chiamati xamarin. Android, noti come la piattaforma Xamarin, xamarin. Mac e xamarin. IOS. Queste sono le librerie e le associazioni che esprimono le API native di queste piattaforme con linguaggi .NET.

Gli sviluppatori possono usare la piattaforma Xamarin per scrivere applicazioni in C# destinati a Mac, iOS o Android. Ma quando la destinazione più piattaforme, è molto utile a condividere parte del codice tra le piattaforme di destinazione. Questa operazione richiede la separazione del programma in codice dipendente dalla piattaforma (a livello generale che coinvolgono l'interfaccia utente) e codice indipendente dalla piattaforma, che in genere richiede solo base .NET framework. Questo codice indipendente dalla piattaforma può trovarsi in una libreria di classi portabile (PCL) o un progetto condiviso, spesso denominata un progetto di Asset condivisi o SAP.

> [!NOTE]
> Librerie di classi portabili sono state sostituite dalle librerie .NET Standard. Tutto il codice di esempio dal libro è stato convertito per utilizzare le librerie .NET standard.

## <a name="introducing-xamarinforms"></a>Introduzione a xamarin. Forms

Quando la destinazione più piattaforme per dispositivi mobili, xamarin. Forms consente la condivisione del codice ancora più. Un singolo programma scritto per xamarin. Forms è utilizzabile queste piattaforme:

- per i programmi eseguiti su iPhone, iPad e iPod touch iOS
- Android per i programmi eseguiti su telefoni e Tablet Android
- la piattaforma universale di Windows di destinazione Windows 10

> [!NOTE]
> Xamarin. Forms non supporta più Windows 8.1, Windows Phone 8.1 o Windows 10 Mobile, ma le applicazioni xamarin. Forms vengono eseguite sul desktop di Windows 10. È inoltre disponibile il supporto di anteprima per il [Mac](~/xamarin-forms/platform/other/mac.md), [WPF](~/xamarin-forms/platform/other/wpf.md), [GTK #](~/xamarin-forms/platform/other/gtk.md), e [Tizen](~/xamarin-forms/platform/other/tizen.md) piattaforme.

La maggior parte di un'applicazione xamarin. Forms esistente in una libreria o un SAP. Ognuna delle piattaforme è costituito da uno stub piccola applicazione che chiama il codice condiviso.

Eseguire il mapping APIs Xamarin.Forms in controlli nativi in ciascuna piattaforma, in modo che ogni piattaforma gestisce la caratteristica aspetto:

[![Schermata tripla degli oggetti visivi di piattaforma di condivisione](images/ch01fg03-small.png "xamarin. Forms Controls on Each Platform")](images/ch01fg03-large.png#lightbox "xamarin. Forms Controls on Each Platform")

Gli screenshot da sinistra a destra mostrano un iPhone e un telefono Android:

In ogni schermata, la pagina contiene un xamarin. Forms [ `Label` ](xref:Xamarin.Forms.Label) per la visualizzazione di testo, una [ `Button` ](xref:Xamarin.Forms.Button) per l'inizializzazione di azioni, un [ `Switch` ](xref:Xamarin.Forms.Switch) per scelta di un valore di attivazione/disattivazione e un [ `Slider` ](xref:Xamarin.Forms.Slider) per specificare un valore all'interno di un intervallo continuo. Tutti e quattro le visualizzazioni sono elementi figlio di un [ `StackLayout` ](xref:Xamarin.Forms.StackLayout) su un [ `ContentPage` ](xref:Xamarin.Forms.ContentPage).

Anche collegati alla pagina è una barra degli strumenti di xamarin. Forms costituito da diversi [ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem) oggetti. Questi sono visibili come le icone nella parte superiore di iOS e Android schermate e nella parte inferiore della schermata di Windows 10 Mobile.

Xamarin. Forms supporta anche XAML, l'Extensible Application Markup Language sviluppate presso Microsoft per diverse piattaforme applicative. Tutti gli oggetti visivi del programma illustrato in precedenza sono definiti in XAML, come illustrato nel [ **PlatformVisuals** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01/PlatformVisuals) esempio.

Un programma di xamarin. Forms possa determinare in quale piattaforma in cui viene eseguito ed eseguire codice diverso di conseguenza. Più visivo, gli sviluppatori possono scrivere codice personalizzato per le varie piattaforme ed eseguire il codice da un programma di xamarin. Forms in modo indipendente dalla piattaforma. Gli sviluppatori possono anche creare ulteriori controlli mediante la scrittura di renderer per ogni piattaforma.

Sebbene xamarin. Forms è un'ottima soluzione per applicazioni line-of-business, o per la creazione di prototipi, o effettuare una rapida dimostrazione di proof-of-concept, è meno ideale per le applicazioni che richiedono la grafica vettoriale o l'interazione touch complesse.

## <a name="your-development-environment"></a>L'ambiente di sviluppo

L'ambiente di sviluppo dipende quali piattaforme di destinazione e quali computer si desidera utilizzare.

Se si vuole usare iOS come destinazione, è necessario un Mac con Xcode e la piattaforma Xamarin installati. Anche il supporto Android richiede l'installazione Java e degli SDK necessari. È possibile assegnare iOS e Android con Visual Studio per Mac.

Installazione di Visual Studio consente in un computer di destinazione tutte le piattaforme Windows, Android e iOS. Tuttavia, destinate a iOS da Visual Studio richiede comunque un Mac con Xcode e la piattaforma Xamarin installati.

È possibile testare i programmi in un dispositivo reale connesso tramite USB al computer o in un simulatore.

## <a name="installation"></a>Installazione

Prima di creare e compilare un'applicazione xamarin. Forms, si deve provare a creare e compilare separatamente, un'applicazione iOS, un'applicazione Android e un'applicazione UWP, a seconda di piattaforme di destinazione e l'ambiente di sviluppo.

I siti web di Xamarin e Microsoft contengono informazioni su come eseguire questa operazione:

- [Introduzione a iOS](~/ios/get-started/index.md)
- [Introduzione ad Android](~/android/get-started/index.md)
- [Windows Dev Center](http://dev.windows.com)

Una volta è possibile creare ed eseguire i progetti per le singole piattaforme, si dovrebbe presentare alcun problema di creazione ed esecuzione di un'applicazione xamarin. Forms.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 1 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch01-Apr2016.pdf)
- [Esempio di capitolo 1](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)
