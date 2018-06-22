---
title: "Android.Support.v7.AppCompat - trovata alcuna risorsa corrispondente al nome specificato: attr 'android: actionModeShareDrawable'"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5814069C-FC43-41DE-B5A5-024D05E59929
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 07655587642c3e1aa94d035e76f6f6758340546d
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30774896"
---
# <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawable"></a>Android.Support.v7.AppCompat - trovata alcuna risorsa corrispondente al nome specificato: attr 'android: actionModeShareDrawable'

1. Assicurarsi che scaricare l'ultime funzionalità aggiuntive, nonché di Android 5.0 (API 21) SDK mediante Android SDK Manager.

2. Verificare che si esegue la compilazione dell'applicazione con compileSdkVersion impostati su 21. Facoltativamente, è possibile impostare il targetSdkVersion su nonché 21.

3. Se è necessaria una versione precedente, ad esempio API 19, scaricare la versione corrispondente trovata nella pagina di Nuget:

[https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)

*Nota*: se si installa manualmente questo mediante la Console di gestione pacchetti, assicurarsi di installare anche la stessa versione di Xamarin.Android.Support.v4

[https://www.nuget.org/packages/Xamarin.Android.Support.v4/](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)

Riferimento di Overflow dello stack: [http://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro](http://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro)

## <a name="see-also"></a>Vedere anche

- [Quali pacchetti di Android SDK è necessario installare?](~/android/troubleshooting/questions/install-android-sdk-packages.md)

