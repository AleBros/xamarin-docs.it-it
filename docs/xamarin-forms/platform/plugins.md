---
title: Plug-in
description: Aggiungere facilmente funzionalità native per le app xamarin. Forms
ms.prod: xamarin
ms.assetid: 8A06A420-A9D0-4BCB-B9AF-3AEA6A648A8B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/07/2016
ms.openlocfilehash: 5770d13c46998872752820b7a0cbb222a04c3ff8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="plugins"></a>Plug-in

Sono disponibili molte funzionalità di piattaforma nativa che esistono in tutte le piattaforme, ma dispongono di API leggermente diverse. Gli sviluppatori di scrivere plug-in per creare un'interfaccia multipiattaforma astratta per le funzionalità possono inoltre condividere con altri utenti.

Queste funzionalità includono: stato della batteria, compass, sensori di movimento, georilevazione, sintesi vocale e molti altri. I plug-in consente di queste funzionalità per essere facilmente utilizzati dalle applicazioni di xamarin. Forms.

## <a name="finding-and-adding-plugins"></a>Ricerca e aggiunta di plug-in

La community di Xamarin creati molti plug-in multipiattaforma compatibile con xamarin. Forms - una raccolta di grandi dimensioni è reperibile in:

[**Plug-in Xamarin**](https://github.com/xamarin/plugins)

Per una Guida all'aggiunta di pacchetti NuGet al progetto, vedere la procedura dettagliata su [incluso un pacchetto NuGet nel progetto](/visualstudio/mac/nuget-walkthrough/).


## <a name="creating-plugins"></a>Creazione di plug-in

È anche possibile creare e pubblicare i plug-in personalizzati come pacchetti Nuget (e i componenti di Xamarin). Molti plug-in esistente sono open source, pertanto è possibile esaminare il codice per comprendere come sono stati writtern.

Ad esempio, l'elenco dei plug-in di seguito sono open source di tutti e che corrispondano agli esempi nel [ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md) sezione:

- **Sintesi vocale** da James Montemagno &ndash; [GitHub](https://github.com/jamesmontemagno/Xamarin.Plugins/tree/master/TextToSpeech) e [NuGet](https://www.nuget.org/packages/Xam.Plugin.Battery)
- **Stato della batteria** da James Montemagno &ndash; [GitHub](https://github.com/jamesmontemagno/Xamarin.Plugins/tree/master/Battery) e [NuGet](https://www.nuget.org/packages/Xam.Plugins.TextToSpeech/)

Nei progetti di Github garantiti un buon punto di partenza per la creazione di più piattaforme plug-in personalizzati, seguire queste istruzioni per [la creazione di un plug-in per Xamarin](https://github.com/xamarin/plugins#create-a-plugin-for-xamarin).

### <a name="structuring-cross-platform-plugin-projects"></a>Strutturare i progetti di plug-in multipiattaforma

Anche se non esistono requisiti per la progettazione di un pacchetto NuGet particolari, esistono alcune linee guida per la creazione di un pacchetto per le app multipiattaforma.

In genere un plug-in multipiattaforma deve includere i componenti seguenti:

- Libreria di classi Portabile con un'interfaccia che rappresenta l'API per il plug-in,
- iOS, Android e Windows librerie di classi con un'implementazione dell'interfaccia.

Lettura James Montemagno [post di blog](https://blog.xamarin.com/creating-reusable-plugins-for-xamarin-forms/) che descrive il processo di creazione di plug-in per Xamarin.

È un preferibile evitare di fare riferimento a xamarin. Forms direttamente da un plug-in.
Questo può creare problemi di conflitto di versione durante il tentativo di utilizzare il plug-in altri sviluppatori. Provare invece a progettare l'API in modo che può essere utilizzato da qualsiasi applicazione .NET o di Xamarin.

### <a name="publishing-nuget-packages"></a>Pubblicazione di pacchetti NuGet

Pacchetti NuGet sono un **nuspec** file, ovvero un file xml che definisce quali parti del progetto vengono pubblicate nel pacchetto. Il **nuspec** file include anche informazioni sul pacchetto, ad esempio id, titolo e autori.

Vedere [documentazione di NuGet](http://docs.nuget.org/create/creating-and-publishing-a-package) per ulteriori informazioni sulla creazione e pubblicazione di pacchetti NuGet.


## <a name="related-links"></a>Collegamenti correlati

- [Creazione di plug-in riutilizzabili per xamarin. Forms](https://blog.xamarin.com/creating-reusable-plugins-for-xamarin-forms)
- [Plug-in utilizzando gli & lo sviluppo di Xamarin (video)](https://university.xamarin.com/guestlectures/using-developing-plugins-for-xamarin)
