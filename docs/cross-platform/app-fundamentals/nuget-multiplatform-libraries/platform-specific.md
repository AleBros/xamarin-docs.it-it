---
title: Creazione di nuovi progetti di libreria specifica della piattaforma per NuGet
description: Questo documento descrive come creare un singolo pacchetto NuGet che contiene codice specifico della piattaforma per più piattaforme.
ms.prod: xamarin
ms.assetid: D8BC4906-805F-4AFB-8D1A-88B7BF87E17F
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 00a02973d6016ad63e4317279515acc2b4e2e81b
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780643"
---
# <a name="creating-new-platform-specific-library-projects-for-nuget"></a>Creazione di nuovi progetti di libreria specifica della piattaforma per NuGet

Progetti multipiattaforma di librerie destinate a piattaforme specifiche, ad esempio iOS e Android, funzionano meglio con i progetti condivisi.

NuGet può contenere sia codice iOS e Android specifico, nonché il codice .NET comune a entrambi.

Più assembly vengono creati e compilato in un singolo pacchetto NuGet. NuGet garantiscono che il pacchetto può essere aggiunto a tutti i tipi di progetto supportati, ad esempio progetti di xamarin. IOS e Android.

## <a name="steps-to-create-a-cross-platform-library-nuget"></a>Passaggi per creare una libreria multipiattaforma di NuGet

1. Selezionare **File > nuova soluzione** (o fare clic con il pulsante destro una soluzione esistente e scegliere **Aggiungi > Nuovo progetto**).

2. Scegliere **libreria multipiattaforma** dal **multipiattaforma > libreria** sezione:

  [![](platform-specific-images/mulitplatform-library-sml.png "Configurare una libreria multipiattaforma per una sola codebase")](platform-specific-images/multiplatform-library.png#lightbox)

3. Immettere un **nome** e **descrizione**e scegliere **specifica della piattaforma**:

  [![](platform-specific-images/specific-configure-sml.png "Configurare la libreria specifica della piattaforma per iOS e Android")](platform-specific-images/specific-configure.png#lightbox)

4. Completare la procedura guidata. I progetti seguenti vengono aggiunti alla soluzione:

  - **Progetto Android** : codice specifico per Android, facoltativamente, è possibile aggiungere a questo progetto.
  - **Progetto iOS** : codice specifico per iOS, facoltativamente, è possibile aggiungere a questo progetto.
  - **Progetto NuGet** : non viene aggiunto codice al progetto. Fa riferimento negli altri progetti e contiene la configurazione di metadati per l'output del pacchetto NuGet.
  - **Progetto condiviso** : codice comune deve essere aggiunto al progetto, incluso il codice specifico della piattaforma all'interno di `#if` direttive del compilatore.

5. Pulsante destro del mouse sul progetto NuGet e scegliere **opzioni**, quindi aprire il **pacchetto NuGet > metadati** sezione e immettere il [i metadati necessari](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) (come nonché facoltativi metadati):

  [![](platform-specific-images/specific-metadata-sml.png "Immettere i metadati necessari")](platform-specific-images/specific-metadata.png#lightbox)

6. Anche nel **opzioni progetto** finestra, aprire il **gli assembly di riferimento** sezione e scegliere quali profili PCL supporterà la libreria condivisa tramite "tecnica dello":

  ![](platform-specific-images/specific-reference-assemblies.png "Anche nella finestra Opzioni progetto, aprire la sezione di assembly di riferimento e scegliere quali profili PCL supporterà la libreria condivisa tramite tecnica dello")

  > [!NOTE]
> "Tecnica dello" significa che gli assembly libreria di classi Portabile conterrà solo le API esposta dalla libreria (non può contenere il codice specifico della piattaforma). Quando NuGet viene aggiunto a un progetto Xamarin, librerie condivise verranno compilate con libreria di classi Portabile, ma gli assembly specifico della piattaforma contengano il codice che viene utilizzato dal progetto Android o iOS.

7. Pulsante destro del mouse sul progetto e scegliere **Crea pacchetto NuGet** (o compilare o distribuire la soluzione) e **.nupkg** verrà salvata nel file del pacchetto NuGet di **/bin/** (cartella Debug o Release, a seconda della configurazione).

  ![](platform-specific-images/create-nuget-package.png "File del pacchetto NuGet salvata nella cartella bin Debug o Release, a seconda della configurazione")


## <a name="verifying-the-output"></a>Verifica per determinare se l'Output

Pacchetti NuGet sono anche i file ZIP, pertanto è possibile esaminare la struttura interna del pacchetto generato.

Questa schermata mostra il contenuto di un NuGet specifico della piattaforma che supporta iOS e Android e ha due assembly di riferimento selezionati:

![](platform-specific-images/nuget-output.png "File contenuti nel pacchetto NuGet.")


## <a name="related-links"></a>Collegamenti correlati

- [Guida ai metadati](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
