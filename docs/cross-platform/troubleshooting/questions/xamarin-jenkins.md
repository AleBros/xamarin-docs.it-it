---
title: Perché Jenkins non è supportato da Microsoft?
description: Questo documento illustra, a livello generale, l'interazione di Xamarin con il sistema di integrazione Continuata Jenkins. Illustra anche alcuni problemi da affrontare quando si lavora con Jenkins.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9951F980-2C6C-47C0-8A35-A78F06C20BEB
author: asb3993
ms.author: amburns
ms.date: 06/05/2018
ms.openlocfilehash: c2e409b796d5ef2525079e02aafdd0c6e8db5d81
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61158936"
---
# <a name="why-isnt-jenkins-supported-by-microsoft"></a>Perché Jenkins non è supportato da Microsoft?

## <a name="jenkins-support-explanation"></a>Spiegazione di supporto di Jenkins

Jenkins è una suite di integrazione continua open source; a causa di questo molti problemi sono causati direttamente dagli Jenkins *sé* dovrà essere presentata come problemi di in cui è stato ottenuto il codice, ad esempio il [repository Jenkins principale](https://github.com/jenkinsci/jenkins), o il repository per [ Jenkins.app](https://github.com/stisti/jenkins-app).

L'eccezione è per i problemi che possono essere isolati in particolare dei bug in strumenti di Xamarin. Se si ritiene che questo è il caso è possibile controllare la [opzioni di supporto](~/cross-platform/troubleshooting/support-options.md), se anche in questo caso, il problema potrebbe essere un valore di fuori di quali il supporto di Xamarin può team *direttamente* assistenza.

## <a name="setup-jenkins-with-xamarin"></a>Il programma di installazione di Jenkins con Xamarin

Anche se come indicato in precedenza direttamente dal nostro team; non sono supportati i problemi di Jenkins il [uso di Jenkins con Xamarin](~/tools/ci/jenkins-walkthrough.md) Guida può essere utilizzata per configurare un server di integrazione Continuata Jenkins è integrato con Xamarin. 

## <a name="fixes-for-common-issues"></a>Correzioni per problemi comuni

### <a name="jenkins-is-unable-to-find-the-android-sdk"></a>Jenkins è in grado di trovare Android SDK

Il messaggio di errore per risolvere questo problema è simile al seguente:

> Errore XA5205: La Directory Android SDK non trovata. Impostare tramite /p:AndroidSdkDirectory

Le opzioni per l'impostazione di percorso del SDK possono variare a seconda del plug-in Jenkins Android esatta in uso; un buon punto di ricerca per informazioni su come impostare questo valore è nella Guida di plug-in. Ad esempio; il [plug-in dell'emulatore Android](https://wiki.jenkins-ci.org/display/JENKINS/Android+Emulator+Plugin#AndroidEmulatorPlugin-Systemconfiguration) cerca automaticamente il SDK, ma se non la trova; il percorso può essere impostato anche tramite la pagina di configurazione di sistema di Jenkins per tale plug-in. 


## <a name="deprecated-errors"></a>Errori deprecati

> [!IMPORTANT]
> Questo problema è stato risolto nelle versioni recenti di Xamarin. Tuttavia, se il problema si verifica la versione più recente del software, inviare un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) il completo controllo delle versioni delle informazioni e full output del log di compilazione.



### <a name="jenkins-reports-an-invalid-xamarin-license"></a>Jenkins segnala una licenza di Xamarin non valida
I messaggi di errore per risolvere questo problema sono in genere simile a

> Errore XA9008: Compilazione dalla riga di comando richiede una licenza business

oppure

> Errore: L'edizione xamarin Starter. di IOS non supporta la compilazione di fuori di Xamarin Studio 

La causa più comune di questo scenario è l'uso di Jenkins eseguendo l'accesso con un account utente non associato con la licenza di Xamarin. Il modo più semplice per risolvere, consiste nell'installare Jenkins come app direttamente tramite l'account utente. Questo processo e alcune considerazioni aggiuntive sono descritti di seguito: [https://forums.xamarin.com/discussion/comment/99397/#Comment_99397](https://forums.xamarin.com/discussion/comment/99397/#Comment_99397)
