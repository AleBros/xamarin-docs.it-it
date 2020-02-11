---
title: Panoramica sulla compilazione di applicazioni multipiattaforma
description: Questo documento fornisce una panoramica di alto livello della creazione di applicazioni multipiattaforma. Viene illustrato il valore di C#, modelli di progettazione come MVC/MVVM e interfacce utente Native.
ms.prod: xamarin
ms.assetid: E442EEFB-FA9C-40E9-9668-5A3F915C8400
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 7d839e0141f14f4ba86897b128bf2a8c0a79548d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016921"
---
# <a name="building-cross-platform-applications-overview"></a>Panoramica sulla compilazione di applicazioni multipiattaforma

Questa guida introduce la piattaforma Xamarin e illustra come progettare un'applicazione multipiattaforma per ottimizzare il riutilizzo del codice e offrire un'esperienza nativa di qualità elevata su tutte le principali piattaforme per dispositivi mobili: iOS, Android e Windows Phone.

L'approccio usato in questo documento è in genere applicabile sia alle app di produttività che alle app di gioco. Tuttavia, l'obiettivo è la produttività e l'utilità (applicazioni non di gioco). Vedere il [documento introduttivo a monogame](~/graphics-games/monogame/introduction/index.md) o consultare [Visual Studio Tools per Unity](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-tools-for-unity) per le linee guida per lo sviluppo di giochi multipiattaforma.

La frase "Write-Once, run everywhere" viene spesso usata per esaltate le virtù di una singola codebase che viene eseguita senza modifiche su più piattaforme. Sebbene abbia il vantaggio di riutilizzare il codice, questo approccio spesso conduce a applicazioni con un set di funzionalità più basso comune e un'interfaccia utente generica che non si adatta perfettamente alle piattaforme di destinazione.

Xamarin non è solo una piattaforma "Write-Once, run everywhere", perché uno dei suoi punti di forza è la possibilità di implementare interfacce utente Native in modo specifico per ogni piattaforma. Tuttavia, con la progettazione riflessiva è ancora possibile condividere la maggior parte del codice dell'interfaccia non utente e ottenere il meglio di entrambi i mondi: scrivere il codice di archiviazione dei dati e della logica di business una volta e presentare le interfacce utente Native in ogni piattaforma. In questo documento viene descritto un approccio architetturale generale per raggiungere questo obiettivo.

Ecco un riepilogo dei punti chiave per la creazione di app multipiattaforma Xamarin:

- **Usare C#**  : scrivere le app in C#. Il codice esistente scritto C# in può essere trasferito a iOS e Android usando Xamarin in modo molto semplice e ovviamente usato nelle app di Windows.
- Usare i **modelli di progettazione MVC o MVVM** : sviluppare l'interfaccia utente dell'applicazione usando il modello di modello/vista/controller. Progettare l'applicazione usando un approccio modello/vista/controller o un approccio modello/vista/ViewModel in cui esiste una netta separazione tra il "modello" e il resto. Determinare quali parti dell'applicazione utilizzeranno gli elementi dell'interfaccia utente nativa di ogni piattaforma (iOS, Android, Windows, Mac) e usarli come linee guida per suddividere l'applicazione in due componenti: "core" e "interfaccia utente".
- **Compilazione di interfacce utente Native** : ogni applicazione specifica del sistema operativo fornisce un livello di interfaccia utente diverso C# (implementato in con l'assistenza degli strumenti di progettazione dell'interfaccia utente nativi):

1. In iOS usare le API UIKit per creare applicazioni di aspetto nativo, usando facoltativamente la finestra di progettazione iOS di Xamarin per creare visivamente l'interfaccia utente.
1. In Android usare Android. views per creare applicazioni di aspetto nativo, sfruttando la finestra di progettazione dell'interfaccia utente di Xamarin.
1. In Windows verrà usato XAML per il livello di presentazione, creato in Visual Studio o nella finestra di progettazione dell'interfaccia utente di Blend.
1. In Mac si useranno gli storyboard per il livello di presentazione, creato in Xcode.

I progetti Xamarin.Forms sono supportati in tutte le piattaforme e consentono di creare interfacce utente che possono essere condivise tra piattaforme usando il codice XAML di Xamarin.Forms. 

La quantità di riutilizzo del codice dipende in larga misura dalla quantità di codice che viene mantenuta nel Core condiviso e dalla quantità di codice specifico dell'interfaccia utente. Il codice principale è qualsiasi elemento che non interagisce direttamente con l'utente, ma fornisce invece servizi per parti dell'applicazione che raccoglieranno e visualizzeranno queste informazioni.

Per aumentare la quantità di riutilizzo del codice, è possibile adottare componenti multipiattaforma che forniscono servizi comuni tra tutti questi sistemi, ad esempio:

1. [Sqlite-net](https://www.nuget.org/packages/sqlite-net-pcl/) per l'archiviazione SQL locale,
1. [Plug](https://xamarin.com/plugins) -in Xamarin per l'accesso a funzionalità specifiche del dispositivo, tra cui la fotocamera, i contatti e la georilevazione
1. [Pacchetti NuGet](https://nuget.org) compatibili con i progetti Xamarin, ad esempio [JSON.NET](https://www.nuget.org/packages/Newtonsoft.Json/),
1. Uso delle funzionalità di .NET Framework per la rete, i servizi Web, i/o e altro ancora.

Alcuni di questi componenti sono implementati nella case study *Tasky* .

 <a name="Separate_Reusable_Code_into_a_Core_Library" />

## <a name="separate-reusable-code-into-a-core-library"></a>Separare il codice riutilizzabile in una libreria principale

Seguendo il principio di separazione delle responsabilità attraverso l'architettura dell'applicazione e spostando la funzionalità di base indipendente dalla piattaforma in una libreria principale riutilizzabile, è possibile ottimizzare la condivisione del codice tra le piattaforme, come illustrato nella figura seguente. illustra

 ![](overview-images/layers2.png "By following the principle of separation of responsibility by layering your application architecture and then moving core functionality that is platform agnostic into a reusable core library, you can maximize code sharing across platforms")

 <a name="Case_Studies" />

## <a name="case-studies"></a>Case Study

È presente un case study che accompagna questo documento, *Tasky Pro*. Ogni case study illustra l'implementazione dei concetti descritti in questo documento in un esempio reale. Il codice è open source ed è disponibile su [GitHub](https://github.com/xamarin/mobile-samples/).
