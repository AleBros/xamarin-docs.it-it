---
title: Creazione di nuovi progetti di libreria specifici per la piattaforma per NuGet
description: Questo documento descrive come creare un singolo pacchetto NuGet che contiene il codice specifico della piattaforma per più piattaforme.
ms.prod: xamarin
ms.assetid: D8BC4906-805F-4AFB-8D1A-88B7BF87E17F
author: conceptdev
ms.author: crdun
ms.date: 03/23/2017
ms.openlocfilehash: 73f44acad3e30e4301a69e5f2422cd4dd1a3dbf5
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70766568"
---
# <a name="creating-new-platform-specific-library-projects-for-nuget"></a>Creazione di nuovi progetti di libreria specifici per la piattaforma per NuGet

I progetti di libreria multipiattaforma destinati a piattaforme specifiche, ad esempio iOS e Android, funzionano meglio con i progetti condivisi.

NuGet può contenere sia codice specifico per iOS che Android, nonché codice .NET comune a entrambi.

Vengono creati più assembly e incorporati in un singolo pacchetto NuGet. Gli standard NuGet assicurano che il pacchetto possa essere aggiunto a tutti i tipi di progetto supportati, ad esempio i progetti Novell. iOS e Android.

## <a name="steps-to-create-a-cross-platform-library-nuget"></a>Passaggi per creare una libreria multipiattaforma NuGet

1. Selezionare **File > nuova soluzione** oppure fare clic con il pulsante destro del mouse su una soluzione esistente e scegliere **Aggiungi > nuovo progetto**.

2. Scegliere **libreria multipiattaforma** dalla sezione **Libreria > multipiattaforma** :

    [![](platform-specific-images/mulitplatform-library-sml.png "Configurare la libreria multipiattaforma per una singola codebase")](platform-specific-images/multiplatform-library.png#lightbox)

3. Immettere un **nome** e una **Descrizione**e scegliere **specifico della piattaforma**:

    [![](platform-specific-images/specific-configure-sml.png "Configurare la libreria specifica della piattaforma per iOS e Android")](platform-specific-images/specific-configure.png#lightbox)

4. Completare la procedura guidata. Alla soluzione vengono aggiunti i progetti seguenti:

    - **Progetto Android** : il codice specifico per Android può essere aggiunto facoltativamente a questo progetto.
    - **progetto iOS** : il codice specifico per iOS può essere aggiunto facoltativamente a questo progetto.
    - **Progetto NuGet** : nessun codice aggiunto al progetto. Fa riferimento agli altri progetti e contiene la configurazione dei metadati per l'output del pacchetto NuGet.
    - **Progetto condiviso** : il codice comune deve essere aggiunto a questo progetto, incluso il codice specifico della `#if` piattaforma all'interno delle direttive del compilatore.

5. Fare clic con il pulsante destro del mouse sul progetto NuGet e scegliere **Opzioni**, quindi aprire il **pacchetto NuGet > sezione metadati** e immettere i [metadati richiesti](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) (oltre ai metadati facoltativi):

    [![](platform-specific-images/specific-metadata-sml.png "Immettere i metadati necessari")](platform-specific-images/specific-metadata.png#lightbox)

6. Inoltre, nella finestra **Opzioni progetto** aprire la sezione **assembly di riferimento** e scegliere i profili PCL che la libreria condivisa supporterà tramite "Bait and switch":

    ![](platform-specific-images/specific-reference-assemblies.png "Inoltre, nella finestra Opzioni progetto aprire la sezione assembly di riferimento e scegliere i profili PCL che la libreria condivisa supporterà tramite esche e switch")

    > [!NOTE]
    > "Bait and switch" significa che gli assembly PCL conterranno solo l'API esposta dalla libreria (non può contenere il codice specifico della piattaforma). Quando NuGet viene aggiunto a un progetto Novell, le librerie condivise vengono compilate sulla libreria di classi portabile, ma gli assembly specifici della piattaforma contengono il codice effettivamente usato dal progetto iOS o Android.

7. Fare clic con il pulsante destro del mouse sul progetto e scegliere **Crea pacchetto NuGet** (o compila o Distribuisci la soluzione) e il file del pacchetto NuGet **. nupkg** verrà salvato nella cartella **/bin/** (debug o versione, a seconda della configurazione).

    ![](platform-specific-images/create-nuget-package.png "Il file del pacchetto NuGet verrà salvato nella cartella bin debug o release, a seconda della configurazione")

## <a name="verifying-the-output"></a>Verifica dell'output

I pacchetti NuGet sono anche file ZIP, quindi è possibile controllare la struttura interna del pacchetto generato.

Questa schermata mostra il contenuto di un NuGet specifico della piattaforma che supporta iOS e Android ed è stato selezionato due assembly di riferimento:

![](platform-specific-images/nuget-output.png "File contenuti nel pacchetto NuGet")

## <a name="related-links"></a>Collegamenti correlati

- [Guida ai metadati](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
