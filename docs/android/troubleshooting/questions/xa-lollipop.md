---
title: In quale versione di Xamarin.Android è stato aggiunto il supporto di Lollipop?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63B6E10C-098D-4C82-9253-07CA62EA85A5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 4fe1bd4dda9a54eb3a1692f07d1069adb39345cb
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523287"
---
# <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>In quale versione di Xamarin.Android è stato aggiunto il supporto di Lollipop?

> [!NOTE]
> Questa guida è stata scritta originariamente per Android L Preview.

- [Novell. Android 4,17](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_4/xamarin.android_4.17/index.md) ha aggiunto il supporto per Android L Preview.
- [Novell. Android 4,20](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_4/xamarin.android_4.20/index.md) ha aggiunto il supporto di Android Lollipop.

Novell supporta attivamente solo la versione stabile corrente degli strumenti di Novell. Le informazioni riportate di seguito vengono fornite "così come sono" per le versioni precedenti degli strumenti. Per le informazioni più aggiornate sulle versioni di Novell, vedere [qui](http://releases.xamarin.com/).

## <a name="missing-androidjar-for-api-level-21-in-android-l-preview"></a>"Manca Android. jar per l'API livello 21" in Android L Preview

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

È possibile che venga visualizzato il messaggio di errore seguente (o simile):

```cmd
Error 1 Could not find android.jar for API Level 21.
```

Questo messaggio indica che la piattaforma Android SDK per il livello API 21 non è installata. Installarlo in gestione Android SDK (**strumenti > aprire Android SDK Manager...** ) oppure modificare il progetto Novell. Android in modo che sia destinato a una versione API installata.

Esistono alcune soluzioni alternative per questo problema:

1. Modificare il progetto in modo che sia destinato all'API 19 o inferiore.

2. Rinominare la cartella Android-21 da Android-21 ad Android-L. (Al meglio, questo dovrebbe essere usato solo come correzione temporanea e potrebbe non funzionare molto bene).

   **%LOCALAPPDATA%\\Android\\android-sdk\\platforms\\android-21**

3. Eseguire temporaneamente il downgrade all'anteprima dell'API Android livello 21 "L" [1]:

    1. Eliminare le **piattaforme\\\\\\Android-21 dell'SDK% LocalAppData% Android-21\\** 
    2. Estrarre [1] in **C:\\utenti\\&lt;nomeutente&gt;\\AppDataLocal\\AndroidAndroid-\\SDK Platforms da creare\\\\** una cartella **Android-L** .

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

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

    1. Delete **/Users/username/Library/Developer/Xamarin/android-sdk-macosx/android-21**
    2. Estrarre [1] in **/Users/username/Library/Developer/Xamarin/Android-SDK-MacOSX** per creare una cartella **Android-L** .

-----


[1] - [https://dl-ssl.google.com/android/repository/android-L_r04.zip](https://dl-ssl.google.com/android/repository/android-L_r04.zip)
