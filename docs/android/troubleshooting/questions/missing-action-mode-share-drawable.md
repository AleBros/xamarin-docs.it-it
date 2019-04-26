---
title: "Android.Support.v7.AppCompat - Nessuna risorsa trovata corrispondente al nome specificato: attr 'android:actionModeShareDrawable'"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5814069C-FC43-41DE-B5A5-024D05E59929
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: ff2a586773e33a1f4cf78657c3c69c22e79ed047
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61153373"
---
# <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawable"></a>Android.Support.v7.AppCompat - Nessuna risorsa trovata corrispondente al nome specificato: attr 'android:actionModeShareDrawable'

1. Assicurarsi di che scaricare l'ultime funzionalità aggiuntive, nonché di Android 5.0 (API 21) SDK tramite Android SDK Manager.

2. Assicurarsi che si esegue la compilazione dell'applicazione con compileSdkVersion impostata su 21. È facoltativamente possibile impostare il targetSdkVersion a 21 anche.

3. Se è necessaria una versione precedente, ad esempio API 19, scaricare la versione corrispondente trovata nella pagina di Nuget:

[https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)

*Nota*: Se si installa manualmente questo mediante la Console di gestione pacchetti, assicurarsi di che installare anche la stessa versione di Xamarin.Android.Support.v4

[https://www.nuget.org/packages/Xamarin.Android.Support.v4/](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)

Riferimento di Overflow dello stack: [https://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro](https://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro)

## <a name="see-also"></a>Vedere anche

- [Quali pacchetti di Android SDK è necessario installare?](~/android/troubleshooting/questions/install-android-sdk-packages.md)

