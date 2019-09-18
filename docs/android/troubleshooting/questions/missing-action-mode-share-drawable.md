---
title: "Android.Support.v7.AppCompat - Nessuna risorsa trovata corrispondente al nome specificato: attr 'android:actionModeShareDrawable'"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5814069C-FC43-41DE-B5A5-024D05E59929
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 3bce50169919df83ba1ab76c29d1ecc5c1da0bd6
ms.sourcegitcommit: 13e43f510da37ad55f1c2f5de1913fb0aede6362
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71021129"
---
# <a name="androidsupportv7appcompat---no-resource-found-that-matches-the-given-name-attr-androidactionmodesharedrawable"></a>Android.Support.v7.AppCompat - Nessuna risorsa trovata corrispondente al nome specificato: attr 'android:actionModeShareDrawable'

1. Assicurarsi di scaricare gli aggiornamenti più recenti e Android 5,0 (API 21) SDK tramite gestione Android SDK.

2. Assicurarsi di compilare l'applicazione con compileSdkVersion impostato su 21. Facoltativamente, è anche possibile impostare targetSdkVersion su 21.

3. Se è necessaria una versione precedente, ad esempio l'API 19, scaricare la rispettiva versione disponibile nella pagina NuGet:

[https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/](https://www.nuget.org/packages/Xamarin.Android.Support.v7.AppCompat/)

> [!NOTE]
> Se questa operazione viene installata manualmente tramite la console di gestione pacchetti, assicurarsi di installare anche la stessa versione di Novell. Android. support. v4

[https://www.nuget.org/packages/Xamarin.Android.Support.v4/](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)

Riferimento Stack Overflow:[https://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro](https://stackoverflow.com/questions/26431676/appcompat-v721-0-0-no-resource-found-that-matches-the-given-name-attr-andro)

## <a name="see-also"></a>Vedere anche

- [Quali pacchetti di Android SDK è necessario installare?](~/android/troubleshooting/questions/install-android-sdk-packages.md)
