---
title: 'fastlane per iOS: cert'
ms.topic: article
ms.prod: xamarin
ms.assetid: 900FA6FF-F3C9-4D35-993E-B0D88E6B1883
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/19/2017
ms.openlocfilehash: a37b0dc1418fd9dda5c59723a96c20855c1c3e1b
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="fastlane-for-ios--cert"></a>fastlane per iOS: cert

> [!IMPORTANT]
> fastlane consiglia di usare lo strumento [`match`](~/ios/deploy-test/provisioning/fastlane/match.md) per generare e gestire i certificati. Usare `cert` direttamente solo se si vuole il controllo completo e si hanno conoscenze sufficienti sulla firma del codice. Usare questa azione per scaricare l'ultima identità di firma del codice.

## <a name="overview"></a>Panoramica

Il provisioning di dispositivi viene tradizionalmente effettuato da ogni membro di un team di sviluppo tramite Xcode o in Apple Developer Portal. La procedura prevede diversi passaggi:

- Richiesta di un certificato di sviluppo
- Aggiunta di un dispositivo al portale
- Creazione di un ID app
- Creazione di un profilo di provisioning
- Download di profili e certificati

Ognuno di questi passaggi contiene variabili che devono essere risolte, a seconda del tipo di applicazione che si sta sviluppando. Altre informazioni sui passaggi necessari per configurare un dispositivo per lo sviluppo manualmente o tramite Xcode sono disponibili nella guida [Provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md).

Questa guida presenta gli strumenti fastlane come alternativa all'uso di Xcode ed espone gli argomenti seguenti:

- [Informazioni su cert](#whatiscert)
- [Uso di cert](#using)
- [Opzioni aggiuntive](#options)

## <a name="installation"></a>Installazione

Per informazioni sull'installazione e sull'aggiornamento di fastlane, vedere l'introduzione alla guida di [fastlane](~/ios/deploy-test/provisioning/fastlane/index.md#Installation).

<a name="whatiscert" />

## <a name="what-is-cert"></a>Informazioni su cert

cert offre un'interfaccia di terminale per la creazione di nuove identità di firma del codice (spesso chiamate _certificato_ dello sviluppatore) per gli ambienti di sviluppo e distribuzione.

<a name="using" />

## <a name="using-cert"></a>Uso di cert

Per usare l'utilità cert, immettere il comando seguente nell'interfaccia della riga di comando del terminale:

    fastlane cert

Per impostazione predefinita, verrà creato un certificato di distribuzione. Per creare un certificato di sviluppo, passare il flag `--development`:

    fastlane cert --development

Poiché cert chiederà l'ID e la password Apple, immetterli ora:

[![](cert-images/fastlane-image1.png "cert chiederà l'ID e la password Apple")](cert-images/fastlane-image1.png#lightbox)

> [!IMPORTANT]
> La prima volta che viene immessa, la password viene salvata nel keychain di macOS locale. In alternativa, si possono usare variabili di ambiente per archiviare il nome utente e la password oppure è possibile usare `export fastlane_DONT_STORE_PASSWORD=1` se non si vuole archiviare la password nel keychain. Per altre informazioni sulla gestione delle credenziali con fastlane, vedere la [guida a CredentialsManager](https://github.com/fastlane/fastlane/blob/master/credentials_manager/README.md) di fastlane.

L'ID Apple può anche essere passato come argomento usando il comando seguente:

    fastlane cert -u myemailadress@domain.com

Se l'ID Apple è connesso a più team, questi verranno visualizzati qui. Selezionare il numero corrispondente al team che si vuole usare:

[![](cert-images/fastlane-image2.png "Selezionare il team che si vuole usare")](cert-images/fastlane-image2.png#lightbox)

È possibile passare l'ID team anche usando il flag seguente:

    fastlane cert -l 2TU993NY9J

fastlane controllerà se uno dei certificati di firma disponibili è installato nel computer locale e, se presente, lo userà.

Se non sono disponibili certificati di firma, cert:

- Creerà una nuova chiave privata e una nuova richiesta di firma
- Genererà, scaricherà e installerà il certificato
- Importerà il certificato e la chiave privata nel keychain

Quando verrà raggiunto il numero massimo di identità di firma consentite per l'account, verrà generata un'eccezione. Per creare una nuova identità di firma, è necessario revocare manualmente uno dei certificati esistenti tramite il centro sviluppatori e riprovare.

> [!NOTE]
> fastlane non riesce a scaricare le identità di firma esistenti dal centro sviluppatori se non sono già nel keychain, perché la chiave privata esiste sempre e solo nel computer o nella versione esportata (*.p12) del certificato, ma mai nel centro sviluppatori.

<a name="options" />

## <a name="additional-options"></a>Opzioni aggiuntive

Per un supporto aggiuntivo durante l'uso di cert, è possibile usare le opzioni seguenti:

- Usare il flag `-–help` per un elenco di tutti i comandi disponibili:

        fastlane cert --help

- Usare il flag `-–verbose` per aumentare il livello di dettaglio dell'output

        fastlane cert --development --verbose


## <a name="related-links"></a>Collegamenti correlati

- [fastlane: cert](https://github.com/fastlane/fastlane/blob/master/cert/README.md)
