---
title: Modifica dei metadati NuGet
description: Questo documento descrive come usare le opzioni del progetto per modificare i metadati NuGet per le librerie multipiattaforma. Vengono illustrati i metadati obbligatori e facoltativi.
ms.prod: xamarin
ms.assetid: 147BA370-67A7-4E6C-BF17-AA7C536C0A48
author: conceptdev
ms.author: crdun
ms.date: 03/23/2017
ms.openlocfilehash: 125412ec229f07c4515f42e4df7996d90f87a67b
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70766548"
---
# <a name="editing-nuget-metadata"></a>Modifica dei metadati NuGet

_Usare le opzioni del progetto per modificare i metadati NuGet per le librerie multipiattaforma_

I tipi di progetto di libreria (ad esempio PCL o .NET Standard o il nuovo tipo di progetto NuGet) hanno una sezione **pacchetto NuGet** nella finestra **Opzioni progetto** .

La sezione **Metadata** configura i valori usati nel [file manifesto del pacchetto NuGet **. NuSpec** ](https://docs.microsoft.com/nuget/create-packages/creating-a-package#the-role-and-structure-of-the-nuspec-file).

## <a name="required-information"></a>Informazioni obbligatorie

La scheda **generale** contiene quattro campi che devono essere immessi per generare un pacchetto NuGet:

[![](metadata-images/metadata-general-sml.png "Finestra dei metadati necessaria del pacchetto NuGet")](metadata-images/metadata-general.png#lightbox)

- **ID** : l'identificatore del pacchetto, che deve essere univoco all'interno di NuGet.org (o ogni volta che il pacchetto verrà distribuito). Seguire queste [indicazioni](https://docs.microsoft.com/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number) e usare solo i caratteri validi in un URL (senza spazi ed evitare la maggior parte dei caratteri speciali).
- **Versione** : scegliere un numero di versione coerente con [le regole di controllo delle versioni di NuGet](https://docs.microsoft.com/nuget/create-packages/dependency-versions).
- **Authors** : elenco delimitato da virgole di nomi.
- **Descrizione** : Panoramica delle funzionalità del pacchetto visualizzate quando gli utenti selezionano il pacchetto.

> [!NOTE]
> Ricordarsi di incrementare il numero di versione quando si compilano nuove versioni per la distribuzione in NuGet o altri utenti.

Per ulteriori informazioni, vedere il [riferimento agli elementi necessari](https://docs.microsoft.com/nuget/schema/nuspec#required-metadata-elements) per ulteriori informazioni, nonché le istruzioni dettagliate sulla [scelta di un identificatore univoco del pacchetto e sull'impostazione del numero di versione e dell'](https://docs.microsoft.com/nuget/create-packages/creating-a-package#choosing-a-unique-package-identifier-and-setting-the-version-number) [impostazione di un tipo di pacchetto](https://docs.microsoft.com/nuget/create-packages/creating-a-package#setting-a-package-type).

> [!IMPORTANT]
> È necessario immettere tutti i campi in questa scheda; in caso contrario, verrà visualizzato un messaggio di errore: _"Il progetto non contiene metadati NuGet, quindi non verrà creato un pacchetto NuGet. I metadati del pacchetto NuGet possono essere specificati nella sezione metadati in opzioni progetto "_

## <a name="optional-metadata"></a>Metadati facoltativi

La scheda **Dettagli** contiene i campi facoltativi da includere nel file manifesto del pacchetto NuGet.

[![](metadata-images/metadata-detail-sml.png "Finestra dei metadati facoltativi del pacchetto NuGet")](metadata-images/metadata-detail.png#lightbox)

Per ulteriori informazioni sui campi obbligatori e facoltativi, vedere [riferimento agli elementi facoltativi](https://docs.microsoft.com/nuget/schema/nuspec#optional-metadata-elements) .

> [!NOTE]
> Se il pacchetto NuGet viene distribuito in [NuGet.org](https://www.nuget.org) , è consigliabile fornire quante più informazioni possibile.

## <a name="related-links"></a>Collegamenti correlati

- [Informazioni di riferimento sul file .nuspec](https://docs.microsoft.com/nuget/schema/nuspec#general-form-and-schema)
