---
title: Creazione di una nuova libreria multipiattaforma per NuGet
ms.prod: xamarin
ms.assetid: E7B55354-9BBE-4122-BCE3-3506B79090DD
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 85403ee2ab8b5c1433e0e070b3af22fa42cd5efa
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="creating-a-new-multiplatform-library-for-nuget"></a>Creazione di una nuova libreria multipiattaforma per NuGet

La creazione di un progetto di libreria multipiattaforma che utilizza una libreria di classi Portabile o .NET Standard significa che è possibile aggiungere a qualsiasi progetto .NET che supporta il profilo di destinazione, tra cui i progetti ASP.NET o App desktop con Windows Form, WPF o UWP NuGet risultante.

La raccolta può contenere solo codice supportati mediante il profilo selezionato PCL o .NET Standard, come qualsiasi altro NuGets che vengono aggiunti.
Ciò è utile per la logica di business e algoritmi predefiniti che possono essere espresse interamente in libreria di classi base .NET.

Un singolo assembly viene creato e compilato in un pacchetto NuGet.

Se successivamente sarà necessario funzionalità specifiche della piattaforma, [possibile aggiungere progetti specifici della piattaforma](#add-platforms).

## <a name="steps-to-create-a-multiplatform-library-nuget"></a>Passaggi per creare una libreria multipiattaforma di NuGet

1. Selezionare **File > nuova soluzione** (o fare clic con il pulsante destro una soluzione esistente e scegliere **Aggiungi > Nuovo progetto**).

2. Scegliere **libreria multipiattaforma** dal **multipiattaforma > libreria** sezione:

  [![](single-codebase-images/mulitplatform-library-sml.png "Configurare una libreria multipiattaforma per una sola codebase")](single-codebase-images/mulitplatform-library.png#lightbox)

3. Immettere un **nome** e **descrizione**e scegliere **singola per tutte le piattaforme**:

  [![](single-codebase-images/single-configure-sml.png "Configurare una libreria multipiattaforma per una sola codebase")](single-codebase-images/single-configure.png#lightbox)

4. Completare la procedura guidata. Viene creato un progetto di libreria singola nella soluzione.

5. Fare clic sul nuovo progetto di libreria e quindi selezionare **opzioni**. Il **compilare > Generale** sezione consente di **Framework di destinazione** impostare-scegliere un profilo .NET portabili PCL o una versione .NET Standard:

  [![](single-codebase-images/single-choose-type-sml.png "Scegliere PCL o .NET Standard per il tipo di libreria")](single-codebase-images/single-choose-type.png#lightbox)

6. Anche nel **opzioni progetto** finestra, aprire il **pacchetto NuGet > metadati** sezione e immettere il [i metadati necessari](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) (nonché i metadati facoltativi):

  [![](single-codebase-images/single-metadata-sml.png "Immettere i metadati necessari")](single-codebase-images/single-metadata.png#lightbox)

7. Pulsante destro del mouse sul progetto di libreria e scegliere **Crea pacchetto NuGet** (o compilare o distribuire la soluzione) e **.nupkg** verrà salvata nel file del pacchetto NuGet di **/bin/** cartella (Debug o Release, a seconda della configurazione):

  ![](single-codebase-images/create-nuget-package.png "Il file del pacchetto NuGet verrà essere salvato nella cartella bin Debug o Release, a seconda della configurazione")


## <a name="verifying-the-output"></a>Verifica per determinare se l'Output

Pacchetti NuGet sono anche i file ZIP, pertanto è possibile esaminare la struttura interna del pacchetto generato.

Questa schermata è riportato il contenuto di un NuGet basata sulla libreria di classi Portabile: è incluso un singolo assembly libreria di classi Portabile:

![](single-codebase-images/nuget-output.png "File contenuti nel pacchetto NuGet.")

<a name="add-platforms" />

## <a name="adding-platform-specific-code"></a>Aggiunta di codice specifico della piattaforma

Progetti basati su libreria di classi Portabile e progetti basati su .NET Standard non possono contenere riferimenti specifici della piattaforma (ad esempio iOS o Android).

Se un progetto libreria di classi Portabile esistente o un progetto .NET Standard deve essere espanso per includere codice specifico della piattaforma, questa operazione può essere eseguita facendo clic sul progetto e selezionando **Aggiungi > aggiungere l'implementazione della piattaforma...** :

[![](single-codebase-images/add-later-sml.png "Aggiungere il menu di implementazione della piattaforma")](single-codebase-images/add-later.png#lightbox)

Uno o più progetti di piattaforma possono essere aggiunto alla soluzione e, facoltativamente, nella libreria di classi Portabile o .NET Standard nella raccolta esistente può essere convertita in un progetto condiviso:

[![](single-codebase-images/add-later-platforms-sml.png "Aggiungere le opzioni di piattaforma, ad esempio iOS, Android e progetto condiviso")](single-codebase-images/add-later-platforms-sml.png#lightbox)

Dopo la conversione in un progetto condiviso, visitare il **opzioni progetto > pacchetto NuGet > assembly di riferimento**
[sezione](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/platform-specific.md) e verificare che le richieste vengono selezionati i profili (in modo che il NuGet continua a essere compatibile con i progetti che è stato utilizzato in precedenza).


## <a name="related-links"></a>Collegamenti correlati

- [Guida ai metadati](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
