---
title: Modifica dei metadati NuGet
description: Questo documento descrive come usare le opzioni di progetto per modificare i metadati di NuGet per le librerie multipiattaforma. Vengono illustrati i metadati obbligatori e facoltativi.
ms.prod: xamarin
ms.assetid: 147BA370-67A7-4E6C-BF17-AA7C536C0A48
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 3680b02003a844668b0b5c97e5d4c0d296ae3500
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61266870"
---
# <a name="editing-nuget-metadata"></a>Modifica dei metadati NuGet

_Usare le opzioni di progetto per modificare i metadati di NuGet per le librerie multipiattaforma_

Tipi di progetto di libreria (ad esempio libreria di classi Portabile .NET Standard o al nuovo tipo di progetto NuGet) hanno una **pacchetto NuGet** sezione il **opzioni progetto** finestra.

Il **metadati** sezione Configura i valori utilizzati per il [ **con estensione nuspec** file manifesto del pacchetto NuGet](https://docs.microsoft.com/nuget/create-packages/creating-a-package#the-role-and-structure-of-the-nuspec-file).

## <a name="required-information"></a>Informazioni necessarie

Il **generale** scheda contiene quattro campi che devono essere immessi per generare un pacchetto NuGet:

[![](metadata-images/metadata-general-sml.png "Finestra metadati necessari pacchetti NuGet")](metadata-images/metadata-general.png#lightbox)

- **ID** : l'identificatore del pacchetto, che deve essere univoco in Nuget.org (o punto in cui verrà distribuito il pacchetto). Seguire questo [materiale sussidiario](https://docs.microsoft.com/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number) e usare solo caratteri validi in un URL (senza spazi ed evitare caratteri speciali).
- **Versione** : scegliere un numero di versione conforme [regole di controllo delle versioni di NuGet](https://docs.microsoft.com/nuget/create-packages/dependency-versions).
- **Gli autori** : elenco delimitato da virgole di nomi.
- **Descrizione** : panoramica delle funzionalità del pacchetto che viene visualizzato quando gli utenti selezionano il pacchetto.

> [!NOTE]
> Ricordare di incrementare il numero di versione durante la creazione di nuove versioni per la distribuzione di NuGet o ad altri utenti.

Per altre informazioni, vedere la [riferimento agli elementi necessari](https://docs.microsoft.com/nuget/schema/nuspec#required-metadata-elements) per altre informazioni, nonché come queste istruzioni dettagliate sul [scelta di un identificatore univoco del pacchetto e impostazione del numero di versione](https://docs.microsoft.com/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number) e[ Impostazione di un tipo di pacchetto](https://docs.microsoft.com/nuget/create-packages/creating-a-package#setting-a-package-type).

> [!IMPORTANT]
> È necessario immettere tutti i campi in questa scheda; in caso contrario, verrà visualizzato un messaggio di errore: _"Il progetto non dispone dei metadati NuGet in modo che non verrà creato un pacchetto NuGet. I metadati del pacchetto NuGet possono essere specificato nella sezione dei metadati in Opzioni progetto"_

## <a name="optional-metadata"></a>Metadati facoltativi

Il **dettagli** scheda contiene i campi facoltativi da includere nel file manifesto del pacchetto NuGet.

[![](metadata-images/metadata-detail-sml.png "Finestra metadati facoltativi del pacchetto NuGet")](metadata-images/metadata-detail.png#lightbox)

Vedere la [riferimento agli elementi facoltativi](https://docs.microsoft.com/nuget/schema/nuspec#optional-metadata-elements) per altre informazioni sui campi obbligatori e facoltativi.

> [!NOTE]
> Se viene distribuito tramite il pacchetto NuGet [NuGet.org](https://www.nuget.org) , è consigliabile fornire quante più informazioni possibili.


## <a name="related-links"></a>Collegamenti correlati

- [Informazioni di riferimento sul file .nuspec](https://docs.microsoft.com/nuget/schema/nuspec#general-form-and-schema)
