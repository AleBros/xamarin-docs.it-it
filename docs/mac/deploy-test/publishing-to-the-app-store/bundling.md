---
title: Creazione di bundle per Mac App Store
description: Questo documento descrive come creare un bundle di un'app Xamarin.Mac per la pubblicazione in Mac App Store. Vengono illustrate le opzioni di firma del codice e la compilazione.
ms.prod: xamarin
ms.assetid: 00a36d7c-937d-4657-bf6a-0de9684b8f94
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: f4d38bb66a34257c1e0a27c5fbbfe16f59743e83
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "76725506"
---
# <a name="bundling-for-the-mac-app-store"></a>Creazione di bundle per Mac App Store

In questa sezione vengono descritti i concetti di base per compilare con Visual Studio per Mac un'applicazione da rilasciare in Mac App Store. In base alle funzionalità aggiuntive, come ad esempio l'accesso iCloud e le notifiche push, potrebbe essere necessaria una configurazione aggiuntiva, che oltrepassa l'ambito di questo articolo.

> [!NOTE]
> Prima di iniziare questa sezione, lo sviluppatore deve aver creato un profilo di provisioning di produzione per la compilazione per Mac App Store. Per la creazione dei profili di provisioning richiesti, vedere le [istruzioni per i profili](profiles.md).

## <a name="code-signing-options"></a>Opzioni di firma del codice

Modifica il valore di **Configurazione** su **Release** (Rilascio) prima di aggiornare le opzioni di creazione di pacchetti e firma del codice. Lo sviluppatore deve assicurarsi di usare l'**Identità** dell'azienda e il profilo di provisioning creati in precedenza quando ha firmato l'applicazione per il rilascio in App Store.

[![Modifica delle opzioni di firma del codice](bundling-images/sign.png)](bundling-images/sign-large.png#lightbox)

Verificare che l'opzione per creare un pacchetto di installazione sia stata selezionata nelle impostazioni **Compilazione Mac**:

[![Modifica delle opzioni di compilazione](bundling-images/build.png "Modifica delle opzioni di compilazione")](bundling-images/build-large.png#lightbox)

## <a name="build"></a>Compilare

Prima della compilazione, assicurarsi che sia stata selezionata la configurazione **Release** (Rilascio). Quando lo sviluppatore compila l'app, verranno visualizzate _due_ richieste di conferma (per usare sia il certificato dell'applicazione che il certificato del programma di installazione):

![Richiesta visualizzata due volte per consentire all'app di usare il certificato](bundling-images/perms02.png)

Dopo aver compilato l'applicazione, lo sviluppatore può fare clic con il pulsante destro del mouse sul progetto e scegliere **Mostra nel Finder** per trovare il file del pacchetto. Nell'esempio illustrato sotto si tratta della directory `bin/Release/AppStore`.  Il file del pacchetto include un programma di installazione per l'app che può essere inviata a Apple per l'inclusione nel Mac App Store.

> [!div class="mx-imgBorder"]
> ![Selezione del pacchetto di compilazione nel Finder](bundling-images/path.png)

## <a name="related-links"></a>Collegamenti correlati

- [Installazione](/visualstudio/mac/installation/)
- [Esempio Hello, Mac](~/mac/get-started/hello-mac.md)
- [Distribuire le app in Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [ID sviluppatore e GateKeeper](https://developer.apple.com/developer-id/)
