---
title: Creazione di una nuova libreria multipiattaforma per NuGet
description: Questo documento descrive come creare una libreria multipiattaforma per l'uso con NuGet. Questa tecnica è ideale per la logica di business e gli algoritmi che possono essere espressi interamente nella libreria di classi Base .NET e pertanto verranno eseguito su tutte le piattaforme di destinazione senza codice specifico della piattaforma.
ms.prod: xamarin
ms.assetid: E7B55354-9BBE-4122-BCE3-3506B79090DD
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 6d695df9c59a5f95441092d6d7b44d5feda941bd
ms.sourcegitcommit: 5fc171a45697f7c610d65f74d1f3cebbac445de6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52172054"
---
# <a name="creating-a-new-multiplatform-library-for-nuget"></a>Creazione di una nuova libreria multipiattaforma per NuGet

Creazione di un progetto di libreria multipiattaforma che Usa libreria di classi Portabile o .NET Standard significa che NuGet risultante può essere aggiunto a qualsiasi progetto .NET che supporta il profilo di destinazione, tra cui i progetti ASP.NET o applicazioni desktop con Windows Form, WPF o UWP.

La libreria può contenere solo codice supportato dal profilo di libreria di classi Portabile o .NET Standard selezionato, nonché eventuali altri pacchetti NuGet aggiunti.
Ciò è utile per la logica di business e gli algoritmi che possono essere espressi interamente nella libreria di classi base .NET.

Un singolo assembly viene creato e compilato in un pacchetto NuGet.

Se è necessario in un secondo momento funzionalità specifiche della piattaforma [progetti specifici delle piattaforme possono essere aggiunti](#add-platforms).

## <a name="steps-to-create-a-multiplatform-library-nuget"></a>Passaggi per creare un libreria multipiattaforma di NuGet

1. Selezionare **File > nuova soluzione** (o fare clic con il pulsante destro una soluzione esistente e scegliere **Aggiungi > Nuovo progetto**).

2. Scegli **libreria multipiattaforma** dal **multipiattaforma > libreria** sezione:

  [![](single-codebase-images/mulitplatform-library-sml.png "Configurare multi-piattaforma raccolta per una singola codebase")](single-codebase-images/mulitplatform-library.png#lightbox)

3. Immettere un **Name** e **descrizione**, quindi scegliere **singolo per tutte le piattaforme**:

  [![](single-codebase-images/single-configure-sml.png "Configurare multi-piattaforma raccolta per una singola codebase")](single-codebase-images/single-configure.png#lightbox)

4. Completare la procedura guidata. Viene creato un progetto di libreria singola nella soluzione.

5. Fare doppio clic sul nuovo progetto di libreria e quindi selezionare **opzioni**. Il **compilazione > Generale** sezione consente il **Framework di destinazione** da impostare – scegliere un profilo Portabile .NET o una versione di .NET Standard:

  [![](single-codebase-images/single-choose-type-sml.png "Scegliere una libreria di classi Portabile o .NET Standard per il tipo di libreria")](single-codebase-images/single-choose-type.png#lightbox)

6. Anche nel **opzioni progetto** finestra, aprire il **pacchetto NuGet > metadati** sezione e immettere il [i metadati necessari](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) (nonché tutti i metadati facoltativi):

  [![](single-codebase-images/single-metadata-sml.png "Immettere i metadati obbligatori")](single-codebase-images/single-metadata.png#lightbox)

7. Pulsante destro del mouse sul progetto di libreria e scegliere **creazione di pacchetti NuGet** (o compilare o distribuire la soluzione) e il **pacchetto. nupkg** verrà salvata nel file del pacchetto NuGet il **/bin/** cartella (Debug o Release, a seconda della configurazione):

  ![](single-codebase-images/create-nuget-package.png "Il file del pacchetto NuGet verrà essere salvato nella cartella bin Debug o Release, a seconda della configurazione")


## <a name="verifying-the-output"></a>Verifica per determinare se l'Output

I pacchetti NuGet sono anche i file con estensione ZIP, pertanto è possibile esaminare la struttura interna del pacchetto generato.

Questa schermata mostra il contenuto del pacchetto NuGet basato su libreria di classi Portabile, che è incluso solo un singolo assembly libreria di classi Portabile:

![](single-codebase-images/nuget-output.png "File contenuti nel pacchetto NuGet")

<a name="add-platforms" />

## <a name="adding-platform-specific-code"></a>Aggiunta di codice specifico della piattaforma

I progetti basati su libreria di classi Portabile e basate su .NET Standard non può contenere riferimenti specifici della piattaforma (ad esempio, iOS o Android funzionalità).

Se un progetto libreria di classi Portabile esistente o un progetto .NET Standard deve essere espanso per includere codice specifico della piattaforma, questa operazione può essere eseguita facendo clic sul progetto e selezionando **Aggiungi > Aggiungi l'implementazione della piattaforma...** :

[![](single-codebase-images/add-later-sml.png "Aggiungere il menu di implementazione della piattaforma")](single-codebase-images/add-later.png#lightbox)

Uno o più progetti di piattaforma possono essere aggiunto alla soluzione e la libreria .NET Standard o libreria di classi Portabile esistente può facoltativamente essere convertita in un progetto condiviso:

[![](single-codebase-images/add-later-platforms-sml.png "Aggiungere le opzioni di piattaforma, ad esempio iOS, Android e progetto condiviso")](single-codebase-images/add-later-platforms-sml.png#lightbox)

Dopo aver convertito in un progetto condiviso, visitare il **opzioni progetto > pacchetto NuGet > assembly di riferimento**
[sezione](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/platform-specific.md) e assicurarsi che qualsiasi richiesta vengono selezionati i profili (in modo che il NuGet continua a essere compatibile con i progetti che è stato utilizzato in precedenza).


## <a name="related-links"></a>Collegamenti correlati

- [Guida ai metadati](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
