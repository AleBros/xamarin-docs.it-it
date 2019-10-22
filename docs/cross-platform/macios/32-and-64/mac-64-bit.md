---
title: Aggiornamento di applicazioni unificate Novell. Mac a 64 bit
description: Questa guida descrive come aggiornare le applicazioni Novell. Mac alla destinazione 64 bit. Vengono inoltre forniti esempi dei tipi di errori che possono verificarsi durante l'esecuzione di questa modifica.
ms.prod: xamarin
ms.assetid: C3810A74-539C-4FFB-B47F-68CA5F7BCDAD
author: conceptdev
ms.author: crdun
ms.date: 02/22/2018
ms.openlocfilehash: 5539bab417c5efc0064cd1753cb74c7524463ee5
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "70765914"
---
# <a name="updating-xamarinmac-unified-applications-to-64-bit"></a>Aggiornamento di applicazioni unificate Novell. Mac a 64 bit

A partire dal 2018 gennaio Apple richiede che i nuovi [invii di Mac App Store siano destinati a 64 bit](https://developer.apple.com/news/?id=06282017a). Le app già disponibili in Mac App Store devono essere aggiornate per la destinazione 64 bit entro il 2018 giugno.

Il **File**  > **nuovo** modello di progetto Novell. Mac crea applicazioni a 64 bit per impostazione predefinita, pertanto le app create di recente sono già compatibili a 64 bit e non richiedono alcuna modifica.

## <a name="targeting-64-bit"></a>Destinazione 64 bit

1. Aprire la finestra delle **Opzioni del progetto** per l'app Novell. Mac:

   ![Menu contestuale per il progetto](mac-64-bit-images/1-contextual_menu-vsmac.png "Menu contestuale per il progetto")

2. Selezionare **compilazione Mac** e impostare le **architetture supportate** su **x86 \_64**:

   [![Impostazione delle architetture supportate su x86_64](mac-64-bit-images/2-project_options-vsmac.png "Impostazione delle architetture supportate su x86_64")](mac-64-bit-images/2-project_options-vsmac-large.png#lightbox)

3. Se l'app presenta dipendenze esterne, ad esempio riferimenti nativi o progetti di associazione, aggiornarle a 64 bit di destinazione.

### <a name="errors"></a>Errori

La prima volta che si compila o si esegue l'applicazione con supporto a 64 bit, è possibile che si verifichino errori di collegamento da Clang o da problemi di Runtime. Questi errori possono verificarsi se le dipendenze di terze parti, ad esempio i riferimenti nativi nei progetti Novell. Mac o bindings o i Framework a livello di sistema caricati manualmente, non sono stati aggiornati a 64 bit.

> [!TIP]
> La conversione del progetto in 64 bit è una modifica sostanziale e può rilevare indirettamente diversi errori di programmazione. In particolare, può modificare le dimensioni e l'allineamento delle strutture di dati, che potrebbero influenzare le firme p/invoke e il codice nativo collegato nel progetto. Esaminare tutti gli avvisi di compilazione specificati e testare l'applicazione in seguito per rilevare potenziali problemi.

#### <a name="example-error-resulting-from-a-dynamically-linked-third-party-dependency-that-does-not-target-64-bit"></a>Errore di esempio risultante da una dipendenza di terze parti collegata in modo dinamico che non è destinata a 64 bit:

```console
ld : warning : ignoring file PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary, 
file was built for i386 which is not the architecture being linked (x86_64): 
PATH/ThirdPartyLibrary.framework/ThirdPartyLibrary 
```

Questo errore può essere seguito in fase di esecuzione `dlopen` restituendo `IntPtr.Zero` invece di un handle previsto.

#### <a name="example-error-resulting-from-a-statically-linked-third-party-dependency-that-does-not-target-64-bit"></a>Errore di esempio risultante da una dipendenza di terze parti collegata in modo statico che non è destinata a 64 bit:

```console
Undefined symbols for architecture x86_64:
  "_LibraryFunction", referenced from:
     -u command line option
ld: symbol(s) not found for architecture x86_64 
```

Per compilare ed eseguire correttamente, aggiornare le dipendenze a 64 bit e ricompilare l'app.
