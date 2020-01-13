---
title: Perché Jenkins non è supportato da Microsoft?
description: Questo documento descrive, a un livello elevato, l'interazione di Novell con il sistema CI di Jenkins. Vengono inoltre illustrati alcuni problemi comuni che si verificano quando si lavora con Jenkins.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9951F980-2C6C-47C0-8A35-A78F06C20BEB
author: davidortinau
ms.author: daortin
ms.date: 06/05/2018
ms.openlocfilehash: 7be60eaa1135284522ef1e6ce81c911a68ff9915
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73012219"
---
# <a name="why-isnt-jenkins-supported-by-microsoft"></a>Perché Jenkins non è supportato da Microsoft?

## <a name="jenkins-support-explanation"></a>Spiegazione del supporto per Jenkins

Jenkins è una suite CI open source, a causa di questo problema, i problemi causati direttamente *da Jenkins dovranno* essere archiviati come problemi rispetto alla posizione in cui è stato ottenuto il codice; come il [repository Jenkins principale](https://github.com/jenkinsci/jenkins)o il repository per [Jenkins. app](https://github.com/stisti/jenkins-app).

L'eccezione è rappresentata da problemi che possono essere isolati con particolari bug negli strumenti di Novell; Se si ritiene che questo sia il caso in cui è possibile controllare le [Opzioni di supporto](~/cross-platform/troubleshooting/support-options.md), anche in questo caso, il problema potrebbe essere qualcosa di diverso da quello che il team di supporto di Novell può usare *direttamente* .

## <a name="setup-jenkins-with-xamarin"></a>Configurare Jenkins con Novell

Sebbene come indicato in precedenza, i problemi Jenkins non sono supportati direttamente dal team. la Guida [uso di Jenkins con Novell](~/tools/ci/jenkins-walkthrough.md) può essere usata per configurare un server ci Jenkins integrato con Novell. 

## <a name="fixes-for-common-issues"></a>Correzioni per problemi comuni

### <a name="jenkins-is-unable-to-find-the-android-sdk"></a>Jenkins non è in grado di trovare il Android SDK

Il messaggio di errore per questo problema è simile al seguente:

> errore XA5205: Impossibile trovare la directory Android SDK. Impostare tramite/p: AndroidSdkDirectory

Le opzioni per l'impostazione del percorso dell'SDK possono variare a seconda dell'esatto plug-in di Jenkins Android in uso; una posizione ideale per cercare come impostare questa impostazione è nella Guida ai plug-in. Ad esempio: il [plug](https://wiki.jenkins-ci.org/display/JENKINS/Android+Emulator+Plugin#AndroidEmulatorPlugin-Systemconfiguration) -in emulatore Android cerca automaticamente l'SDK, ma se non riesce a trovarlo; il percorso può essere impostato anche tramite la pagina di configurazione del sistema Jenkins per il plug-in. 

## <a name="deprecated-errors"></a>Errori deprecati

> [!IMPORTANT]
> Questo problema è stato risolto nelle versioni recenti di Novell. Tuttavia, se il problema si verifica nella versione più recente del software, inserire un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) con le informazioni complete sul controllo delle versioni e l'output del log di compilazione completo.

### <a name="jenkins-reports-an-invalid-xamarin-license"></a>Jenkins segnala una licenza Novell non valida
I messaggi di errore relativi a questo problema sono in genere simili a

> Errore XA9008: la compilazione dalla riga di comando richiede una licenza aziendale

Oppure

> Errore: la versione iniziale di Xamarin.iOS non supporta la compilazione all'esterno di Xamarin Studio 

La causa più comune di questo scenario è l'uso di Jenkins eseguendo l'accesso con un account utente non associato alla licenza Novell. Il modo più semplice per risolvere questo problema consiste nell'installare Jenkins come app direttamente tramite l'account utente. Questo processo e alcune considerazioni aggiuntive sono descritte qui: [https://forums.xamarin.com/discussion/comment/99397/#Comment_99397](https://forums.xamarin.com/discussion/comment/99397/#Comment_99397)
