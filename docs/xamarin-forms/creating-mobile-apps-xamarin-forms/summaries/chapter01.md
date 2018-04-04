---
title: Riepilogo del capitolo 1. La modalità di utilizzo xamarin. Forms
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: F3F864FF-EE70-49D0-90D1-388889037625
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 534c36a16acdc10ffb6f6b6703296a672875286e
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="summary-of-chapter-1-how-does-xamarinforms-fit-in"></a>Riepilogo del capitolo 1. La modalità di utilizzo xamarin. Forms

Uno dei processi più spiacevoli nella programmazione consiste nel porting di un codice di base da una piattaforma a un'altra, in particolare se tale piattaforma prevede un linguaggio di programmazione diverso. Che è durante il porting del codice per il refactoring nonché, ma se entrambe le piattaforme devono essere mantenute in parallelo, quindi le differenze tra i due codebase renderà manutenzione future più difficile.

## <a name="cross-platform-mobile-development"></a>Sviluppo mobile multipiattaforma

Questo problema è comune quando la destinazione delle piattaforme per dispositivi mobili. Attualmente, esistono due principali piattaforme mobili, la famiglia di Apple iPhone e iPad che eseguono il sistema operativo iOS e il sistema operativo Android che viene eseguito su una vasta gamma di telefoni e Tablet. Un'altra piattaforma significativa è Microsoft Windows della piattaforma UWP (Universal), che consente a un singolo programma di destinazione sia Windows 10 e Windows 10 Mobile.

Un fornitore di software che si desidera questi tre le piattaforme di destinazione deve affrontare paradigmi di interfaccia utente diversa, tre gli ambienti di sviluppo diversi, tre interfacce di programmazione diversi, e&mdash;probabilmente più considerato&mdash; tre diversi linguaggi di programmazione: Objective-C per l'iPhone e iPad, Java per Android e in c# per Windows.

## <a name="the-c-and-net-solution"></a>La soluzione c# e .NET

Sebbene Objective-C, Java e c# sono derivati dal linguaggio di programmazione C, cui si sono evoluti dai percorsi molto diversi. In c# è la più recente di questi linguaggi ed è stata evoluzione in modi molto utili. Inoltre, in c# è strettamente associato con un'intera infrastruttura di programmazione .NET, che fornisce il supporto per la matematica, debug, reflection, raccolte, globalizzazione, i/o file, rete, sicurezza, threading, servizi web, la gestione dei dati e XML denominata e JSON, lettura e scrittura.

Attualmente, Xamarin offre strumenti per impostare come destinazione il Mac, iOS e Android API con c# e .NET native. Questi strumenti vengono chiamati Xamarin.Mac, xamarin. IOS e xamarin, noti collettivamente come sulla piattaforma Xamarin. Si tratta di librerie e le associazioni che esprimono le API native di queste piattaforme con i linguaggi .NET.

Gli sviluppatori possono utilizzare sulla piattaforma Xamarin per scrivere applicazioni in c# destinati a Mac, iOS o Android. Ma quando più di una piattaforma di destinazione, rende molto senso per condividere parte del codice tra le piattaforme di destinazione. Tale attività implica la separazione del programma in codice dipendente dalla piattaforma (che in genere comporta l'interfaccia utente) e codice indipendente dalla piattaforma, che in genere richiede solo base .NET framework. Questo codice indipendente dalla piattaforma può risiedere in una libreria di classe portabile (PCL) o un progetto condiviso, spesso denominata progetto Asset condiviso o SAP.

## <a name="introducing-xamarinforms"></a>Introduzione a xamarin. Forms

Quando la destinazione è più piattaforme per dispositivi mobili, xamarin. Forms consente di condividere il codice ulteriormente. Un singolo programma scritto per xamarin. Forms possa interagire con cinque piattaforme distinte:

- iOS per programmi eseguiti con l'iPhone, iPad e iPod touch
- Android per programmi che vengono eseguiti su telefoni e Tablet Android
- la piattaforma Windows universale per Windows 10 Mobile e di destinazione Windows 10
- Windows Runtime API di Windows 8.1
- Windows Runtime API di Windows Phone 8.1

I modelli di soluzioni xamarin. Forms correnti includono modelli di progetti per le piattaforme Windows 8.1 e Windows Phone 8.1.

La maggior parte di un programma xamarin. Forms esiste in una libreria di classi Portabile o a un SAP. Ognuna delle piattaforme è costituito da uno stub di piccola applicazione che chiama la libreria di classi Portabile. Eseguire il mapping le APIs Xamarin.Forms in controlli nativi in ciascuna piattaforma, in modo che ogni piattaforma mantiene il relativo aspetto caratteristica:

[![Schermata tripla degli oggetti visivi di piattaforma condivisione](images/ch01fg03-small.png "xamarin. Forms Controls on Each Platform")](images/ch01fg03-large.png#lightbox "xamarin. Forms Controls on Each Platform")

Le schermate da sinistra a destra mostrano un iPhone, un telefono Android e un telefono Windows 10 Mobile. In ogni schermata, la pagina contiene un xamarin. Forms [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) per la visualizzazione di testo, un [ `Button` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Button/) per l'inizializzazione di azioni, un [ `Switch` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Switch/) per scelta di un valore di attivazione/disattivazione e un [ `Slider` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Slider/) per specificare un valore all'interno di un intervallo continuo. Tutti e quattro le visualizzazioni sono elementi figlio di un [ `StackLayout` ](https://developer.xamarin.com/api/type/Xamarin.Forms.StackLayout/) su un [ `ContentPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ContentPage/).

Anche associata alla pagina è una barra degli strumenti di xamarin. Forms costituito da diversi [ `ToolbarItem` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/) oggetti. Questi sono visibili come le icone nella parte superiore di iOS e Android schermate e nella parte inferiore della schermata di Windows 10 Mobile.

Xamarin. Forms supporta anche XAML, Extensible Application Markup Language sviluppato da Microsoft per diverse piattaforme applicative. Tutti gli oggetti visivi del programma illustrato in precedenza sono definiti in XAML, come dimostrato nel [ **PlatformVisuals** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01/PlatformVisuals) esempio.

Un programma xamarin. Forms può determinare quale piattaforma in cui viene eseguito e pertanto di eseguire codice diversi. Più sfruttarne, gli sviluppatori possono scrivere codice personalizzato per diverse piattaforme e per eseguire il codice da un programma xamarin. Forms in modo indipendente dalla piattaforma. Gli sviluppatori possono inoltre creare ulteriori controlli scrivendo renderer per ogni piattaforma.

Sebbene xamarin. Forms è un'ottima soluzione per applicazioni line-of-business o per la creazione di prototipi, o la creazione di una rapida dimostrazione di prova, è meno ideale per applicazioni che richiedono la grafica vettoriale o l'interazione complessa tocco.

## <a name="your-development-environment"></a>Ambiente di sviluppo

Ambiente di sviluppo dipende da quali piattaforme di destinazione e le macchine da utilizzare.

Se si desidera iOS di destinazione, è necessario un Mac con Xcode e Xamarin piattaforma installata. Nonché di supporto Android richiede l'installazione di Java e gli SDK richiesti. È possibile assegnare iOS e Android usando Visual Studio per Mac.

Installazione di Visual Studio consente nel PC di destinazione tutte le piattaforme Windows, iOS e Android. Tuttavia, destinato a iOS da Visual Studio richiede comunque un Mac con Xcode e Xamarin piattaforma installata.

È possibile testare i programmi in un dispositivo reale connesso tramite USB nel computer o un simulatore.

## <a name="installation"></a>Installazione

Prima della creazione e compilazione di un'applicazione di xamarin. Forms, è consigliabile creare e compilare separatamente, un'applicazione iOS, un'applicazione Android e un'applicazione UWP, a seconda della piattaforme di cui che si desidera l'ambiente di sviluppo e di destinazione.

I siti web di Xamarin e Microsoft contengono informazioni su come eseguire questa operazione:

- [Introduzione a iOS](~/ios/get-started/index.md)
- [Introduzione a Android](~/android/get-started/index.md)
- [Windows Dev Center](http://dev.windows.com)

Una volta è possibile creare e l'esecuzione di progetti per le singole piattaforme, si dovrebbe presentare alcun problema di creazione ed esecuzione di un'applicazione di xamarin. Forms.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 1 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch01-Apr2016.pdf)
- [Esempio capitolo 1](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter01)
