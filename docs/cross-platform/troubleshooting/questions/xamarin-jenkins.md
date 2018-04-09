---
title: Motivo per cui non è supportato Jenkins da Xamarin?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9951F980-2C6C-47C0-8A35-A78F06C20BEB
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: befcdcbee3114e760cec94a61a84106fddc72cf9
ms.sourcegitcommit: 271d3f7ea4abfcf87734d2c747a68cb8114d743c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2018
---
# <a name="why-isnt-jenkins-supported-by-xamarin"></a>Motivo per cui non è supportato Jenkins da Xamarin?

## <a name="jenkins-support-explanation"></a>Supporto di Jenkins spiegazione

Jenkins è una suite di elemento di configurazione open source; a causa di problemi di questo numero direttamente causati da di Jenkins *stesso* dovrà essere archiviato come problemi di base in cui è stato ottenuto il codice; ad esempio il [repository principale di Jenkins](https://github.com/jenkinsci/jenkins), o il repository per [ Jenkins.app](https://github.com/stisti/jenkins-app).

L'eccezione è per i problemi che possono essere isolati in particolare bug in strumenti di Xamarin. Se si ritiene che il problema per il caso in cui è possibile controllare il [opzioni di supporto](~/cross-platform/troubleshooting/support-options.md), anche se nuovamente, il problema potrebbe essere un valore di fuori di quali il supporto Xamarin team può *direttamente* utili.

## <a name="setup-jenkins-with-xamarin"></a>Il programma di installazione Jenkins con Xamarin

Mentre come indicato in precedenza Jenkins problemi non sono supportati direttamente dal nostro team; il [utilizzando Jenkins con Xamarin](~/tools/ci/jenkins-walkthrough.md) Guida può essere utilizzata per configurare un server di integrazione Continuata Jenkins è integrato con Xamarin. 

## <a name="fixes-for-common-issues"></a>Consente di risolvere i problemi comuni
### <a name="jenkins-is-unable-to-find-the-android-sdk"></a>Jenkins è in grado di individuare Android SDK

Il messaggio di errore per questo problema è simile al seguente:

> Errore XA5205: la Directory SDK Android non è stata trovata. Impostare tramite /p:AndroidSdkDirectory

Le opzioni per l'impostazione della posizione di SDK possono variare a seconda del plug-in Jenkins Android esatta in uso; si consiglia di cercare come impostare questa proprietà è nella Guida di plug-in. Ad esempio; il [plug-in dell'emulatore di Android](https://wiki.jenkins-ci.org/display/JENKINS/Android+Emulator+Plugin#AndroidEmulatorPlugin-Systemconfiguration) cerca automaticamente il SDK, ma se non viene trovata; il percorso può essere impostato anche tramite la pagina di configurazione del sistema Jenkins per tale plug-in. 


## <a name="deprecated-errors"></a>Errori obsoleti

> [!IMPORTANT]
> Questo problema è stato risolto nelle versioni recenti di Xamarin. Tuttavia, se il problema si verifica la versione più recente del software, inviare un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) il completo controllo delle versioni delle informazioni e full output del log di compilazione.



### <a name="jenkins-reports-an-invalid-xamarin-license"></a>Jenkins segnala una licenza di Xamarin non valida
I messaggi di errore per questo problema sono in genere simile al seguente

> Errore XA9008: compilazione dalla riga di comando richiede una licenza di business

oppure

> Errore: La Starter Edition di xamarin non supporta la compilazione all'esterno di Xamarin Studio 

La causa più comune di questo scenario è l'utilizzo di Jenkins eseguendo l'accesso con un account utente non associato con la licenza di Xamarin. Il modo più semplice della risoluzione, è di installare Jenkins come un'app direttamente tramite l'account utente. Questo processo e alcune considerazioni aggiuntive sono descritti di seguito: [https://forums.xamarin.com/discussion/comment/99397/#Comment_99397](https://forums.xamarin.com/discussion/comment/99397/#Comment_99397)
