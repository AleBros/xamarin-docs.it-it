---
title: Opzioni del linker Xamarin.Mac
description: "Il collegamento è un potente strumento di ottimizzazione che riduce le dimensioni dell'applicazione rimuovendo il codice inutilizzato."
ms.topic: article
ms.prod: xamarin
ms.assetid: F03176C3-F8D4-4DE8-870C-7F27D8CE525A
ms.technology: xamarin-mac
author: bradumbaugh
ms.author: brumbaug
ms.date: 11/10/2017
ms.openlocfilehash: bee5f86682048fcd72d2212706c188c894eb148a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="xamarinmac-linker-options"></a>Opzioni del linker Xamarin.Mac

_Il collegamento è un potente strumento di ottimizzazione che riduce le dimensioni dell'applicazione rimuovendo il codice inutilizzato._

## <a name="overview"></a>Panoramica

A seconda del [framework di destinazione](~/mac/platform/target-framework.md) usato dal progetto, le opzioni del linker disponibili possono essere limitate. Questo è dovuto al fatto che il collegamento richiede la creazione di un oggetto grafico di ogni tipo usato dall'applicazione e questo non è possibile nel framework completo (o non supportato) a causa di System.Configuration.

Sono disponibili quattro opzioni:

- **Nessuno**: disabilita del tutto il collegamento. Opzione predefinita nella configurazione di debug nel framework moderno e in tutte le configurazioni nel framework completo.
- **SDK**: collega tutti gli assembly SDK, esclusi gli assembly utente. Opzione predefinita nella configurazione di rilascio nel framework moderno. Non è disponibile nel framework completo.
- **Completo**: collega tutti gli assembly. Questa opzione richiede che il codice utente sia indipendente dal linker. Per altre informazioni, vedere le [note](~/ios/deploy-test/linker.md). Non è disponibile nel framework completo.
- **Piattaforma**: collega solo Xamarin.Mac.dll. Di seguito sono riportate informazioni dettagliate.

## <a name="platform-linking"></a>Collegamento della piattaforma

Il collegamento di applicazioni tramite il [framework di destinazione](~/mac/platform/target-framework.md) completo è in genere poco sicuro, ma esiste un certo numero di scenari in cui è necessaria una forma molto limitata di collegamento.

Ad esempio, le applicazioni inviate a macOS App Store non devono fare riferimento a determinate API deprecate, come QTKit, di alcune delle quali Xamarin.Mac contiene binding. Anche se un'applicazione non chiama questi binding, la chiamata sarà presente nell'SDK e verrà rifiutata.

Il collegamento della piattaforma presuppone che l'applicazione e la libreria di classi di base non siano indipendenti dal linker e rimuove solo il codice inutilizzato da Xamarin.Mac.dll. 

Qualsiasi applicazione che non esegue la reflection sui tipi Xamarin.Mac.dll registrerà un miglioramento minimo all'avvio grazie alla rimozione dei tipi non necessari.

Il collegamento della piattaforma è in genere utile solo per le applicazioni che usano il framework di destinazione completo, perché le applicazioni moderne possono usare l'opzione SDK, che è più potente.

## <a name="setting-the-linker-configuration"></a>Impostazione della configurazione del linker

Per passare alla configurazione del linker per un progetto Xamarin.Mac, eseguire le operazioni seguenti:

1. Aprire il progetto Xamarin.Mac in Visual Studio per Mac.
2. In **Esplora soluzioni** fare doppio clic sul file di progetto per aprire la finestra di dialogo **Opzioni progetto**.
3. Nella scheda **Compilazione Mac** selezionare il tipo di comportamento più adatto per le esigenze dell'applicazione in **Comportamento del linker**:

  ![Scegliere il comportamento del linker da usare](linker-images/link-behavior.png "Scegliere il comportamento del linker da usare")

4. Il collegamento della piattaforma per framework di destinazione completi non viene visualizzato nell'IDE fino a un aggiornamento successivo. Fino a quel momento, aggiungere invece `--linkplatform` in **Argomenti aggiuntivi di mmp**.
5. Fare clic sul pulsante **OK** per salvare le modifiche.


## <a name="related-links"></a>Collegamenti correlati

- [Collegamento in iOS](~/ios/deploy-test/linker.md)
