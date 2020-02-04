---
title: Firma delle app Xamarin.Mac con un ID sviluppatore
description: Questo documento descrive come firmare un'app Xamarin.Mac con un ID sviluppatore in modo da poterla distribuire al di fuori di Mac App Store. Vengono illustrate le opzioni di firma del codice e la compilazione.
ms.prod: xamarin
ms.assetid: cf7b733b-e08f-4f56-a233-264b29ee4c97
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: f05e763df58e6fafc4d4ed1d4007979859670c28
ms.sourcegitcommit: db422e33438f1b5c55852e6942c3d1d75dc025c4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76725462"
---
# <a name="signing-xamarinmac-apps-with-a-developer-id"></a>Firma delle app Xamarin.Mac con un ID sviluppatore

Se lo sviluppatore prevede di distribuire un'app direttamente agli utenti di macOS, Apple consiglia di firmarne il codice con l'ID sviluppatore in modo che possa essere installata nei sistemi macOS con **GateKeeper** abilitato. Se l'app non è stata firmata, **GateKeeper** impedirà agli utenti di installarla visualizzando un messaggio di avviso. Gli utenti possono ignorare questa limitazione tenendo premuto CTRL durante l'avvio.

Per altre informazioni, vedere [ID sviluppatore e GateKeeper](https://developer.apple.com/developer-id/) e [Distributing Outside the Mac App Store](https://developer.apple.com/library/content/documentation/IDEs/Conceptual/AppDistributionGuide/Introduction/Introduction.html) (Distribuzione al di fuori di Mac App Store) nel sito Web Apple.

## <a name="code-signing-options"></a>Opzioni di firma del codice

Per compilare un'app per la distribuzione diretta agli utenti, non tramite Mac App Store, impostare le **impostazioni di firma** per usare l'**ID sviluppatore**. Assicurarsi di modificare la configurazione **Release** (Rilascio).

 [![](signing-images/config02.png "The Mac Signing options")](signing-images/config02.png#lightbox)

## <a name="build"></a>Compilazione

Prima della compilazione, assicurarsi di aver selezionato la configurazione corretta e la creazione di un pacchetto di installazione nelle impostazioni **Compilazione Mac**:

[![](signing-images/config03.png "The build options")](signing-images/config03.png#lightbox)

Quando compila l'app, lo sviluppatore dovrà usare entrambi i certificati:

 [![](signing-images/image57.png "Allowing keychain access")](signing-images/image57.png#lightbox)

 [![](signing-images/image58.png "Allowing keychain access")](signing-images/image58.png#lightbox)

Dopo aver compilato l'applicazione, lo sviluppatore può fare clic con il pulsante destro del mouse sul progetto e scegliere **Apri cartella superiore** per trovare il file del pacchetto nella directory `bin/Release`. Il file include un programma di installazione per l'applicazione, in modo che possa essere distribuita a tutti gli utenti macOS per l'installazione.

 [![](signing-images/image59.png "Selecting the app package in Finder")](signing-images/image59.png#lightbox)

## <a name="related-links"></a>Collegamenti correlati

- [Installazione](~//mac/get-started/installation.md)
- [Esempio Hello, Mac](~//mac/get-started/hello-mac.md)
- [Distribuire le app in Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [Guida agli strumenti: Firma del codice dell'app](https://developer.apple.com/library/mac/#documentation/ToolsLanguages/Conceptual/OSXWorkflowGuide/CodeSigning/CodeSigning.html)
- [ID sviluppatore e GateKeeper](https://developer.apple.com/developer-id/)
