---
title: Progetti di librerie di NuGet multipiattaforma (noto anche come Nugetizer 3000)
description: Questo documento descrive come usare lo strumento Nugetizer 3000 per creare automaticamente pacchetti NuGet per condividere codice tra piattaforme.
ms.prod: xamarin
ms.assetid: F0A5A9BB-86CD-44C9-8EE8-74D1E5E74A30
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: 6d3f7b316e397705ecb9bd404007dcd9ef5aa183
ms.sourcegitcommit: 2868c968f418cd7cc110f9664f3c3ffb6df1f9af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2018
ms.locfileid: "53267261"
---
# <a name="nuget-multiplatform-library-projects-nugetizer-3000"></a>Progetti di libreria multipiattaforma NuGet (Nugetizer 3000)

_Creare automaticamente pacchetti NuGet per condividere codice tra piattaforme mediante l'utilizzo di 'Nugetizer 3000'!_

È possibile creare automaticamente pacchetti NuGet per condividere codice tra piattaforme, usando il _Nugetizer 3000_. In questo modo è possibile creare pacchetti NuGet da progetti di libreria esistenti o creando una nuova **progetto di libreria multipiattaforma**.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Nugetizer 3000 è incluso in Visual Studio per Mac &ndash; cercare la **Library > libreria Mulitplatform** nel tipo di progetto il **File > New** finestra:

[![](images/mulitplatform-library-sml.png "Creare finestra nuova libreria multipiattaforma")](images/mulitplatform-library.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Per usare Nugetizer 3000 in Visual Studio, please [scaricare ed eseguire il programma di installazione VSIX](http://bit.ly/nugetizer-2017).

-----

## <a name="building-nuget-packages"></a>Compilazione dei pacchetti NuGet

Una volta configurato, ogni compilazione del progetto genera un pacchetto NuGet completo, che può essere utilizzato per condividere codice internamente con altre App o caricato in [NuGet.org](https://www.nuget.org).

Esistono tre scenari per l'uso di questa funzionalità:

- [Progetti di libreria esistenti](existing-library.md)

  Creare un pacchetto NuGet da progetti di libreria di classi Portabile (o .NET Standard) esistenti.

- [Crea un nuovo progetto di libreria multipiattaforma](single-codebase.md)

  Creare una nuova libreria per condividere il codice tramite NuGet, usando una libreria di classi Portabile o .NET Standard.

- [Creazione di nuovi progetti di libreria specifica della piattaforma](platform-specific.md)

  Creare una nuova libreria e NuGet che include codice specifico della piattaforma per iOS e Android e l'utilizzo di un progetto condiviso per contenere il codice comune e progetti specifici della piattaforma per supportare la funzionalità di iOS o Android specifica.

Vedere le [Guida ai metadati](metadata.md) per informazioni dettagliate sui metadati obbligatori e facoltativi che devono essere aggiunti a un pacchetto NuGet.

## <a name="further-nuget-information"></a>Per ulteriori informazioni di NuGet

Altre informazioni, vedere [creazione manuale di pacchetti NuGet per Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md) e su come [includono un pacchetto NuGet in un'app](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

Avendo [documentazione di NuGet](https://docs.microsoft.com/nuget/) contiene informazioni più dettagliate sul **pacchetto. nupkg** formato e l'uso di pacchetti NuGet in Visual Studio.

La discussione di progettazione per i progetti di pacchetti NuGet (noto anche come Sono disponibili sul NuGetizer 3000) il [repository GitHub di NuGet](https://github.com/NuGet/Home/wiki/NuGetizer-3000).

## <a name="related-links"></a>Collegamenti correlati

- [Casi d'uso NuGetizer 3000](https://github.com/NuGet/Home/wiki/NuGetizer-Core-Scenarios)
- [Creare manualmente i pacchetti NuGet per Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md)
- [Documentazione di NuGet](https://docs.microsoft.com/nuget/)
