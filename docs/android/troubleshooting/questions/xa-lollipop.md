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
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "73026707"
---
# <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>In quale versione di Xamarin.Android è stato aggiunto il supporto di Lollipop?

> [!NOTE]
> Questa guida è stata scritta originariamente per Android L Preview.

- [Novell. Android 4,17](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_4/xamarin.android_4.17/index.md) ha aggiunto il supporto per Android L Preview.
- [Novell. Android 4,20](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_4/xamarin.android_4.20/index.md) ha aggiunto il supporto di Android Lollipop.

Novell supporta attivamente solo la versione stabile corrente degli strumenti di Novell. Le informazioni riportate di seguito vengono fornite "così come sono" per le versioni precedenti degli strumenti. Per le informazioni più recenti sulle versioni di Novell, vedere le [Note sulla versione](https://docs.microsoft.com/xamarin/whats-new/#product-release-notes).

## <a name="missing-androidjar-for-api-level-21-in-android-l-preview"></a>"Manca Android. jar per l'API livello 21" in Android L Preview

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

È possibile che venga visualizzato il messaggio di errore seguente (o simile):

```cmd
Error 1 Could not find android.jar for API Level 21.
```

Questo messaggio indica che la piattaforma Android SDK per il livello API 21 non è installata. Installarlo in gestione Android SDK (**strumenti > aprire Android SDK Manager...** ) oppure modificare il progetto Novell. Android in modo che sia destinato a una versione API installata.

Esistono alcune soluzioni alternative per questo problema:

1. Modificare il progetto in modo che sia destinato all'API 19 o inferiore.

2. Rinominare la cartella Android-21 da Android-21 ad Android-L. (Al meglio, questo dovrebbe essere usato solo come correzione temporanea e potrebbe non funzionare molto bene).

   **% LOCALAPPDATA%\\Android\\Android-SDK\\piattaforme\\Android-21**

3. Eseguire temporaneamente il downgrade all'anteprima dell'API Android livello 21 "L" [1]:

    1. Eliminare le **piattaforme di\\android\\Android-SDK di% LocalAppData%\\Android,\\Android-21** 
    2. Estrarre [1] in **C:\\utenti\\&lt;nomeutente&gt;\\AppData\\locale\\android\\android-sdk\\piattaforme** per creare una cartella **Android-L** .

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

È possibile che venga visualizzato il messaggio di errore seguente (o simile):

```bash
/Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: 
Error: Could not find android.jar for API Level 21.**
```

Ciò significa che la piattaforma di Android SDK per il livello API 21 non è installata. Installarlo in gestione Android SDK (strumenti > SDK Manager...) oppure modificare il progetto Novell. Android in modo che sia destinato a una versione API installata.

Esistono alcune soluzioni alternative per questo problema:

1. Modificare il progetto in modo che sia destinato all'API 19 o inferiore.

2. Rinominare la cartella Android-21 da Android-21 ad Android-L. (Al meglio, questo dovrebbe essere usato solo come correzione temporanea e potrebbe non funzionare molto bene).

   **~/Library/Developer/Xamarin/android-sdk-macosx/android-21**

3. Eseguire temporaneamente il downgrade all'anteprima dell'API Android livello 21 "L" [1]:

    1. Elimina **/Users/username/Library/Developer/Xamarin/Android-SDK-MacOSX/Android-21**
    2. Estrarre [1] in **/Users/username/Library/Developer/Xamarin/Android-SDK-MacOSX** per creare una cartella **Android-L** .

-----

[1]- [https://dl-ssl.google.com/android/repository/android-L_r04.zip](https://dl-ssl.google.com/android/repository/android-L_r04.zip)
