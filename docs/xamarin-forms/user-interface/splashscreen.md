---
title: Schermata iniziale di Novell. Forms
description: Questo articolo illustra come creare una schermata iniziale per un'applicazione Novell. Forms.
ms.prod: xamarin
ms.assetId: 338C8F60-90F2-4B3D-BB47-7F846AE8DC6C
ms.technology: xamarin-forms
author: profexorgeek
ms.author: jusjohns
ms.date: 10/1/2019
ms.openlocfilehash: 2624c3f35a9cfac122a0b36ea8c1684d30f57824
ms.sourcegitcommit: 5110d1279809a2af58d3d66cd14c78113bb51436
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035755"
---
# <a name="xamarinforms-splash-screen"></a>Schermata iniziale di Novell. Forms

Le applicazioni presentano spesso un ritardo di avvio mentre l'applicazione completa il processo di inizializzazione. Gli sviluppatori potrebbero voler offrire un'esperienza personalizzata, in genere denominata schermata iniziale, durante l'avvio dell'applicazione. Questo articolo illustra come creare schermate iniziali per le applicazioni Novell. Forms.

Novell. Forms viene inizializzato su ogni piattaforma dopo che la sequenza di avvio nativa è stata completata. Novell. Forms inizializzato:

- Nel metodo `OnCreate` della classe `MainActivity` in Android.
- Nel metodo `FinishedLaunching` della classe `AppDelegate` in iOS.
- Nel metodo `OnLaunched` della classe `App` in UWP.

La schermata iniziale dovrebbe essere visualizzata appena possibile quando viene avviata l'applicazione, ma Novell. Forms non viene inizializzato fino alla fine della sequenza di avvio, il che significa che la schermata iniziale deve essere implementata all'esterno di Novell. Forms in ogni piattaforma. Le sezioni seguenti illustrano come creare una schermata iniziale in ogni piattaforma.

## <a name="xamarinforms-android-splash-screen"></a>Schermata iniziale di Novell. Forms Android

Per creare una schermata iniziale in Android è necessario creare un Splash `Activity` come `MainLauncher` con un tema speciale. Non appena viene avviata la schermata iniziale `Activity`, viene avviata la `Activity` principale con il tema dell'applicazione normale.

Per altre informazioni sulle schermate iniziali in Novell. Android, vedere [schermata iniziale di Novell. Android](~/android/user-interface/splash-screen.md).

## <a name="xamarinforms-ios-splash-screen"></a>Schermata iniziale di Novell. Forms iOS

Una schermata iniziale in iOS viene definita schermata di avvio. Per creare una schermata di avvio in iOS è necessario creare uno storyboard che definisce l'interfaccia utente della schermata di avvio e quindi impostare lo storyboard come schermata di avvio in **info. plist**.

Per altre informazioni sulle schermate di avvio in Novell. iOS, vedere [schermata di avvio di Novell. iOS](~/ios/app-fundamentals/images-icons/launch-screens.md).

## <a name="xamarinforms-uwp-splash-screen"></a>Schermata iniziale di Novell. Forms UWP

In UWP il **pacchetto. appxmanifest** contiene una scheda **Asset visivi** con un sottomenu della **schermata iniziale** . È possibile specificare la grafica della schermata iniziale in questo menu:

[schermata iniziale ![Setting in UWP](splashscreen-images/uwp-splashscreen-cropped.png)](splashscreen-images/uwp-splashscreen.png#lightbox)

## <a name="related-links"></a>Collegamenti correlati

- [Schermata iniziale di Novell. Android](~/android/user-interface/splash-screen.md)
- [Schermata di avvio di Novell. iOS](~/ios/app-fundamentals/images-icons/launch-screens.md)
