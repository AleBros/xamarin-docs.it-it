---
title: Aggiornamento delle applicazioni unificata di xamarin. Mac a 64 bit
description: Questa guida descrive come aggiornare le applicazioni xamarin. Mac di destinazione a 64 bit. Vengono inoltre forniti esempi dei tipi di errori che possono essere rilevati quando si apporta questa modifica.
ms.prod: xamarin
ms.assetid: C3810A74-539C-4FFB-B47F-68CA5F7BCDAD
author: conceptdev
ms.author: crdun
ms.date: 02/22/2018
ms.openlocfilehash: 9bd70fec5d6d3bbbc4855980e1542bd4e486acaa
ms.sourcegitcommit: 849bf6d1c67df943482ebf3c80c456a48eda1e21
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2018
ms.locfileid: "51528520"
---
# <a name="updating-xamarinmac-unified-applications-to-64-bit"></a>Aggiornamento delle applicazioni unificata di xamarin. Mac a 64 bit

A partire da gennaio 2018, Apple richiede che i nuovi [invii di Mac App Store di destinazione a 64 bit](https://developer.apple.com/news/?id=06282017a). Dal giugno 2018, è necessario aggiornare le app già disponibili in Store di App Mac di destinazione a 64 bit.

Il **File** > **New** modello di progetto xamarin. Mac consente di creare applicazioni a 64 bit per impostazione predefinita, in modo da qualsiasi App creata di recente sono già compatibile a 64 bit e non richiederà modifiche.

## <a name="targeting-64-bit"></a>Destinato a 64 bit

1. Aprire il **opzioni progetto** finestra per l'app xamarin. Mac:

   ![Il menu di scelta rapida per il progetto](mac-64-bit-images/1-contextual_menu-vsmac.png "il menu di scelta rapida per il progetto")

2. Selezionare **compilazione Mac** e impostare **architetture supportate** al **x86\_64**:

   [![Impostazione di architetture supportate su x86_64](mac-64-bit-images/2-project_options-vsmac.png "impostando le architetture supportate su x86_64")](mac-64-bit-images/2-project_options-vsmac-large.png#lightbox)

3. Se l'app ha le dipendenze esterne, ad esempio progetti di binding o riferimenti nativi, aggiornarli alla destinazione a 64 bit.

### <a name="errors"></a>Errori

La prima volta di compilare o eseguire l'applicazione con il supporto a 64 bit, si potrebbero verificare errori di collegamento dai problemi di runtime o clang. Questi errori possono verificarsi se terze parti le dipendenze, ad esempio, i riferimenti nativi in xamarin. Mac o i progetti di binding, caricare manualmente i Framework a livello di sistema, ovvero non sono stati aggiornati a 64 bit.

> [!TIP]
> La conversione del progetto a 64 bit è un cambiamento radicale, potrebbero emergere indirettamente gli errori di programmazione diversi. In particolare può cambiare le dimensioni e allineamento di strutture di dati, che influirebbe firme p/invoke e codice nativo collegato nel progetto. Esaminare eventuali avvisi dati di compilazione e testare accuratamente l'applicazione in un secondo momento per rilevare potenziali problemi.

#### <a name="example-error-resulting-from-a-dynamically-linked-third-party-dependency-that-does-not-target-64-bit"></a>Errore di esempio risultante da una dipendenza di terze parti collegati in modo dinamico che non è destinata a 64 bit:

```console
ld : warning : ignoring file PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary, 
file was built for i386 which is not the architecture being linked (x86_64): 
PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary 
```

Questo errore potrebbe essere seguito in fase di esecuzione dal `dlopen` restituzione `IntPtr.Zero` anziché un handle del previsto.

#### <a name="example-error-resulting-from-a-statically-linked-third-party-dependency-that-does-not-target-64-bit"></a>Errore di esempio risultante da una dipendenza di terze parti collegate staticamente che non è destinata a 64 bit:

```console
Undefined symbols for architecture x86_64:
  "_LibraryFunction", referenced from:
     -u command line option
ld: symbol(s) not found for architecture x86_64 
```

Per compilare ed eseguire correttamente, queste dipendenze a 64 bit e ricompilare l'app.

