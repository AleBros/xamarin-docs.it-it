---
title: Progetti di NuGet (Nugetizer 3000)
description: Creare automaticamente i pacchetti NuGet per condividere il codice tra piattaforme che utilizzano il '3000 Nugetizer'!
ms.topic: article
ms.prod: xamarin
ms.assetid: F0A5A9BB-86CD-44C9-8EE8-74D1E5E74A30
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 11/22/2017
ms.openlocfilehash: 49e7c00feb697d25d61a5e09b051c41945c260c6
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="nuget-projects-nugetizer-3000"></a>Progetti di NuGet (Nugetizer 3000)

_Creare automaticamente i pacchetti NuGet per condividere il codice tra piattaforme che utilizzano il '3000 Nugetizer'!_

È possibile creare automaticamente i pacchetti NuGet per condividere il codice tra piattaforme che utilizzano il _Nugetizer 3000_. In questo modo è possibile creare pacchetti NuGet da progetti di libreria esistente o creando un nuovo **il progetto di libreria multipiattaforma**.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Il 3000 Nugetizer è incluso in Visual Studio per Mac 6.2.

[![](images/mulitplatform-library-sml.png "Creare finestra nuova libreria multipiattaforma")](images/mulitplatform-library.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Per utilizzare il 3000 Nugetizer in Visual Studio, eseguire [scaricare ed eseguire il programma di installazione VSIX](http://bit.ly/nugetizer-2017).

-----

## <a name="building-nuget-packages"></a>Pacchetti NuGet di compilazione

Una volta configurato, ogni compilazione del progetto genera un pacchetto NuGet completato, che può essere usato per condividere codice internamente con altre App o caricato in [NuGet.org](https://www.nuget.org).

Esistono tre scenari per l'utilizzo di questa funzionalità:

- [Progetti di libreria esistenti](existing-library.md)

  Creare un pacchetto NuGet dai progetti di libreria di classi Portabile (.NET Standard o) esistenti.

- [Crea un nuovo progetto di libreria multipiattaforma](single-codebase.md)

  Creare una nuova libreria per condividere il codice tramite NuGet, tramite una libreria di classi Portabile o .NET Standard.

- [Creazione di nuovi progetti di libreria specifica della piattaforma](platform-specific.md)

  Creare una nuova libreria e NuGet che include codice specifico della piattaforma per iOS e Android e utilizza un progetto condiviso per contenere il codice comune e progetti specifici della piattaforma per supportare la funzionalità specifica di iOS o Android.

Consultare il [metadati Guida](metadata.md) per ulteriori informazioni sui metadati obbligatori e facoltativi che devono essere aggiunti a qualsiasi pacchetto NuGet.


## <a name="further-nuget-information"></a>Per ulteriori informazioni di NuGet

Altre informazioni sui [creazione manuale di NuGets per Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md) e come [includono un pacchetto NuGet in un'app](https://docs.microsoft.com/visualstudio/mac/nuget-walkthrough).

Microsoft [documentazione di NuGet](https://docs.microsoft.com/nuget/) contiene informazioni più dettagliate sul **.nupkg** formato e l'utilizzo di pacchetti NuGet in Visual Studio.

La discussione di progettazione per i progetti di pacchetto NuGet (anche noto come È disponibile in NuGetizer 3000) il [repository NuGet GitHub](https://github.com/NuGet/Home/wiki/NuGetizer-3000).


## <a name="related-links"></a>Collegamenti correlati

- [Casi d'uso NuGetizer 3000](https://github.com/NuGet/Home/wiki/NuGetizer-Core-Scenarios)
- [Creare manualmente i pacchetti NuGet di Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md)
- [Documentazione di NuGet](https://docs.microsoft.com/nuget/)
- [Note sulla versione](https://developer.xamarin.com/releases/studio/xamarin.studio_6.2/xamarin.studio_6.2/#NuGetizer_3000)
