---
title: Riassunto del capitolo 1. Scopi di Xamarin.Forms
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 1. Scopi di Xamarin.Forms'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F3F864FF-EE70-49D0-90D1-388889037625
author: davidbritch
ms.author: dabritch
ms.date: 07/19/2018
ms.openlocfilehash: 6dfa473bdfb4c1dd88ca833dbf5011a0bbdec42a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73032890"
---
# <a name="summary-of-chapter-1-how-does-xamarinforms-fit-in"></a>Riassunto del capitolo 1. Scopi di Xamarin.Forms

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)

> [!NOTE]
> Le note in questa pagina indicano le aree in cui Xamarin.Forms si è discostata dal materiale presentato nel libro.

Uno dei lavori più spiacevoli nella programmazione è il porting di una base di codice da una piattaforma all'altra, in particolare se tale piattaforma coinvolge un linguaggio di programmazione diverso. C'è una tentazione quando si esegue il porting del codice per effettuare il refactoring, ma se entrambe le piattaforme devono essere mantenute in parallelo, le differenze tra le due basi di codice renderanno più difficile la manutenzione futura.

## <a name="cross-platform-mobile-development"></a>Sviluppo di app per dispositivi mobili multipiattaforma

Questo problema è comune quando si prendono di mira le piattaforme mobili. Attualmente, esistono due principali piattaforme mobili, la famiglia Apple di iPhone e iPad che eseguono il sistema operativo iOS e il sistema operativo Android che funziona su una varietà di telefoni e tablet. Un'altra piattaforma significativa è la piattaforma Windows universale (UWP) di Microsoft, che consente a un singolo programma di indirizzare sia Windows 10.

Un fornitore di software che desidera indirizzare queste piattaforme deve affrontare diversi paradigmi dell'interfaccia utente, tre diversi ambienti di sviluppo, tre diverse interfacce di programmazione e&mdash;forse più goffamente&mdash;tre diversi linguaggi di programmazione: Objective-C per iPhone e iPad, Java per Android e C .

## <a name="the-c-and-net-solution"></a>La soluzione .NET e C

Anche se Objective-C, Java e C, sono tutti derivati dal linguaggio di programmazione C, si sono evoluti da percorsi molto diversi. Il linguaggio C è il più recente ed è stato maturing in modi molto utili. Inoltre, il linguaggio Cè è strettamente associato a un'intera infrastruttura di programmazione denominata .NET, che fornisce supporto per la matematica, il debug, la reflection, le raccolte, la globalizzazione, l'I/O dei file, la rete, la sicurezza, il threading, i servizi Web, la gestione dei dati e la lettura e la scrittura XML e JSON.

Xamarin attualmente fornisce strumenti per il Mac nativo, iOS e Android API utilizzando C . Questi strumenti sono chiamati Xamarin.Mac, Xamarin.iOS e Xamarin.Android, collettivamente conosciuti come la piattaforma Xamarin. Si tratta di librerie e associazioni che esprimono le API native di queste piattaforme con idiomi .NET.

Gli sviluppatori possono usare la piattaforma Xamarin per scrivere le applicazioni in C , che hanno come destinazione Mac, iOS o Android. Ma quando si mira più di una piattaforma, ha molto senso condividere parte del codice tra le piattaforme di destinazione. Ciò comporta la separazione del programma in codice dipendente dalla piattaforma (in genere che coinvolge l'interfaccia utente) e codice indipendente dalla piattaforma, che in genere richiede solo il framework .NET di base. Questo codice indipendente dalla piattaforma può risiedere in una libreria di classi portabile (PCL, Portable Class Library) o in un progetto condiviso, spesso denominato progetto di asset condiviso o SAP.

> [!NOTE]
> Librerie di classi portabili sono state sostituite da librerie .NET Standard.Portable Class Libraries have been replaced by .NET Standard libraries. Tutto il codice di esempio del libro è stato convertito per utilizzare librerie standard .NET.

## <a name="introducing-xamarinforms"></a>Presentazione di Xamarin.Forms

Quando scegli come target più piattaforme mobili, Xamarin.Forms consente una condivisione ancora maggiore del codice. Un singolo programma scritto per Xamarin.Forms può essere destinato a queste piattaforme:

- iOS per programmi eseguiti su iPhone, iPad e iPod touch
- Android per programmi eseguiti su telefoni e tablet Android
- la piattaforma Windows universale di destinazione di Windows 10

> [!NOTE]
> Xamarin.Forms non supporta più Windows 8.1, Windows Phone 8.1 o Windows 10 Mobile, ma le applicazioni Xamarin.Forms vengono eseguite sul desktop di Windows 10. È inoltre disponibile il supporto per l'anteprima per le piattaforme [Mac](~/xamarin-forms/platform/other/mac.md), [WPF](~/xamarin-forms/platform/other/wpf.md), [GTK](~/xamarin-forms/platform/other/gtk.md)e [Tizen](~/xamarin-forms/platform/other/tizen.md) .

La maggior parte di un programma Xamarin.Forms è presente in una libreria o in un SAP. Ognuna delle piattaforme è costituita da un piccolo stub dell'applicazione che chiama in questo codice condiviso.

Le API Xamarin.Forms eseguono il mapping ai controlli nativi in ogni piattaforma, in modo che ogni piattaforma mantenga l'aspetto caratteristico:

[![Triplo screenshot della condivisione degli oggetti visivi della piattaforma](images/ch01fg03-small.png "Xamarin.Forms Controlli su ogni piattaforma")](images/ch01fg03-large.png#lightbox "Xamarin.Forms Controlli su ogni piattaforma")

Gli screenshot da sinistra a destra mostrano un iPhone e un telefono Android:

In ogni schermata, la pagina contiene [`Label`](xref:Xamarin.Forms.Label) un file Xamarin.Forms per la visualizzazione del testo, un [`Button`](xref:Xamarin.Forms.Button) oggetto per l'imposizione di azioni, un [`Switch`](xref:Xamarin.Forms.Switch) per la scelta di un valore on/off e un [`Slider`](xref:Xamarin.Forms.Slider) per specificare un valore all'interno di un intervallo continuo. Tutti e quattro i punti [`StackLayout`](xref:Xamarin.Forms.StackLayout) di [`ContentPage`](xref:Xamarin.Forms.ContentPage)vista sono figli di un su un .

Alla pagina è inoltre associata una barra degli strumenti [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) Xamarin.Forms costituita da diversi oggetti. Questi sono visibili come icone nella parte superiore delle schermate iOS e Android e nella parte inferiore dello schermo di Windows 10 Mobile.

Xamarin.Forms supporta anche XAML, l'Extensible Application Markup Language sviluppato in Microsoft per diverse piattaforme applicative. Tutti gli oggetti visivi del programma illustrato in precedenza sono definiti in XAML come illustrato nell'esempio [**PlatformVisuals.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01/PlatformVisuals)

Un programma Xamarin.Forms può determinare su quale piattaforma è in esecuzione ed eseguire codice diverso di conseguenza. Più potente, gli sviluppatori possono scrivere codice personalizzato per le varie piattaforme ed eseguire tale codice da un programma Xamarin.Forms in modo indipendente dalla piattaforma. Gli sviluppatori possono anche creare controlli aggiuntivi scrivendo renderer per ogni piattaforma.

Mentre Xamarin.Forms è una buona soluzione per applicazioni line-of-business, per la prototipazione o per fare una rapida dimostrazione proof-of-concept, è meno ideale per le applicazioni che richiedono grafica vettoriale o complesse interazioni touch.

## <a name="your-development-environment"></a>Il tuo ambiente di sviluppo

L'ambiente di sviluppo dipende dalle piattaforme di destinazione e dai computer che si desidera utilizzare.

Se vuoi rivolgerti a iOS, avrai bisogno di un Mac con Xcode e la piattaforma Xamarin installata. Il supporto di Android richiede anche l'installazione di Java e degli SDK necessari. È quindi possibile scegliere come destinazione iOS e Android usando Visual Studio per Mac.You can then target both iOS and Android using Visual Studio for Mac.

L'installazione di Visual Studio consente al PC di indirizzare iOS, Android e tutte le piattaforme Windows. Tuttavia, la destinazione iOS da Visual Studio richiede ancora un Mac con Xcode e la piattaforma Xamarin installata.

È possibile testare i programmi su un dispositivo effettivo collegato tramite USB al computer o su un simulatore.

## <a name="installation"></a>Installazione

Prima di creare e compilare un'applicazione Xamarin.Forms, è consigliabile provare a creare e compilare separatamente un'applicazione iOS, un'applicazione Android e un'applicazione UWP, a seconda delle piattaforme di destinazione e dell'ambiente di sviluppo.

I siti Web Xamarin e Microsoft contengono informazioni su come eseguire questa operazione:

- [Introduzione a iOS](~/ios/get-started/index.md)
- [Introduzione ad Android](~/android/get-started/index.md)
- [Windows Dev Center](https://dev.windows.com)

Una volta che è possibile creare ed eseguire progetti per queste singole piattaforme, non si dovrebbe avere problemi a creare ed eseguire un'applicazione Xamarin.Forms.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 1 testo completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch01-Apr2016.pdf)
- [Capitolo 1 esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)
