---
title: Building cross-Platform Applications Overview
description: Questo documento fornisce una panoramica generale della creazione di applicazioni multipiattaforma. Viene descritto il valore di C#, Progettazione modelli, ad esempio MVC/MVVM e interfacce utente native.
ms.prod: xamarin
ms.assetid: E442EEFB-FA9C-40E9-9668-5A3F915C8400
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 1eb308e0095c29d8ab0d0bdf1f74b807fd2ab97f
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61275620"
---
# <a name="building-cross-platform-applications-overview"></a>Building cross-Platform Applications Overview

Questa guida illustra la piattaforma Xamarin e su come progettare un'applicazione multi-piattaforma per ottimizzare riutilizzare il codice e offrire un'esperienza nativa di qualità elevata su tutte le principali piattaforme per dispositivi mobili: iOS, Android e Windows Phone.

L'approccio usato in questo documento è generalmente applicabile all'App per la produttività sia alle app di gioco, tuttavia, lo stato attivo è sulla produttività e utilità (non-gioco applicazioni). Vedere le [Introduzione a MonoGame documento](~/graphics-games/monogame/introduction/index.md) o scoprire [Visual Studio Tools per Unity](https://docs.microsoft.com/visualstudio/cross-platform/visual-studio-tools-for-unity) per linee guida di sviluppo di giochi multipiattaforma.

La frase "write-una volta, eseguiti ovunque" viene spesso usata per decantando le virtù di una singola codebase che viene eseguito senza modifiche su più piattaforme. Anche se ha il vantaggio di riutilizzare il codice, tale approccio spesso per le applicazioni con un set di funzionalità minimo comune denominatore e un'interfaccia utente dall'aspetto generico che non rientra perfettamente in una qualsiasi delle piattaforme di destinazione.

Xamarin non è semplicemente un "scrittura-una volta, eseguiti ovunque" piattaforma, perché uno dei suoi punti di forza è la possibilità di implementare le interfacce utente native in modo specifico per ogni piattaforma. Tuttavia, con una progettazione attenta è comunque possibile condividere la maggior parte dell'utente non-codice dell'interfaccia e il meglio di entrambi i mondi: scrivere una sola volta il codice dei dati per la logica di archiviazione e di business e presentare interfacce utente native in ciascuna piattaforma. Questo documento illustra un approccio architettura generico per raggiungere questo obiettivo.

Ecco un riepilogo dei punti chiave per la creazione di App multipiattaforma con Xamarin:

-   **Usare C#**  -scrivere le app C#. Il codice esistente scritto in C# può essere trasferito in iOS e Android usando Xamarin in modo molto semplice e ovviamente utilizzato nelle app di Windows.
-   **Usare i modelli di progettazione MVC o MVVM** -sviluppo di interfaccia utente dell'applicazione usando il modello di visualizzazione/Controller/modello. Progettare l'applicazione usando un approccio di modello/View/ViewModel o un approccio di visualizzazione/Controller/modello in cui è presente una netta separazione tra il "modello" e il resto. Determinare quali parti dell'applicazione verrà usando elementi dell'interfaccia utente nativo di ogni piattaforma (iOS, Android, Windows, Mac) e usarlo come linea guida per suddivisa in due componenti: "Core" e "Interfaccia utente".
-   **Compilare interfacce utente native** -ogni applicazione specifici del sistema operativo fornisce un livello di interfaccia utente diversa (implementato in C# strumenti di progettazione con l'aiuto dell'interfaccia utente nativa):

1.  In iOS, usare le APIs UIKit per creare applicazioni native accattivanti, facoltativamente utilizzando Progettazione iOS di Xamarin per creare visivamente un'interfaccia utente.
1.  In Android, usare Android.Views per creare applicazioni native accattivanti, sfruttando i vantaggi della finestra di progettazione dell'interfaccia utente di Xamarin.
1.  In Windows si utilizzerà XAML per il livello di presentazione, creato nella finestra di progettazione dell'interfaccia utente di Visual Studio o Blend.
1.  In Mac, si userà gli storyboard per il livello di presentazione, creato in Xcode.

I progetti xamarin. Forms sono supportati in tutte le piattaforme e consentono di che creare interfacce utente che possono essere condivisi tra piattaforme mediante l'utilizzo di XAML di xamarin. Forms. 

La quantità di riutilizzo del codice dipenderà in larga misura la quantità di codice viene mantenuta nel codice condiviso e la quantità di codice è l'interfaccia utente specifico. Il codice di base è tutto ciò che non interagisce direttamente con l'utente, ma fornisce servizi per le parti dell'applicazione che raccoglierà e visualizzare queste informazioni.

Per aumentare la quantità di riutilizzo del codice, è possibile adottare lo sviluppo multipiattaforma componenti che forniscono servizi comuni tra tutti questi sistemi, ad esempio:

1.   [SQLite-net](https://www.nuget.org/packages/sqlite-net-pcl/) per l'archiviazione locale di SQL,
1.   [Xamarin Plugins](https://xamarin.com/plugins) per l'accesso a funzionalità specifiche del dispositivo, inclusi la fotocamera, contatti e georilevazione,
1.   [I pacchetti NuGet](https://nuget.org) che sono compatibili con i progetti Xamarin, ad esempio [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/),
1.  Utilizzo di funzionalità di .NET framework per la rete, servizi web, i/o e altro ancora.


Alcuni di questi componenti vengono implementati nel *Tasky* case study.

 <a name="Separate_Reusable_Code_into_a_Core_Library" />


## <a name="separate-reusable-code-into-a-core-library"></a>Separare il codice riutilizzabile in una libreria di base

Seguendo il principio di separazione di responsabilità sovrapposizione l'architettura dell'applicazione e quindi spostando le funzionalità principali sono indipendenti dalla piattaforma in una libreria di base riutilizzabile, è possibile ottimizzare la condivisione del codice tra piattaforme, come nella figura seguente viene illustrato:

 ![](overview-images/layers2.png "Seguendo il principio di separazione di responsabilità sovrapposizione l'architettura dell'applicazione e quindi spostando le funzionalità principali sono indipendenti dalla piattaforma in una libreria di base riutilizzabile, è possibile ottimizzare la condivisione del codice tra piattaforme")

 <a name="Case_Studies" />


## <a name="case-studies"></a>Case study

È presente un case study che accompagna questo documento – *Tasky Pro*. Ogni case study vengono esaminati l'implementazione dei concetti descritti in questo documento in un esempio reale. Il codice è open source e disponibile sulla [github](https://github.com/xamarin/mobile-samples/).
