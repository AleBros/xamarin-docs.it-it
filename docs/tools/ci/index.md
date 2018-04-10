---
title: Introduzione all'integrazione continua con Xamarin
description: Integrazione continua è una pratica di progettazione del software in cui viene compilata una compilazione automatica e facoltativamente, verifica di un'app quando il codice viene aggiunto o modificato dagli sviluppatori nel repository di controllo della versione del progetto. In questo articolo verrà illustrati i concetti generali relativi a integrazione continua e alcune delle opzioni disponibili per l'integrazione continua con i progetti di Xamarin.
ms.prod: xamarin
ms.assetid: 99484E96-DC69-4697-8BBB-1B44C5CBB5ED
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 05/04/2017
ms.openlocfilehash: b5bccfa38a9f382789585284765183efa42b6a3d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="introduction-to-continuous-integration-with-xamarin"></a>Introduzione all'integrazione continua con Xamarin

_Integrazione continua è una pratica di progettazione del software in cui viene compilata una compilazione automatica e facoltativamente, verifica di un'app quando il codice viene aggiunto o modificato dagli sviluppatori nel repository di controllo della versione del progetto. In questo articolo verrà illustrati i concetti generali relativi a integrazione continua e alcune delle opzioni disponibili per l'integrazione continua con i progetti di Xamarin._

> [!Video https://youtube.com/embed/wXgnh2Q7Uv8]


##  <a name="introduction-to-continuous-integrationtoolsciintro-to-cimd"></a>[Introduzione all'integrazione continua](~/tools/ci/intro-to-ci.md)

Questa sezione descrive i diversi componenti di integrazione continua e le relative relazioni. Vengono descritti gli ambienti di integrazione continua che sono descritti nelle sezioni specifiche seguenti.

[!include[](~/tools/ci/includes/firewall-information.md)]

## <a name="working-with-continuous-integration-environments"></a>Utilizzo di ambienti di integrazione continua


### <a name="using-app-center-build-with-xamarinappcenterbuildxamarin"></a>[Uso di App Center Build con Xamarin](/appcenter/build/xamarin/)

Compilare soluzioni xamarin. IOS e xamarin centro App direttamente da Bitbucket, GitHub o Visual Studio Team Services.

### <a name="using-teamcity-with-xamarintoolsciteamcitymd"></a>[Uso di TeamCity con Xamarin](~/tools/ci/teamcity.md)

Questa guida vengono illustrati i passaggi relativi all'utilizzo TeamCity per compilare App per dispositivi mobili e quindi inviarli a Xamarin Test Cloud.

###  <a name="using-jenkins-with-xamarintoolscijenkins-walkthroughmd"></a>[Uso di Jenkins con Xamarin](~/tools/ci/jenkins-walkthrough.md)

Questa guida viene illustrato come impostare Jenkins come server di integrazione continua e automatizzare la compilazione di App per dispositivi mobili create con Xamarin. Viene descritto come installare Jenkins su OS X, configurarlo e configurare i processi di compilazione xamarin. IOS e xamarin App quando le modifiche vengono applicate al sistema di controllo della versione.
