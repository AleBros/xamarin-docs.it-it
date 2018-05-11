---
title: Condivisione del codice
description: Concetti di base dell'applicazione
ms.prod: xamarin
ms.assetid: 7D179ACF-09A6-46EE-B49D-E27AB5F09CD4
author: asb3993
ms.author: amburns
ms.date: 02/18/2018
ms.openlocfilehash: 4cca202627d1b901e00532c92598ffddd48b4853
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="sharing-code"></a>Condivisione del codice

In questa sezione viene fornita una Guida su alcune delle più comuni attività di cose o concetti che gli sviluppatori devono tenere conto durante lo sviluppo di applicazioni per dispositivi mobili.

## <a name="code-sharing-overviewcode-sharingmd"></a>[Panoramica della condivisione del codice](code-sharing.md)

Ulteriori informazioni sul codice diverso opzioni disponibili per progetti di Xamarin, incluse le librerie di classi portabili (PCLs), progetti condivisi e le librerie Standard di .NET di condivisione.


##  <a name="portable-class-librariescross-platformapp-fundamentalspclmd"></a>[Librerie di classi portabili](~/cross-platform/app-fundamentals/pcl.md)

Progetti libreria di classi portabili consentono di creare e distribuire gli assembly contenenti codice condiviso per l'esecuzione su più piattaforme. Per creare una libreria di classi portabile (o "PCL") prima selezionare le piattaforme di destinazione, quindi scrivere codice per un sottoinsieme di .NET Framework è disponibile nel profilo definito per tali piattaforme. Questo documento viene descritto come creare e usare PCLs con Xamarin.

##  <a name="shared-projectscross-platformapp-fundamentalsshared-projectsmd"></a>[Progetti condivisi](~/cross-platform/app-fundamentals/shared-projects.md)

Progetti condivisi consentono di scrivere codice comune a cui fa riferimento un numero di progetti di applicazione diverso. Il codice viene compilato come parte di ogni progetto di riferimento e può includere le direttive del compilatore per incorporare le funzionalità specifiche della piattaforma di base di codice condiviso. Questo articolo illustra il funzionamento progetti condivisi e come crearli e utilizzarli con i progetti di Xamarin.

##  <a name="net-standardcross-platformapp-fundamentalsnet-standardmd"></a>[.NET Standard](~/cross-platform/app-fundamentals/net-standard.md)

.NET standard è una nuova opzione per la condivisione del codice tra le piattaforme. Funziona in modo simile alle librerie di classi portabile; codice viene compilato con una versione specifica (attualmente 1.0 tramite 1.6) e può quindi essere utilizzato da altri progetti che supportano tale livello o superiore. Progetti .NET standard sono supportati in 6.2 di Xamarin Studio, Visual Studio per Windows e Visual Studio per Mac.

##  <a name="nuget-projects-multiplatform-libraries-for-code-sharingcross-platformapp-fundamentalsnuget-multiplatform-librariesindexmd"></a>[Progetti di NuGet: Librerie multipiattaforma per la condivisione del codice](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/index.md)

Pacchetti NuGet possono essere generati automaticamente da progetti libreria di classi Portabile o .NET standard. e i progetti condivisi possono essere inseriti in pacchetti NuGet "tecnica dello" utilizzando il tipo di progetto NuGet separato. In questa sezione viene illustrato come creare pacchetti di NuGet per ogni scenario di condivisione del codice.

##  <a name="manually-creating-nuget-packages-for-xamarincross-platformapp-fundamentalsnuget-manualmd"></a>[Creazione manuale di pacchetti NuGet di Xamarin](~/cross-platform/app-fundamentals/nuget-manual.md)

Suggerimenti per la creazione di pacchetti NuGet che funzionano con la piattaforma di Xamarin.
