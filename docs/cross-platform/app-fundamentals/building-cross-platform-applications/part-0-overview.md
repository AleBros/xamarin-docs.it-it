---
title: Compilazione incrociata panoramica delle applicazioni della piattaforma
ms.topic: article
ms.prod: xamarin
ms.assetid: E442EEFB-FA9C-40E9-9668-5A3F915C8400
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 8e7b25f615939c0ae902e09c728615188d1cb86e
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="building-cross-platform-applications-overview"></a>Compilazione incrociata panoramica delle applicazioni della piattaforma

Questa guida vengono presentate sulla piattaforma Xamarin e come progettare un'applicazione multipiattaforma per ottimizzare riutilizzo del codice e offrire un'esperienza nativa di alta qualità in tutte le principali piattaforme mobili: iOS, Android e Windows Phone.

L'approccio adottato in questo documento riguarda in genere sia applicazioni per la produttività e le app di gioco, ma lo stato attivo è sulla produttività e utilità (non gioco applicazioni). Vedere il [Introduzione al documento MonoGame](https://developer.xamarin.com/guides/cross-platform/game_development/monogame/introduction/) o estrarlo [Visual Studio Tools per Unity](https://docs.microsoft.com/en-us/visualstudio/cross-platform/visual-studio-tools-for-unity) per linee guida di sviluppo di giochi multipiattaforma.

La frase "scrivere-una volta, eseguire everywhere" viene spesso utilizzato per esaltate virtù di una singola codebase che viene eseguito senza modificarli su più piattaforme. Che ha il vantaggio di riutilizzo del codice, tale approccio comporta problemi di applicazioni che dispongono di un set di funzionalità minimo comune denominatore e un'interfaccia utente di ricerca generico che non si adatta perfettamente in qualsiasi le piattaforme di destinazione.

Xamarin non è semplicemente un "scrittura-una volta, eseguire everywhere" piattaforma, perché uno dei suoi punti di forza la possibilità di implementare le interfacce utente native in modo specifico per ogni piattaforma. Tuttavia, con una progettazione attenta affinché è comunque possibile condividere la maggior parte dell'utente non-codice dell'interfaccia e ottenere il meglio di entrambi: scrivere codice la logica di business e di archiviazione di dati una volta e presentare le interfacce utente native in ciascuna piattaforma. Questo documento viene illustrato un approccio dell'architettura generale per raggiungere questo obiettivo.

Di seguito è riportato un riepilogo dei punti chiave per la creazione di App multipiattaforma con Xamarin:

-   **Usare c#** -scrivere applicazioni in c#. Codice esistente scritto in c# può essere trasferito a iOS e Android con Xamarin in modo molto semplice ovviamente usato nelle app di Windows
-   **Utilizzare i modelli di progettazione MVC o MVVVM** -sviluppare l'interfaccia utente dell'applicazione usando il modello di visualizzazione/modello/Controller. Progettare l'applicazione utilizzando un approccio di modellazione/visualizzazione/Controller o un approccio di modello/visualizzazione/ViewModel in cui è presente una netta separazione tra il "modello" e il resto. Determinare quali parti dell'applicazione utilizzeranno gli elementi dell'interfaccia utente nativa di tutte le piattaforme (iOS, Android, Windows, Mac) e usarlo come linea guida per suddivisa in due componenti: "Core" e "Interfaccia".
-   **Compilare interfacce utente native** -ogni applicazione specifiche del sistema operativo fornisce un livello di interfaccia utente diversa (implementato in c# con l'assistenza degli strumenti di progettazione dell'interfaccia utente nativi):

1.  In iOS, utilizzare le APIs UIKit per creare applicazioni nativo, facoltativamente utilizzando Progettazione iOS di Xamarin per creare visivamente l'interfaccia utente.
1.  In Android, utilizzare Android.Views per creare applicazioni nativo, sfruttando la possibilità di progettazione dell'interfaccia utente di Xamarin.
1.  In Windows si utilizzerà XAML per il livello di presentazione, creato nella finestra di progettazione dell'interfaccia utente di Visual Studio o Blend.
1.  In Mac, si utilizzeranno gli storyboard per il livello di presentazione, creato in Xcode.

Xamarin. Forms progetti sono supportati in tutte le piattaforme e consentono di che creare interfacce utente che possono essere condivisa tra le piattaforme tramite XAML xamarin. Forms. 

La quantità di riutilizzo del codice dipende in larga misura la quantità di codice viene mantenuta in core condiviso e la quantità di codice dell'interfaccia utente specifico. Il codice di base è tutto ciò che non interagisce direttamente con l'utente, ma lo fornisce servizi per le parti dell'applicazione che raccoglierà e visualizzare queste informazioni.

Per aumentare la quantità di riutilizzo del codice, è possibile adottare multipiattaforma componenti che forniscono servizi comuni a tutti questi sistemi, ad esempio:

1.   [SQLite net](https://www.nuget.org/packages/sqlite-net-pcl/) per l'archiviazione locale di SQL,
1.   [Xamarin Plugins](https://xamarin.com/plugins) per l'accesso a funzionalità specifiche di dispositivo inclusi fotocamera, contatti e geolocation,
1.   [Pacchetti NuGet](https://nuget.org) che sono compatibili con i progetti di Xamarin, ad esempio [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/),
1.  Utilizzando le funzionalità di .NET framework per la rete, servizi web, i/o e altro ancora.


Alcuni di questi componenti sono implementati nel *Tasky* case study di.

 <a name="Separate_Reusable_Code_into_a_Core_Library" />


## <a name="separate-reusable-code-into-a-core-library"></a>Separazione del codice riutilizzabile in una libreria di base

Seguendo il principio di separazione delle responsabilità sovrapposizione l'architettura dell'applicazione e quindi spostando le funzionalità di base che sono indipendente dalla piattaforma in una libreria riutilizzabile di core, è possibile ottimizzare codice condiviso tra le piattaforme, come illustrato nella figura riportata di seguito di seguito viene illustrato:

 ![](part-0-overview-images/layers2.png "Seguendo il principio di separazione delle responsabilità sovrapposizione l'architettura dell'applicazione e quindi spostando le funzionalità di base che sono indipendente dalla piattaforma in una libreria riutilizzabile di core, è possibile ottimizzare codice condiviso tra le piattaforme")

 <a name="Case_Studies" />


## <a name="case-studies"></a>Case study

È un case study che accompagna questo documento: *Tasky Pro*. Ogni case study di viene illustrata l'implementazione dei concetti descritti in questo documento in un esempio reale. Il codice è open source e disponibile in [github](https://github.com/xamarin/mobile-samples/).