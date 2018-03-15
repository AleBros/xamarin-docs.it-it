---
title: 'fastlane per iOS: match'
ms.topic: article
ms.prod: xamarin
ms.assetid: C4A2A67E-0643-4CED-B1A9-79D65054F3CA
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: d92f820e22277148b4de3ff87e3fdaca0f573f52
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="fastlane-for-ios---match"></a>fastlane per iOS: match

Il provisioning di dispositivi è stato tradizionalmente effettuato da ogni membro di un team di sviluppo tramite Xcode o in Apple Developer Portal. La procedura è costituita da diversi passaggi:

- Richiesta di un certificato di sviluppo
- Aggiunta di un dispositivo al portale
- Creazione di un ID app
- Creazione di un profilo di provisioning
- Download di profili e certificati

Altre informazioni sui passaggi necessari per configurare un dispositivo per lo sviluppo manualmente o tramite Xcode sono disponibili nella guida [Provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md).

Questa guida descrive come usare gli strumenti fastlane come alternativa all'uso di Xcode.

## <a name="installation"></a>Installazione

Per informazioni sull'installazione di fastlane, vedere l'introduzione alla guida di [fastlane](~/ios/deploy-test/provisioning/fastlane/index.md#Installation).

<a name="whatismatch" />

## <a name="what-is-match"></a>Informazioni su match

match gestisce la creazione e la manutenzione dei certificati di firma del codice e dei profili di provisioning e consente a un team di sviluppo iOS di condividere un'identità di firma del codice tra tutti gli sviluppatori.

Quando si distribuisce un'app nell'App Store, si esegue un test della versione beta o si installa l'app in un dispositivo, ogni membro di un team di sviluppo ha la propria identità di firma. Ne possono derivare conflitti di identità e di profili e la necessità di creare, ruotare e gestire manualmente i profili e gli ID delle app.

match invece crea e gestisce tutti i certificati e i profili automaticamente e li archivia in un repository git privato. Tutti gli sviluppatori di un team possono così accedere a queste credenziali e usarle, il che offre sicurezza aggiuntiva per i certificati: oltre a essere in un repository git privato, vengono anche crittografati con una [passphrase](#passphrase). L'archiviazione degli elementi di firma del codice in un repository consente agli agenti e agli amministratori del team di aggiornare e ruotare i certificati in base alle esigenze e in questo modo si impiega meno tempo per distribuire i nuovi certificati a ogni sviluppatore.

> [!IMPORTANT]
> match attualmente non supporta i profili aziendali interni.

<a name="initializing" />

## <a name="initializing-your-project-with-match"></a>Inizializzazione del progetto con match

Se l'amministratore del team crea un repository git privato, tramite github.com o bitbucket.com, deve assicurarsi di aggiungere tutti i membri del team come collaboratori al repository.

Usando il terminale, sostituire la directory con la directory del progetto ed eseguire:

    fastlane match init

Quando richiesto, immettere l'URL del repository git:

 [![](match-images/fastlane-image7.png "Immettere l'URL del repository git")](match-images/fastlane-image7.png#lightbox)

Per trovare e copiare l'URL, fare clic sul pulsante **Clone or Download** (Clona o scarica) in github.com, come illustrato sotto:

[![](match-images/fastlane-image6.png "URL sotto il pulsante Clone or Download (Clona o scarica) in github.com")](match-images/fastlane-image6.png#lightbox)

Con l'inizializzazione del progetto viene creato un matchfile, un file di testo che può essere modificato per passare le variabili di ambiente allo strumento match. Di seguito è illustrato un esempio di matchfile:

[![](match-images/fastlane-image8.png "Esempio di matchfile")](match-images/fastlane-image8.png#lightbox)

<a name="running" />

## <a name="running-match"></a>Esecuzione di match

> [!NOTE]
> Prima di eseguire match per la prima volta, fastlane consiglia di provare a cancellare i profili e i certificati esistenti usando il [comando match nuke](#using).

A seconda dell'ambiente necessario, è possibile usare uno dei comandi seguenti per creare un nuovo certificato e profilo di provisioning e archiviarlo nel nuovo repository git:

    fastlane match appstore

    fastlane match adhoc

    fastlane match development

Oltre a creare nuovi certificati e profili, usando uno di questi comandi verranno aggiunti (o aggiornati, se esistono già) gli elementi seguenti al repository git:

- Cartella Certificates
- Cartella Profiles
- Un file leggimi con le istruzioni di base
- Una versione di match

[![](match-images/fastlane-image9.png "Struttura del progetto nel repository git")](match-images/fastlane-image9.png#lightbox)

I profili di provisioning vengono installati in `~/Library/MobileDevice/Provisioning Profiles`. I certificati e le chiavi private vengono installati direttamente nel keychain.

<a name="using" />

### <a name="using-the-nuke-command"></a>Uso del comando `nuke`

Se sono presenti certificati non ordinati, è possibile usare `nuke` per revocare i certificati e i profili per ogni ambiente usando i comandi seguenti:

    fastlane match nuke

Per revocare tutti i certificati e i profili di provisioning per un ambiente specifico:

    fastlane match nuke development

 oppure

    fastlane match nuke distribution

fastlane confermerà i file che verranno rimossi prima di eseguire un'eliminazione.

<a name="passphrase" />

### <a name="passphrase"></a>Passphrase

Quando si esegue `match` per la prima volta, verrà chiesto di impostare una passphrase per il repository git. Prendere nota della password, perché sarà necessaria quando si eseguirà match in un computer diverso. Questo è un livello di sicurezza aggiuntivo: ogni file verrà crittografato usando OpenSSL. A ogni successiva esecuzione di `match` in un nuovo computer, verrà chiesta questa passphrase. Dopo averla immessa per la prima volta, la passphrase verrà aggiunta al keychain locale.

Per impostare la passphrase per decrittografare i profili tramite una variabile di ambiente, usare `MATCH_PASSWORD`.

<a name="options" />

## <a name="additional-options"></a>Opzioni aggiuntive

Per un supporto aggiuntivo durante l'uso di match, è possibile usare le opzioni seguenti:

- Usare il flag `-–help` per un elenco di tutti i comandi disponibili:

        fastlane match cert --help

- Usare il flag `-–verbose` per aumentare il livello di dettaglio dell'output:

        fastlane match --development --verbose

- Usare il flag `--force_for_new_devices` per forzare il rinnovo dei profili di provisioning, se il conteggio dei dispositivi nel portale per sviluppatori è cambiato:

        fastlane match development --force_for_new_devices

## <a name="related-links"></a>Collegamenti correlati

- [fastlane - match](https://github.com/fastlane/fastlane/blob/master/match/README.md)
