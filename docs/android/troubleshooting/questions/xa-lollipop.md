---
title: In quale versione di Xamarin.Android è stato aggiunto il supporto di Lollipop?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63B6E10C-098D-4C82-9253-07CA62EA85A5
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 9e36189c771ed0c91a6030fd0ab615ab9af4dd52
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73026707"
---
# <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>In quale versione di Xamarin.Android è stato aggiunto il supporto di Lollipop?

> [!NOTE]
> Questa guida è stata originariamente scritta per l'anteprima di Android L.

- [Xamarin.Android 4.17](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_4/xamarin.android_4.17/index.md) aggiunto il supporto per l'anteprima di Android L.
- [Xamarin.Android 4.20](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_4/xamarin.android_4.20/index.md) aggiunto il supporto Perllipop Android.

Xamarin supporta solo attivamente l'attuale versione stabile degli strumenti Xamarin. Le informazioni riportate di seguito sono fornite "così come sono" per le versioni precedenti degli strumenti. Per le informazioni più recenti sulle versioni Xamarin, si prega di controllare le note di [rilascio](https://docs.microsoft.com/xamarin/whats-new/#product-release-notes).

## <a name="missing-androidjar-for-api-level-21-in-android-l-preview"></a>"Missing android.jar for API Level 21" in Android L Preview

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Il seguente messaggio di errore (o simile) potrebbe essere visualizzato:

```cmd
Error 1 Could not find android.jar for API Level 21.
```

Questo messaggio indica che la piattaforma Android SDK per API Level 21 non è installata. Installarlo in Gestione SDK di Android **(strumenti > Aprire Android SDK Manager...)** o modificare il progetto Xamarin.Android in modo che sia destinato a una versione dell'API installata.

Esistono alcune soluzioni alternative per questo problema:

1. Modificare il progetto in modo che sia destinato all'API 19 o inferiore.

2. Rinomina la cartella Android-21 da Android-21 ad Android-L. (Nella migliore delle quali diessa, questo dovrebbe essere usato solo come correzione temporanea e potrebbe non funzionare molto bene.)

   **%LOCALAPPDATA%\\\\Android Android-sdk\\piattaforme\\android-21**

3. Eseguire temporaneamente il downgrade all'anteprima di Android API Level 21 "L" [1]:

    1. Eliminare le **piattaforme\\\\%LOCALAPPDATA% Android\\\\Android-sdk android-21** 
    2. Estrarre [1] in **\\C:\\gli\\\\utenti\\&lt;come\\username&gt;\\AppData Local Android-sdk piattaforme** per creare una cartella **android-L.**

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

Il seguente messaggio di errore (o simile) potrebbe essere visualizzato:

```bash
/Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: 
Error: Could not find android.jar for API Level 21.**
```

Ciò significa che la piattaforma Android SDK per API Level 21 non è installata. Installarlo in Gestione SDK Android (Tools > SDK Manager... ) oppure modificare il progetto Xamarin.Android in modo che sia destinato a una versione dell'API installata.

Esistono alcune soluzioni alternative per questo problema:

1. Modificare il progetto in modo che sia destinato all'API 19 o inferiore.

2. Rinomina la cartella Android-21 da Android-21 ad Android-L. (Nella migliore delle quali diessa, questo dovrebbe essere usato solo come correzione temporanea e potrebbe non funzionare molto bene.)

   **/Libreria/Sviluppatore/Xamarin/android-sdk-macosx/android-21**

3. Eseguire temporaneamente il downgrade all'anteprima di Android API Level 21 "L" [1]:

    1. Eliminare **/Utenti/nomeutente/Libreria/Sviluppatore/Xamarin/android-sdk-macosx/android-21**
    2. Estrarre [1] in **/Utenti/nomeutente/Libreria/Developer/Xamarin/android-sdk-macosx** per creare una cartella **android-L.**

-----

[1] -[https://dl-ssl.google.com/android/repository/android-L_r04.zip](https://dl-ssl.google.com/android/repository/android-L_r04.zip)
