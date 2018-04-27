---
title: Dov'è il mio Mac?
description: Istruzioni per la configurazione di Mac per Visual Studio per compilare progetti Xamarin
ms.prod: xamarin
ms.assetid: 4f792690-b3b1-4d56-a5e2-363b4f246059
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: f3e2988c9700549db24ad69277df9e412c99caed
ms.sourcegitcommit: 797597d902330652195931dec9ac3e0cc00792c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2018
---
# <a name="wheres-my-mac"></a>Dov'è il mio Mac?

_Istruzioni per la configurazione di Mac per Visual Studio per compilare progetti Xamarin_

La versione corrente **stabile** di Xamarin per Visual Studio interagisce con Mac in modo diverso rispetto alle versioni precedenti[^](#earlier-versions).

Per usare Mac come Xamarin Mac Agent, è necessario abilitare l'opzione **Remote Login** (Accesso remoto) nel Mac.

1. Aprire *Spotlight* (`Cmd-Space`), cercare **Remote Login** (Accesso remoto) e selezionare il risultato **Sharing** (Condivisione). Verrà aperta la finestra **System Preferences** (Preferenze di sistema) nel pannello **Sharing** (Condivisione).

  ![](visual-studio-ssh-images/spotlight.png "Ricerca Spotlight per l'accesso remoto")

2. Selezionare l'opzione **Remote login** (Accesso remoto) nell'elenco **Service** (Servizio) sulla sinistra per consentire a Xamarin per Visual Studio di connettersi a Mac.

  ![](visual-studio-ssh-images/sharing.png "Selezionare l'opzione di accesso remoto nell'elenco dei servizi")

3. Assicurarsi che l'opzione **Remote Login** (Accesso remoto) sia impostata su **All users** (Tutti gli utenti), oppure che il nome utente o il gruppo Mac sia incluso nell'elenco di utenti consentiti sulla destra.

A questo punto il Mac dovrebbe essere individuabile da Visual Studio se si trova nella stessa rete.
Quando Visual Studio tenta di connettersi al Mac, verrà chiesto di eseguire l'accesso da Visual Studio con le credenziali utente Mac.

## <a name="where-can-i-find-more-information"></a>Altre informazioni

Le diverse guide disponibili per configurare e comprendere il nuovo agente sono elencate di seguito:

- [Connecting to your Mac](~/ios/get-started/installation/windows/connecting-to-mac/index.md) (Connessione a Mac)
- [Risoluzione dei problemi](~/ios/get-started/installation/windows/connecting-to-mac/troubleshooting.md)
- [Xamarin University - Xamarin Mac Agent](https://university.xamarin.com/lightninglectures/xamarin-mac-agent)

<a name="earlier-versions" />

## <a name="migrating-from-previous-versions"></a>Migrazione dalle versioni precedenti

Se sono state usate versioni precedenti di Xamarin per Visual Studio, si avrà familiarità con l'applicazione **Build Host di Xamarin.iOS** che era necessario avviare nel Mac e quindi *associare* tramite un codice PIN all'istanza di Visual Studio.

Tale applicazione non è più necessaria con la versione attuale di Xamarin per Visual Studio.
