---
title: Aggiornamento delle applicazioni unificata Xamarin.Mac a 64 bit
description: Questa guida viene descritto come aggiornare le applicazioni Xamarin.Mac di destinazione a 64 bit
ms.prod: xamarin
ms.assetid: C3810A74-539C-4FFB-B47F-68CA5F7BCDAD
author: bradumbaugh
ms.author: brumbaug
ms.date: 02/22/2018
ms.openlocfilehash: 558edbdee5adfe57205c7f76b35a0538c78b927f
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2018
---
# <a name="updating-xamarinmac-unified-applications-to-64-bit"></a>Aggiornamento delle applicazioni unificata Xamarin.Mac a 64 bit

A partire da gennaio 2018 Apple richiede che i nuovi [gli invii in archivio di App Mac di destinazione a 64 bit](https://developer.apple.com/news/?id=06282017a). Da giugno 2018, è necessario aggiornare l'App già disponibili in Mac App Store di destinazione a 64 bit.

Il **File** > **New** Xamarin.Mac modello consente di creare applicazioni a 64 bit per impostazione predefinita, in modo che le app create di recente sono già compatibile a 64 bit e non richiede tutte le modifiche.

## <a name="targeting-64-bit"></a>Destinazione a 64 bit

1. Aprire il **opzioni progetto** finestra per l'utente sta Xamarin.Mac app:

   ![Menu di scelta rapida per il progetto](mac-64-bit-images/1-contextual_menu-vsmac.png "menu di scelta rapida per il progetto")

2. Selezionare **Mac compilare** e impostare **architetture supportate** a **x86\_64**:

   [![L'impostazione di architetture supportate su x86_64](mac-64-bit-images/2-project_options-vsmac.png "impostare x86_64 delle architetture supportate")](mac-64-bit-images/2-project_options-vsmac-large.png#lightbox)

3. Se l'applicazione ha dipendenze esterne, ad esempio i riferimenti nativi o progetti di associazione, è possibile aggiornarli alla destinazione a 64 bit.

### <a name="errors"></a>Errori

La prima volta, si compila o si esegue l'applicazione con supporto a 64 bit, si potrebbero verificare errori di collegamento da problemi di runtime o clang. Questi errori possono verificarsi se terze parti le dipendenze, ad esempio, i riferimenti nativi Xamarin.Mac o progetti di associazioni, caricati manualmente Framework a livello di sistema, non sono stati aggiornati a 64 bit.

> [!TIP]
> Conversione del progetto a 64 bit è una modifica importante e indirettamente rilevi errori di programmazione diversi. In particolare può modificare le dimensioni e allineamento di strutture di dati, che influirebbe sulle firme di p/invoke e codice nativo collegato nel progetto. È consigliabile rivedere gli avvisi di compilazione specificati e testare l'applicazione accurati in un secondo momento per rilevare potenziali problemi.

#### <a name="example-error-resulting-from-a-dynamically-linked-third-party-dependency-that-does-not-target-64-bit"></a>Errore di esempio risultante da una dipendenza di terze parti collegati in modo dinamico non è destinata a 64 bit:

```console
ld : warning : ignoring file PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary, 
file was built for i386 which is not the architecture being linked (x86_64): 
PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary 
```

Questo errore potrebbe essere seguito in fase di esecuzione da `dlopen` restituzione `IntPtr.Zero` anziché un handle previsto.

#### <a name="example-error-resulting-from-a-statically-linked-third-party-dependency-that-does-not-target-64-bit"></a>Errore di esempio risultante da una dipendenza di terze parti collegate staticamente che non è destinata a 64 bit:

```console
Undefined symbols for architecture x86_64:
  "_LibraryFunction", referenced from:
     -u command line option
ld: symbol(s) not found for architecture x86_64 
```

Per compilare ed eseguire correttamente, queste dipendenze a 64 bit e ricompilare l'app.

