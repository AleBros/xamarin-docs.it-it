---
title: Introduzione all'integrazione continua con Xamarin
description: Questo documento include collegamenti a guide che descrivono l'integrazione continua con Xamarin. Il contenuto collegato offre una panoramica dell'integrazione continua e illustra App Center Build, TeamCity e Jenkins.
ms.prod: xamarin
ms.assetid: 99484E96-DC69-4697-8BBB-1B44C5CBB5ED
author: davidortinau
ms.author: daortin
ms.date: 10/23/2018
ms.openlocfilehash: c59667213f7ecbe5603e638c4b19d54ffdf6dd01
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029820"
---
# <a name="continuous-integration-with-xamarin"></a>Integrazione continua con Xamarin

> [!Video https://youtube.com/embed/wXgnh2Q7Uv8]

## <a name="introduction-to-continuous-integrationtoolsciintro-to-cimd"></a>[Introduzione all'integrazione continua](~/tools/ci/intro-to-ci.md)

In questa sezione vengono illustrati i diversi componenti interessati dall'integrazione continua e dalle relative relazioni. Vengono descritti gli ambienti di integrazione continua descritti nelle sezioni specifiche riportate di seguito.

## <a name="devops-with-xamarintoolscidevopsmd"></a>[DevOps con Xamarin](~/tools/ci/devops.md)

Questa sezione identifica le funzionalità DevOps di Azure e Visual Studio che possono essere usate correttamente con un progetto Xamarin.

## <a name="working-with-continuous-integration-environments"></a>Uso di ambienti di integrazione continua

### <a name="build-xamarin-apps-with-azure-pipelineshttpsdocsmicrosoftcomazuredevopspipelineslanguagesxamarin"></a>[Crea app Xamarin con Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/languages/xamarin/)

Usare Azure Pipelines per compilare automaticamente app Xamarin per Android e iOS.

### <a name="build-xamarin-apps-using-app-centerhttpsdocsmicrosoftcomappcenterbuildxamarin"></a>[Crea app Xamarin usando App Center](https://docs.microsoft.com/appcenter/build/xamarin/)

Crea soluzioni Xamarin.iOS e Xamarin.Android con App Center, direttamente da GitHub, Azure DevOps o bitbucket.

### <a name="build-xamarin-apps-with-teamcitytoolsciteamcitymd"></a>[Crea app Xamarin con TeamCity](~/tools/ci/teamcity.md)

Questa guida illustra i passaggi necessari per l'uso di TeamCity per compilare app per dispositivi mobili e quindi inviarli a App Center test.

### <a name="build-xamarin-apps-with-jenkinstoolscijenkins-walkthroughmd"></a>[Crea app Xamarin con Jenkins](~/tools/ci/jenkins-walkthrough.md)

Questa guida illustra come configurare Jenkins come server di integrazione continua e automatizzare la compilazione di app per dispositivi mobili create con Xamarin. Viene descritto come installare Jenkins in OS X, configurarlo e configurare i processi per compilare le app Xamarin.iOS e Xamarin.Android quando viene eseguito il commit delle modifiche nel sistema di controllo della versione.
