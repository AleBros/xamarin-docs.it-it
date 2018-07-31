---
title: Anteprima della distribuzione di OpenJDK di Microsoft
description: Guida dettagliata alla configurazione della distribuzione di OpenJDK di Microsoft.
ms.prod: xamarin
ms.assetid: B5F8503D-F4D1-44CB-8B29-187D1E20C979
ms.technology: xamarin-android
author: vyedin
ms.author: vyedin
ms.date: 07/22/2018
ms.openlocfilehash: 6c1346918ca6881e551f6c5b89ab16ad13d3f804
ms.sourcegitcommit: b56b3f906d2c05a3f1be219ef41be8b79e519b8e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242514"
---
# <a name="microsofts-openjdk-distribution-preview"></a>Anteprima della distribuzione di OpenJDK di Microsoft

_Questa guida descrive la procedura per il passaggio alla versione di anteprima della distribuzione di OpenJDK di Microsoft._

![Funzionalità di anteprima](~/media/shared/preview.png)

## <a name="overview"></a>Panoramica

A partire da Visual Studio 15.9 e Visual Studio per Mac 7.7, Visual Studio Tools per Xamarin passerà da Oracle JDK a una versione leggera di OpenJDK destinata unicamente allo sviluppo per Android:

![Nuovo flusso di lavoro che offre un'anteprima Web di OpenJDK in Visual Studio 15.8 Preview 5](openjdk-images/openjdk.png)

I vantaggi di questo passaggio sono:

- Sarà sempre disponibile una versione di OpenJDK che funziona per lo sviluppo Android.

- Il download di JDK 9 o 10 non influisce sull'esperienza di sviluppo.

- Riduzione significativa delle dimensioni e del footprint del download.

- Nessun problema con i server e i programmi di installazione di terze parti.

Se si desidera passare prima all'esperienza migliorata, sono disponibili build della distribuzione di OpenJDK di Microsoft per il testing, sia per Windows che per Mac. Il processo di installazione è descritto di seguito ed è possibile tornare a Oracle JDK in qualsiasi momento.

## <a name="download"></a>Download

Per iniziare, scaricare la build corretta per il sistema in uso:

- **Mac** &ndash; https://dl.xamarin.com/OpenJDK/mac/microsoft-dist-openjdk-1.8.0.9.zip
- **Windows x86** &ndash; https://dl.xamarin.com/OpenJDK/win32/microsoft-dist-openjdk-1.8.0.9.zip
- **Windows x64** &ndash; https://dl.xamarin.com/OpenJDK/win64/microsoft-dist-openjdk-1.8.0.9.zip

## <a name="configure"></a>Configurare

Decomprimere i file nel percorso corretto:

- **Mac** &ndash; **$HOME/Library/Developer/Xamarin/jdk/microsoft_dist_openjdk_1.8.0.9**
- **Windows** &ndash; **C:\\Programmi\\Android\\jdk\\microsoft_dist_openjdk_1.8.0.9**

> [!IMPORTANT]
> Questo esempio usa la build 1.8.0.9. La versione che si scarica potrebbe essere tuttavia più recente.

Puntare l'IDE al nuovo JDK:

- **Mac** &ndash; Fare clic su **Strumenti > SDK Manager > Percorsi** e impostare **Percorso di Java SDK (JDK)** sul percorso completo dell'installazione di OpenJDK. Nell'esempio seguente questo percorso è impostato su **$HOME/Library/Developer/Xamarin/jdk/microsoft_dist_openjdk_1.8.0.9**.

![Impostazione del percorso di JDK per la distribuzione di Microsoft OpenJDK in Mac](openjdk-images/vsm.png)

- **Windows** &ndash; fare clic su **Strumenti > Opzioni > Xamarin > Impostazioni Android** e impostare **Percorso di Java Development Kit** sul percorso completo dell'installazione di OpenJDK. Nell'esempio seguente questo percorso è impostato su **C:\\Programmi\\Android\\jdk\\microsoft_dist_openjdk_1.8.0.9**:

![Impostazione del percorso di JDK per la distribuzione di Microsoft OpenJDK in Windows](openjdk-images/vs.png)

## <a name="revert"></a>Ripristinare

Per ripristinare Oracle JDK, impostare il percorso di Java SDK sul percorso di Oracle JDK usato in precedenza e ricompilare la soluzione. In Mac, è possibile ripristinare il percorso di Oracle JDK facendo clic su **Ripristina valori predefiniti**.

Se si verificano problemi con la distribuzione di Microsoft OpenJDK, segnalare i problemi tramite lo strumento per l'invio di commenti e suggerimenti nell'IDE in modo che possano essere monitorati e corretti rapidamente.

## <a name="known-issues--planned-fix-dates"></a>Problemi noti e date di correzione pianificate

La variabile di ambiente `JAVA_HOME` potrebbe non essere esportata correttamente nell'SDK e in Device Manager. Per risolvere il problema, è possibile impostare questa variabile di ambiente sul percorso di OpenJDK nel computer in uso. Questo problema è risolto nelle anteprime 15.9.

## <a name="summary"></a>Riepilogo

In questo articolo si è appreso come configurare l'IDE per usare la versione di anteprima della distribuzione di OpenJDK di Microsoft, per cui è previsto il rilascio della versione stabile più avanti nel 2018.
