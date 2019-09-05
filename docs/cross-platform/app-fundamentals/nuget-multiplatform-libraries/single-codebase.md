---
title: Creazione di una nuova libreria multipiattaforma per NuGet
description: Questo documento descrive come creare una libreria multipiattaforma da usare con NuGet. Questa tecnica è adatta per la logica di business e gli algoritmi che possono essere espressi interamente nella libreria di classi base .NET e vengono quindi eseguiti in tutte le piattaforme di destinazione senza codice specifico della piattaforma.
ms.prod: xamarin
ms.assetid: E7B55354-9BBE-4122-BCE3-3506B79090DD
author: conceptdev
ms.author: crdun
ms.date: 03/23/2017
ms.openlocfilehash: 966d7a21da0112383c08e862a357c3c1d4fb9c22
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70289729"
---
# <a name="creating-a-new-multiplatform-library-for-nuget"></a>Creazione di una nuova libreria multipiattaforma per NuGet

La creazione di un progetto di libreria multipiattaforma che usa PCL o .NET Standard significa che il NuGet risultante può essere aggiunto a qualsiasi progetto .NET che supporta il profilo di destinazione, inclusi i progetti ASP.NET o le app desktop usando WinForms, WPF o UWP.

La libreria può contenere solo codice supportato dal profilo PCL o .NET Standard selezionato, nonché qualsiasi altro NuGet aggiunto.
Questo è adatto alla logica di business e agli algoritmi che possono essere espressi interamente nella libreria di classi di base .NET.

Un singolo assembly viene creato e incorporato in un pacchetto NuGet.

Se successivamente si necessita di funzionalità specifiche della piattaforma, è [possibile aggiungere progetti specifici della piattaforma](#add-platforms).

## <a name="steps-to-create-a-multiplatform-library-nuget"></a>Procedura per creare una libreria multipiattaforma NuGet

1. Selezionare **File > nuova soluzione** oppure fare clic con il pulsante destro del mouse su una soluzione esistente e scegliere **Aggiungi > nuovo progetto**.

2. Scegliere **libreria multipiattaforma** dalla sezione **Libreria > multipiattaforma** :

   [![](single-codebase-images/mulitplatform-library-sml.png "Configurare la libreria multipiattaforma per una singola codebase")](single-codebase-images/mulitplatform-library.png#lightbox)

3. Immettere un **nome** e una **Descrizione**e scegliere **Single per tutte le piattaforme**:

   [![](single-codebase-images/single-configure-sml.png "Configurare la libreria multipiattaforma per una singola codebase")](single-codebase-images/single-configure.png#lightbox)

4. Completare la procedura guidata. Un progetto di libreria singolo viene creato nella soluzione.

5. Fare clic con il pulsante destro del mouse sul nuovo progetto di libreria e quindi scegliere **Opzioni**. La sezione **compilazione > generale** consente di impostare il **Framework di destinazione** . scegliere un profilo della libreria di classi portabile .net o una versione .NET standard:

   [![](single-codebase-images/single-choose-type-sml.png "Scegli PCL o .NET Standard per tipo di libreria")](single-codebase-images/single-choose-type.png#lightbox)

6. Nella finestra **Opzioni progetto** aprire inoltre il **pacchetto NuGet > sezione metadati** e immettere i [metadati necessari](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) , nonché i metadati facoltativi:

   [![](single-codebase-images/single-metadata-sml.png "Immettere i metadati necessari")](single-codebase-images/single-metadata.png#lightbox)

7. Fare clic con il pulsante destro del mouse sul progetto di libreria e scegliere **Crea pacchetto NuGet** (o compila o Distribuisci la soluzione) e il file del pacchetto NuGet **. nupkg** verrà salvato nella cartella **/bin/** (debug o versione, a seconda della configurazione):

   ![](single-codebase-images/create-nuget-package.png "Il file del pacchetto NuGet verrà salvato nella cartella bin debug o release, a seconda della configurazione")


## <a name="verifying-the-output"></a>Verifica dell'output

I pacchetti NuGet sono anche file ZIP, quindi è possibile controllare la struttura interna del pacchetto generato.

Questa schermata mostra il contenuto di un NuGet basato su PCL. è incluso solo un singolo assembly PCL:

![](single-codebase-images/nuget-output.png "File contenuti nel pacchetto NuGet")

<a name="add-platforms" />

## <a name="adding-platform-specific-code"></a>Aggiunta di codice specifico per la piattaforma

I progetti basati su PCL e i progetti basati su .NET Standard non possono contenere riferimenti specifici della piattaforma, ad esempio la funzionalità iOS o Android.

Se è necessario espandere un progetto PCL o un progetto .NET Standard esistente per includere codice specifico della piattaforma, è possibile fare clic con il pulsante destro del mouse sul progetto e scegliere **aggiungi > Aggiungi implementazione della piattaforma...** :

[![](single-codebase-images/add-later-sml.png "Menu Aggiungi implementazione piattaforma")](single-codebase-images/add-later.png#lightbox)

È possibile aggiungere uno o più progetti di piattaforma alla soluzione e la libreria PCL o .NET Standard esistente può facoltativamente essere convertita in un progetto condiviso:

[![](single-codebase-images/add-later-platforms-sml.png "Aggiungere opzioni della piattaforma, ad esempio iOS, Android e progetto condiviso")](single-codebase-images/add-later-platforms-sml.png#lightbox)

Dopo la conversione in un progetto condiviso, visitare la[sezione](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/platform-specific.md) **Opzioni progetto > pacchetto NuGet > assembly**
di riferimento e assicurarsi che siano selezionati tutti i profili necessari, in modo che NuGet continui a essere compatibile con i progetti in precedenza era usato in).


## <a name="related-links"></a>Collegamenti correlati

- [Guida ai metadati](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
