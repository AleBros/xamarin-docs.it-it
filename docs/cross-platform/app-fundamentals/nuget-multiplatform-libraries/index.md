---
title: Progetti libreria multipiattaforma NuGet (noto anche come Nugetizer 3000)
description: Questo documento descrive come usare lo strumento Nugetizer 3000 per creare automaticamente pacchetti NuGet per condividere codice tra piattaforme diverse.
ms.prod: xamarin
ms.assetid: F0A5A9BB-86CD-44C9-8EE8-74D1E5E74A30
author: davidortinau
ms.author: daortin
ms.date: 07/25/2018
ms.openlocfilehash: 5744bb9947b196ee319535729338bcf64a5cd09e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016753"
---
# <a name="nuget-multiplatform-library-projects-nugetizer-3000"></a>Progetti libreria multipiattaforma NuGet (Nugetizer 3000)

_Crea automaticamente i pacchetti NuGet per condividere il codice tra le piattaforme usando ' Nugetizer 3000'._

È possibile creare automaticamente pacchetti NuGet per condividere il codice tra piattaforme usando il _Nugetizer 3000_. Ciò rende possibile la creazione di pacchetti NuGet da progetti di libreria esistenti o creando un nuovo **progetto di libreria multipiattaforma**.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Nugetizer 3000 è incluso con Visual Studio per Mac &ndash; cercare la libreria > tipo di progetto **libreria Mulitplatform** nel **file > nuova** finestra:

[![](images/mulitplatform-library-sml.png "Create new Multiplatform Library window")](images/mulitplatform-library.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Per usare Nugetizer 3000 in Visual Studio, [scaricare ed eseguire il programma di installazione VSIX](https://bit.ly/nugetizer-2017).

-----

## <a name="building-nuget-packages"></a>Compilazione di pacchetti NuGet

Una volta configurata, ogni compilazione del progetto restituisce un pacchetto NuGet completo, che può essere usato per condividere il codice internamente con altre app o caricate in [NuGet.org](https://www.nuget.org).

Sono disponibili tre scenari per l'utilizzo di questa funzionalità:

- [Progetti di libreria esistenti](existing-library.md)

  Creare un pacchetto NuGet da progetti PCL (o .NET Standard) esistenti.

- [Creazione di un nuovo progetto di libreria multipiattaforma](single-codebase.md)

  Creare una nuova libreria per condividere il codice comune tramite NuGet, usando una libreria di classi portabile o un .NET Standard.

- [Creazione di nuovi progetti di libreria specifici della piattaforma](platform-specific.md)

  Creare una nuova libreria e NuGet che includa codice specifico della piattaforma per iOS e Android e usa un progetto condiviso per contenere il codice comune e i progetti specifici della piattaforma per supportare le funzionalità specifiche di iOS o Android.

Vedere la [Guida ai metadati](metadata.md) per informazioni dettagliate sui metadati obbligatori e facoltativi che devono essere aggiunti a qualsiasi pacchetto NuGet.

## <a name="further-nuget-information"></a>Ulteriori informazioni su NuGet

Scopri di più sulla [creazione manuale di NuGet per Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md) e su come [includere un pacchetto NuGet in un'app](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

La documentazione di Microsoft [NuGet](https://docs.microsoft.com/nuget/) contiene informazioni più dettagliate sul formato **nupkg** e sull'uso di pacchetti NuGet in Visual Studio.

Descrizione della progettazione per i progetti di pacchetti NuGet (noto anche come NuGetizer 3000) è disponibile nel [repository GitHub di NuGet](https://github.com/NuGet/Home/wiki/NuGetizer-3000).

## <a name="related-links"></a>Collegamenti correlati

- [NuGetizer-3000 casi d'uso](https://github.com/NuGet/Home/wiki/NuGetizer-Core-Scenarios)
- [Creare manualmente pacchetti NuGet per Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md)
- [Documentazione di NuGet](https://docs.microsoft.com/nuget/)
