---
title: Creazione di un NuGet da progetti di libreria esistente
ms.prod: xamarin
ms.assetid: EDAC3E5E-DB7D-40A9-AE28-45C52ADA854E
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 04/20/2017
ms.openlocfilehash: 36de23c2a6db817805cafae282d227cc5ef15cc4
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="creating-a-nuget-from-existing-library-projects"></a>Creazione di un NuGet da progetti di libreria esistente

Librerie PCL esistente o .NET Standard possono essere trasformate in NuGets tramite il **opzioni progetto** finestra:

1. Pulsante destro del mouse sul progetto libreria di **riempimento soluzione** e scegliere **opzioni**.

2. Passare al **pacchetto NuGet > metadati** sezione e immettere tutte le [le informazioni necessarie](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) nel **generale** scheda:

  [![](existing-library-images/existing-metadata-sml.png "Immettere i metadati necessari")](existing-library-images/existing-metadata.png#lightbox)

3. Facoltativamente, [aggiungere metadati aggiuntivi](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md) nel **dettagli** scheda.

4. Dopo aver configurato i metadati, è possibile fare doppio clic sul progetto e scegliere **Crea pacchetto NuGet** e **.nupkg** verrà salvata nel file del pacchetto NuGet di **/bin/** cartella (Debug o Release, a seconda della configurazione).

  ![](existing-library-images/create-nuget-package.png "Scegliere Crea pacchetto NuGet dal menu di scelta rapida")

5. Per creare il pacchetto NuGet in _ogni_ compilare o distribuire, visitare il **pacchetto NuGet > compilare** sezione e segni di graduazione **creare un pacchetto NuGet durante la compilazione del progetto**:

    [![](existing-library-images/existing-tickbox-sml.png "Segno di spunta per creare un pacchetto NuGet")](existing-library-images/existing-tickbox.png#lightbox)

> [!NOTE]
> Compilazione di NuGet pacchetto può rallentare il processo di compilazione. Se questa casella non è selezionata, è comunque possibile generare un pacchetto NuGet a manualmente in qualsiasi momento da scelta rapida del progetto (indicato nel passaggio 4 sopra).

## <a name="verifying-the-output"></a>Verifica per determinare se l'Output

Pacchetti NuGet sono anche i file ZIP, pertanto è possibile esaminare la struttura interna del pacchetto generato.

Questa schermata è riportato il contenuto di un NuGet basata sulla libreria di classi Portabile: è incluso un singolo assembly libreria di classi Portabile:

![](existing-library-images/nuget-output.png "File contenuti nel pacchetto NuGet.")


## <a name="related-links"></a>Collegamenti correlati

- [Guida ai metadati](~/cross-platform/app-fundamentals/nuget-multiplatform-libraries/metadata.md)
