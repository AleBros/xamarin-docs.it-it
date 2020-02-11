---
title: Condivisione di codice su più piattaforme
description: Questo documento contiene collegamenti a diverse guide che descrivono le tecniche per la condivisione di codice, tra cui librerie di classi portabili, progetti condivisi, .NET Standard e NuGet.
ms.prod: xamarin
ms.assetid: 7D179ACF-09A6-46EE-B49D-E27AB5F09CD4
author: davidortinau
ms.author: daortin
ms.date: 07/18/2018
ms.openlocfilehash: a91fba3cd1fba3bcf2317e8f9cb25631c62491cb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016819"
---
# <a name="sharing-code-on-multiple-platforms"></a>Condivisione di codice su più piattaforme

Questi articoli illustrano le diverse opzioni disponibili per la condivisione di codice tra piattaforme, tra cui Windows, Android, iOS e altro ancora.

## <a name="code-sharing-overviewcode-sharingmd"></a>[Panoramica della condivisione del codice](code-sharing.md)

Informazioni sulle diverse opzioni di condivisione del codice disponibili per i progetti Xamarin, tra cui librerie .NET Standard e progetti condivisi. Sono supportate anche le librerie di classi portabili, ma sono considerate deprecate a favore di .NET Standard.

## <a name="net-standardcross-platformapp-fundamentalsnet-standardmd"></a>[.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)

.NET Standard è l'opzione preferita per la condivisione di codice tra piattaforme diverse. Il codice viene compilato in base a una versione specifica (2,0 fornisce la migliore compatibilità API con il codice di .NET Framework esistente) e può quindi essere utilizzato da altri progetti che supportano tale livello o superiore. I progetti .NET Standard sono supportati sia in Visual Studio 2019 che in Visual Studio 2019 per Mac.

## <a name="shared-projectscross-platformapp-fundamentalsshared-projectsmd"></a>[Progetti condivisi](~/cross-platform/app-fundamentals/shared-projects.md)

I progetti condivisi consentono di scrivere codice comune a cui fa riferimento una serie di progetti di applicazioni diversi. Il codice viene compilato come parte di ogni progetto di riferimento e può includere direttive del compilatore che consentono di incorporare funzionalità specifiche della piattaforma nella codebase condivisa. Questo articolo illustra come funzionano i progetti condivisi e come crearli e usarli con i progetti Xamarin.

## <a name="portable-class-librariescross-platformapp-fundamentalspclmd"></a>[Librerie di classi portabili](~/cross-platform/app-fundamentals/pcl.md)

I progetti libreria di classi portabili consentono di compilare e distribuire assembly che contengono codice condiviso da eseguire su più piattaforme. Per creare una libreria di classi portabile (o "PCL"), è necessario innanzitutto selezionare le piattaforme di destinazione, quindi scrivere il codice su un subset del .NET Framework disponibile nel profilo definito per tali piattaforme. Classi portabili sono considerati deprecati nelle versioni più recenti di Visual Studio. gli sviluppatori sono invitati a usare invece .NET Standard 2,0.

## <a name="nuget-projects-multiplatform-libraries-for-code-sharingcross-platformapp-fundamentalsnuget-multiplatform-librariesindexmd"></a>[Progetti NuGet: librerie multipiattaforma per la condivisione del codice](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)

I pacchetti NuGet possono essere generati automaticamente da progetti PCL o .NET standard. e i progetti condivisi possono essere inclusi in pacchetti NuGet "Bait and switch" usando il tipo di progetto NuGet separato. Questa sezione illustra come creare pacchetti NuGet per ogni scenario di condivisione del codice.

## <a name="manually-creating-nuget-packages-for-xamarincross-platformapp-fundamentalsnuget-manualmd"></a>[Creazione manuale di pacchetti NuGet per Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md)

Suggerimenti per la creazione di pacchetti NuGet che funzionano con la piattaforma Xamarin.

## <a name="use-cc-libraries-in-cross-platform-xamarin-projectscross-platformcppindexmd"></a>[Usare C/C++ librerie nei progetti Xamarin multipiattaforma](~/cross-platform/cpp/index.md)

Questa tecnica consente di separare l'evoluzione delle librerie C/C++ , un' C# associazione in un NuGet e le applicazioni Xamarin. La funzionalità viene fornita dallaC++ libreria C-Platform nativa, ma tutto il codice specifico della piattaforma è isolato dalle applicazioni Xamarin finali, consentendo le prestazioni più elevate senza duplicazione del codice. 
