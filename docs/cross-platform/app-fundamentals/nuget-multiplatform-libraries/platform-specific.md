---
title: Creazione di nuovi progetti di libreria specifica della piattaforma per NuGet
description: Questo documento descrive come creare un singolo pacchetto NuGet che contiene codice specifico della piattaforma per più piattaforme.
ms.prod: xamarin
ms.assetid: D8BC4906-805F-4AFB-8D1A-88B7BF87E17F
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 00a02973d6016ad63e4317279515acc2b4e2e81b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61267206"
---
# <a name="creating-new-platform-specific-library-projects-for-nuget"></a>Creazione di nuovi progetti di libreria specifica della piattaforma per NuGet

Multipiattaforma progetti della libreria destinate a piattaforme specifiche, ad esempio iOS e Android, funzionano meglio con i progetti condivisi.

NuGet può contenere sia codice specifiche di Android e iOS, nonché il codice .NET comune a entrambi.

Più assembly vengono creati e incorporato in un singolo pacchetto NuGet. NuGet garantiscono che il pacchetto può essere aggiunto a tutti i tipi di progetto supportati, ad esempio i progetti xamarin. IOS e Android.

## <a name="steps-to-create-a-cross-platform-library-nuget"></a>Passaggi per creare un libreria multipiattaforma di NuGet

1. Selezionare **File > nuova soluzione** (o fare clic con il pulsante destro una soluzione esistente e scegliere **Aggiungi > Nuovo progetto**).

2. Scegli **libreria multipiattaforma** dal **multipiattaforma > libreria** sezione:

  [![](platform-specific-images/mulitplatform-library-sml.png "Configurare multi-piattaforma raccolta per una singola codebase")](platform-specific-images/multiplatform-library.png#lightbox)

3. Immettere un **nome** e **descrizione**, quindi scegliere **specifiche della piattaforma**:

  [![](platform-specific-images/specific-configure-sml.png "Configurare la libreria specifica della piattaforma per iOS e Android")](platform-specific-images/specific-configure.png#lightbox)

4. Completare la procedura guidata. I progetti seguenti vengono aggiunti alla soluzione:

  - **Progetto Android** – codice specifico per Android può essere aggiunto facoltativamente al progetto.
  - **Progetto iOS** – codice specifico di iOS può essere aggiunto facoltativamente al progetto.
  - **Progetto NuGet** – non viene aggiunto codice al progetto. Fa riferimento gli altri progetti e contiene la configurazione di metadati per l'output del pacchetto NuGet.
  - **Progetto condiviso** : il codice comune deve essere aggiunto a questo progetto, incluso il codice specifico della piattaforma in `#if` direttive del compilatore.

5. Pulsante destro del mouse sul progetto NuGet e scegliere **opzioni**, quindi aprire il **pacchetto NuGet > metadati** sezione e immettere il [i metadati necessari](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) (come nonché a tutte facoltative metadati):

  [![](platform-specific-images/specific-metadata-sml.png "Immettere i metadati obbligatori")](platform-specific-images/specific-metadata.png#lightbox)

6. Anche nel **opzioni progetto** finestra, aprire il **gli assembly di riferimento** sezione e scegliere i profili della libreria di classi Portabile della libreria condivisa supporterà tramite "specchietto per le allodole":

  ![](platform-specific-images/specific-reference-assemblies.png "Anche nella finestra Opzioni del progetto, aprire la sezione degli assembly di riferimento e scegliere i profili della libreria di classi Portabile della libreria condivisa supporterà bait and switch")

  > [!NOTE]
> "Specchietto per le allodole" significa che gli assembly libreria di classi Portabile conterrà solo l'API esposta dalla libreria (non può contenere il codice specifico della piattaforma). Quando NuGet viene aggiunto a un progetto Xamarin, le librerie condivise verranno compilate con la libreria di classi Portabile, ma gli assembly specifici della piattaforma contengano il codice che viene effettivamente usato dal progetto Android o iOS.

7. Pulsante destro del mouse sul progetto e scegliere **creazione di pacchetti NuGet** (o compilare o distribuire la soluzione) e il **pacchetto. nupkg** verrà salvata nel file del pacchetto NuGet il **/bin/** (cartella Debug o Release, a seconda della configurazione).

  ![](platform-specific-images/create-nuget-package.png "File del pacchetto NuGet verrà salvato nella cartella bin Debug o Release, a seconda della configurazione")


## <a name="verifying-the-output"></a>Verifica per determinare se l'Output

I pacchetti NuGet sono anche i file con estensione ZIP, pertanto è possibile esaminare la struttura interna del pacchetto generato.

La schermata seguente mostra il contenuto del pacchetto NuGet specifico della piattaforma che supporta iOS e Android e due degli assembly di riferimento di cui è stato selezionato:

![](platform-specific-images/nuget-output.png "File contenuti nel pacchetto NuGet")


## <a name="related-links"></a>Collegamenti correlati

- [Guida ai metadati](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
