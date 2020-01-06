---
title: Creazione di bundle per Mac App Store
description: Questo documento descrive come creare un bundle di un'app Xamarin.Mac per la pubblicazione in Mac App Store. Vengono illustrate le opzioni di firma del codice e la compilazione.
ms.prod: xamarin
ms.assetid: 00a36d7c-937d-4657-bf6a-0de9684b8f94
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 7d2cd650dd22cd64d506d5d17a5ae0c34b44fc2b
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487202"
---
# <a name="bundling-for-the-mac-app-store"></a>Creazione di bundle per Mac App Store

In questa sezione vengono descritti i concetti di base per compilare con Visual Studio per Mac un'applicazione da rilasciare in Mac App Store. In base alle funzionalità aggiuntive, come ad esempio l'accesso iCloud e le notifiche push, potrebbe essere necessaria una configurazione aggiuntiva, che oltrepassa l'ambito di questo articolo.

> [!NOTE]
> Prima di iniziare questa sezione, lo sviluppatore deve aver creato un profilo di provisioning di produzione per la compilazione per Mac App Store. Vedere le [istruzioni sul profilo](profiles.md) per la creazione dei profili di provisioning richiesti.

## <a name="code-signing-options"></a>Opzioni di firma del codice

Modifica il valore di **Configurazione** su **Release** (Rilascio) prima di aggiornare le opzioni di creazione di pacchetti e firma del codice. Lo sviluppatore deve assicurarsi di usare l'**Identità** dell'azienda e il profilo di provisioning creati in precedenza quando ha firmato l'applicazione per il rilascio in App Store.

[![modificare le opzioni di firma del codice](bundling-images/sign.png)](bundling-images/sign-large.png#lightbox)

Verificare che l'opzione per creare un pacchetto di installazione sia stata selezionata nelle impostazioni **Compilazione Mac**:

[![Modifica delle opzioni di compilazione](bundling-images/build.png "Modifica delle opzioni di compilazione")](bundling-images/build-large.png#lightbox)

## <a name="build"></a>Compila

Prima della compilazione, assicurarsi che sia stata selezionata la configurazione **Release** (Rilascio). Quando lo sviluppatore compila l'app, verrà richiesto _due volte_ (per usare i certificati dell'applicazione e del programma di installazione):

![Per consentire all'app di usare il certificato, verrà visualizzato due volte](bundling-images/perms02.png)

Dopo che l'applicazione è stata compilata, lo sviluppatore può fare clic con il pulsante destro del mouse sul progetto e scegliere Visualizza **in Finder** per trovare il file del pacchetto (nella directory `bin/Release/AppStore` nell'esempio riportato di seguito).  Il file del pacchetto include un programma di installazione per l'app che può essere inviata a Apple per l'inclusione nel Mac App Store.

> [!div class="mx-imgBorder"]
> ![selezione del pacchetto di compilazione in Finder](bundling-images/path.png)

## <a name="related-links"></a>Collegamenti correlati

- [Installazione](/visualstudio/mac/installation/)
- [Esempio Hello, Mac](~/mac/get-started/hello-mac.md)
- [Distribuire le app in Mac App Store](https://developer.apple.com/devcenter/mac/checklist/)
- [ID sviluppatore e GateKeeper](https://developer.apple.com/resources/developer-id/)
