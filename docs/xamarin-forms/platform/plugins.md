---
title: Utilizzo e la creazione di plug-in xamarin. Forms
description: Questo articolo illustra come usare e creare plug-in xamarin. Forms. I plug-in sono in genere utilizzata per esporre facilmente funzionalità di piattaforma nativa.
ms.prod: xamarin
ms.assetid: 8A06A420-A9D0-4BCB-B9AF-3AEA6A648A8B
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/05/2018
ms.openlocfilehash: 4d121c2dfcca380e1735da1a4ca47c42d1957b8a
ms.sourcegitcommit: ec50c626613f2f9af51a9f4a52781129bcbf3fcb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2018
ms.locfileid: "37854740"
---
# <a name="consuming-and-creating-xamarinforms-plugins"></a>Utilizzo e la creazione di plug-in xamarin. Forms

Sono disponibili molte funzionalità di piattaforma nativa che esistono in tutte le piattaforme, ma sono leggermente diverse API. Un modo per gli sviluppatori a usare queste funzionalità è necessario creare un'interfaccia astratta multipiattaforma e quindi implementa tale interfaccia in varie piattaforme. Quindi l'applicazione xamarin. Forms consente di accedere usando queste implementazioni della piattaforma [ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md).

Gli sviluppatori possono condividere questo lavoro scrivendo un _plug-in_ e pubblicandola in NuGet.

> [!NOTE]
> Numerose funzionalità multipiattaforma in precedenza disponibili solo tramite i plug-in fanno ora parte dell'open source **[Xamarin.Essentials](~/essentials/index.md)** libreria. Queste funzionalità includono: lo stato della batteria, bussola, sensori di movimento, georilevazione, sintesi vocale e molto altro ancora. In futuro **Xamarin.Essentials** sarà la fonte principale delle funzionalità di multi-piattaforma per le applicazioni xamarin. Forms. Anche se gli sviluppatori possono comunque creare e pubblicare i plug-in, prendere in considerazione l'aggiunta come contributo al **Xamarin.Essentials**.

## <a name="finding-and-adding-plugins"></a>Ricerca e aggiunta di plug-in

La community di Xamarin ha creato molti plug-in multipiattaforma compatibile con xamarin. Forms. Una raccolta di grandi dimensioni è reperibile in:

[**Plug-in Xamarin**](https://github.com/xamarin/XamarinComponents)

Per una Guida per l'aggiunta di pacchetti NuGet al progetto, vedere la procedura dettagliata sul [inserimento di un pacchetto NuGet nel progetto](/visualstudio/mac/nuget-walkthrough/).

## <a name="creating-plugins"></a>Creazione di plug-in

È anche possibile creare e pubblicare il proprio plug-in come pacchetti Nuget (e i componenti di Xamarin). Molti plug-in esistente sono open source in modo che è possibile esaminare il codice per comprendere il modo in cui sono stati writtern.

Ad esempio, l'elenco dei plug-in seguenti sono tutti open source e corrispondono a alcuni esempi per la [ `DependencyService` ](~/xamarin-forms/app-fundamentals/dependency-service/index.md) sezione:

- **Sintesi vocale** di James montemagno su &ndash; [GitHub](https://github.com/jamesmontemagno/TextToSpeechPlugin) e [NuGet  ](https://www.nuget.org/packages/Xam.Plugins.TextToSpeech)
- **Lo stato della batteria** di James montemagno su &ndash; [GitHub](https://github.com/jamesmontemagno/BatteryPlugin) e [NuGet](https://www.nuget.org/packages/Xam.Plugin.Battery)

Tali progetti Github possono fornire un buon punto di partenza per la creazione di cross-platform plug-in personalizzati, come seguire queste istruzioni per [creazione di un plug-in per Xamarin](https://github.com/xamarin/XamarinComponents#create-a-plugin-for-xamarin).

### <a name="structuring-cross-platform-plugin-projects"></a>Strutturare i progetti di plug-in multipiattaforma

Anche se non sono previsti requisiti particolari per la progettazione di un pacchetto NuGet, esistono alcune linee guida per la creazione di un pacchetto per le app multipiattaforma.

In passato, un plug-in multipiattaforma costituito in genere i componenti seguenti:

- Libreria di classi Portabile con un'interfaccia che rappresenta l'API per il plug-in,
- librerie con un'implementazione dell'interfaccia di classi iOS, Android e Universal Windows Platform (UWP).

Lettura James Montemagno [post di blog](https://blog.xamarin.com/creating-reusable-plugins-for-xamarin-forms/) che descrive il processo di creazione di plug-in per Xamarin.

Più di recente, i plug-in è possibile essere creare con una singola piattaforma di multitargeting. Questo approccio è descritto di James Montemagno [post di blog](https://montemagno.com/converting-xamarin-libraries-to-sdk-style-multi-targeted-projects/). Questo approccio viene usato nel plug-in di James Montemagno collegati indicati sopra, e viene inoltre utilizzato il formato **Xamarin.Essentials**.

È preferibile usare per evitare che fanno riferimento a xamarin. Forms direttamente da un plug-in.
Ciò può creare problemi di conflitto di versione durante il tentativo di usare il plug-in altri sviluppatori. Provare invece a progettare l'API in modo che può essere utilizzato da qualsiasi applicazione basata su Xamarin o .NET.

### <a name="publishing-nuget-packages"></a>Pubblicazione di pacchetti NuGet

I pacchetti NuGet hanno una **nuspec** file, ovvero un file xml che definisce quali parti del progetto vengono pubblicate nel pacchetto. Il **nuspec** file include anche informazioni sul pacchetto, ad esempio id, title e autori.

Visualizzare [documentazione di NuGet](/nuget/create-packages/creating-a-package.md) per altre informazioni sulla creazione e pubblicazione di pacchetti NuGet.

## <a name="related-links"></a>Collegamenti correlati

- [Creazione di plug-in riutilizzabili per xamarin. Forms](https://blog.xamarin.com/creating-reusable-plugins-for-xamarin-forms)
- [Plug-in Usa l'au & lo sviluppo per Xamarin (video)](https://university.xamarin.com/guestlectures/using-developing-plugins-for-xamarin)
