---
title: Creazione di un NuGet da progetti di libreria esistenti
description: Questo documento descrive come creare un pacchetto NuGet da un progetto di libreria esistente, consentendo la condivisione del codice con altri sviluppatori.
ms.prod: xamarin
ms.assetid: EDAC3E5E-DB7D-40A9-AE28-45C52ADA854E
author: davidortinau
ms.author: daortin
ms.date: 04/20/2017
ms.openlocfilehash: 30158056b8adbdd5aba4cc311220a22502ea9968
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016790"
---
# <a name="creating-a-nuget-from-existing-library-projects"></a>Creazione di un NuGet da progetti di libreria esistenti

Le librerie PCL o .NET Standard esistenti possono essere convertite in NuGet tramite la finestra delle **Opzioni del progetto** :

1. Fare clic con il pulsante destro del mouse sul progetto libreria nella **riquadro della soluzione** e scegliere **Opzioni**.

2. Passare alla sezione **dei metadati del pacchetto NuGet >** e immettere tutte le [informazioni necessarie](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) nella scheda **generale** :

   [![](existing-library-images/existing-metadata-sml.png "Enter required metadata")](existing-library-images/existing-metadata.png#lightbox)

3. Facoltativamente, [aggiungere metadati aggiuntivi](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) nella scheda **Dettagli** .

4. Una volta configurati i metadati, è possibile fare clic con il pulsante destro del mouse sul progetto e scegliere **Crea pacchetto NuGet** e il file del pacchetto NuGet **. nupkg** verrà salvato nella cartella **/bin/** (debug o versione, a seconda della configurazione).

   ![](existing-library-images/create-nuget-package.png "Choose Create NuGet Package from the right-click menu")

5. Per creare il pacchetto NuGet in _ogni_ compilazione o distribuzione, passare al **pacchetto NuGet > sezione compilazione** e selezionare **Crea un pacchetto NuGet durante la compilazione del progetto**:

    [![](existing-library-images/existing-tickbox-sml.png "Tick to create a NuGet package")](existing-library-images/existing-tickbox.png#lightbox)

> [!NOTE]
> La creazione del pacchetto NuGet può rallentare il processo di compilazione. Se questa casella non è selezionata, è comunque possibile generare manualmente un pacchetto NuGet in qualsiasi momento dal menu di scelta rapida del progetto (illustrato nel passaggio 4 precedente).

## <a name="verifying-the-output"></a>Verifica dell'output

I pacchetti NuGet sono anche file ZIP, quindi è possibile controllare la struttura interna del pacchetto generato.

Questa schermata mostra il contenuto di un NuGet basato su PCL. è incluso solo un singolo assembly PCL:

![](existing-library-images/nuget-output.png "Files contained in the NuGet package")

## <a name="related-links"></a>Collegamenti correlati

- [Guida ai metadati](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
