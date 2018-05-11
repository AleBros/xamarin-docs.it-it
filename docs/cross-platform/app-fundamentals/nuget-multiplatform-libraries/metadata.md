---
title: Modifica dei metadati di NuGet
description: Utilizzare le opzioni di progetto per modificare i metadati di NuGet per librerie multipiattaforma
ms.prod: xamarin
ms.assetid: 147BA370-67A7-4E6C-BF17-AA7C536C0A48
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: dc2bd64339aa3deacd640cc982af5ee52d76ff07
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="editing-nuget-metadata"></a>Modifica dei metadati di NuGet

_Utilizzare le opzioni di progetto per modificare i metadati di NuGet per librerie multipiattaforma_

Tipi di progetto di libreria (ad esempio PCL .NET Standard o il nuovo tipo di progetto NuGet) hanno un **pacchetto NuGet** sezione la **opzioni progetto** finestra.

Il **metadati** sezione Configura i valori utilizzati per il [ **. nuspec** file manifesto del pacchetto NuGet](https://docs.microsoft.com/nuget/create-packages/creating-a-package#the-role-and-structure-of-the-nuspec-file).

## <a name="required-information"></a>Informazioni necessarie

Il **generale** scheda contiene quattro campi che devono essere immessi per generare un pacchetto NuGet:

[![](metadata-images/metadata-general-sml.png "Finestra metadati richiesti del pacchetto NuGet")](metadata-images/metadata-general.png#lightbox)

- **ID** : l'identificatore del pacchetto, che deve essere univoco all'interno di Nuget.org (o punto in cui verrà distribuito il pacchetto). Seguire questo [indicazioni](https://docs.microsoft.com/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number) e usare solo caratteri validi in un URL (senza spazi ed evitare i caratteri speciali).
- **Versione** : scegliere un numero di versione coerenza con [le regole di controllo delle versioni di NuGet](https://docs.microsoft.com/nuget/create-packages/dependency-versions).
- **Gli autori** : elenco delimitato da virgole di nomi.
- **Descrizione** : panoramica delle funzionalità del pacchetto che viene visualizzato quando gli utenti selezionano il pacchetto.

> [!NOTE]
> Ricordare di incrementare il numero di versione durante la creazione di nuove versioni per la distribuzione di NuGet o ad altri utenti.

Per ulteriori informazioni, vedere il [richiesto riferimento agli elementi](https://docs.microsoft.com/nuget/schema/nuspec#required-metadata-elements) per ulteriori informazioni, nonché come queste indicazioni dettagliate su [scegliendo un identificatore univoco del pacchetto e impostando il numero di versione](https://docs.microsoft.com/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number) e [ L'impostazione di un tipo di pacchetto](https://docs.microsoft.com/nuget/create-packages/creating-a-package#setting-a-package-type).

> [!IMPORTANT]
> Tutti i campi in questa scheda devono essere attivati; in caso contrario, in cui verrà visualizzato un messaggio di errore: _"il progetto non dispone dei metadati di NuGet in modo non verrà creato un pacchetto NuGet. Nella sezione dei metadati nelle opzioni di progetto, è possono specificare metadati del pacchetto NuGet"_

## <a name="optional-metadata"></a>Metadati facoltativi

Il **dettagli** scheda contiene i campi facoltativi da includere nel file di manifesto del pacchetto NuGet.

[![](metadata-images/metadata-detail-sml.png "Finestra di metadati facoltative del pacchetto NuGet")](metadata-images/metadata-detail.png#lightbox)

Consultare il [riferimenti a elementi facoltativi](https://docs.microsoft.com/nuget/schema/nuspec#optional-metadata-elements) per ulteriori informazioni sui campi obbligatori e facoltativi.

> [!NOTE]
> Se viene distribuito il pacchetto NuGet [NuGet.org](https://www.nuget.org) si consiglia di fornire il maggior numero possibile di informazioni.


## <a name="related-links"></a>Collegamenti correlati

- [Informazioni di riferimento sul file .nuspec](https://docs.microsoft.com/nuget/schema/nuspec#general-form-and-schema)
