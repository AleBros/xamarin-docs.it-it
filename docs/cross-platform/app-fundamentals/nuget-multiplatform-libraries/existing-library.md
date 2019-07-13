---
title: Creazione pacchetto NuGet da progetti di libreria esistenti
description: Questo documento descrive come creare un pacchetto NuGet da un progetto di libreria esistente, consentendo il codice da condividere con altri sviluppatori.
ms.prod: xamarin
ms.assetid: EDAC3E5E-DB7D-40A9-AE28-45C52ADA854E
author: asb3993
ms.author: amburns
ms.date: 04/20/2017
ms.openlocfilehash: 6e043334d3ca45a573423ebdfdf1ec9149167b55
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864698"
---
# <a name="creating-a-nuget-from-existing-library-projects"></a>Creazione pacchetto NuGet da progetti di libreria esistenti

Le librerie .NET Standard o libreria di classi Portabile esistente possono essere trasformate in pacchetti NuGet tramite il **opzioni progetto** finestra:

1. Pulsante destro del mouse sul progetto di libreria nel **riquadro della soluzione** e scegliere **opzioni**.

2. Passare al **pacchetto NuGet > metadati** della sezione e immettere tutte le [informazioni necessarie](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) nel **generale** scheda:

   [![](existing-library-images/existing-metadata-sml.png "Immettere i metadati obbligatori")](existing-library-images/existing-metadata.png#lightbox)

3. Facoltativamente [aggiungere i metadati aggiuntivi](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) nel **dettagli** scheda.

4. Dopo aver configurato i metadati, è possibile fare doppio clic sul progetto e scegliere **creazione di pacchetti NuGet** e il **pacchetto. nupkg** verrà salvata nel file del pacchetto NuGet il **/bin/** cartella (Debug o Release, a seconda della configurazione).

   ![](existing-library-images/create-nuget-package.png "Scegliere di creare pacchetti NuGet dal menu di scelta rapida")

5. Per creare il pacchetto NuGet nel _ogni_ compilare o distribuire, andare alla **pacchetto NuGet > compilare** sezione e segni di graduazione **creare un pacchetto NuGet durante la compilazione del progetto**:

    [![](existing-library-images/existing-tickbox-sml.png "Segno di spunta per creare un pacchetto NuGet")](existing-library-images/existing-tickbox.png#lightbox)

> [!NOTE]
> Compilazione di NuGet package può rallentare il processo di compilazione. Se questa casella non sia selezionata, è comunque possibile generare un pacchetto NuGet a manualmente in qualsiasi momento dal menu di scelta rapida progetto (illustrato nel passaggio 4 sopra).

## <a name="verifying-the-output"></a>Verifica per determinare se l'Output

I pacchetti NuGet sono anche i file con estensione ZIP, pertanto è possibile esaminare la struttura interna del pacchetto generato.

Questa schermata mostra il contenuto del pacchetto NuGet basato su libreria di classi Portabile, che è incluso solo un singolo assembly libreria di classi Portabile:

![](existing-library-images/nuget-output.png "File contenuti nel pacchetto NuGet")


## <a name="related-links"></a>Collegamenti correlati

- [Guida ai metadati](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
