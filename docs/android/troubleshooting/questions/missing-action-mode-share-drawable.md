---
title: "Android.Support.v7.AppCompat - Nessuna risorsa trovata che corrisponde al nome specificato: attr 'android: actionModeShareDrawable'"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5814069C-FC43-41DE-B5A5-024D05E59929
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: fea681ac3b99abed09d3d3e745bd4bf6015970df
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50112416"
---
# <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawable"></a>Android.Support.v7.AppCompat - Nessuna risorsa trovata che corrisponde al nome specificato: attr 'android: actionModeShareDrawable'

1. Assicurarsi di che scaricare l'ultime funzionalità aggiuntive, nonché di Android 5.0 (API 21) SDK tramite Android SDK Manager.

2. Assicurarsi che si esegue la compilazione dell'applicazione con compileSdkVersion impostata su 21. È facoltativamente possibile impostare il targetSdkVersion a 21 anche.

3. Se è necessaria una versione precedente, ad esempio API 19, scaricare la versione corrispondente trovata nella pagina di Nuget:

[https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)

*Nota*: se si installa manualmente questo mediante la Console di gestione pacchetti, assicurarsi di installare anche la stessa versione di Xamarin.Android.Support.v4

[https://www.nuget.org/packages/Xamarin.Android.Support.v4/](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)

Riferimento di Overflow dello stack: [http://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro](http://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro)

## <a name="see-also"></a>Vedere anche

- [Quali pacchetti di Android SDK è necessario installare?](~/android/troubleshooting/questions/install-android-sdk-packages.md)

