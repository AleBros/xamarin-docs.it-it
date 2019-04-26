---
title: In quale versione di Xamarin.Android è stato aggiunto il supporto di Lollipop?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63B6E10C-098D-4C82-9253-07CA62EA85A5
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 7e31f9ad46a04b648a6a1f24c075426f7d98a663
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61228256"
---
# <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>In quale versione di Xamarin.Android è stato aggiunto il supporto di Lollipop?

**Nota:** Questa guida è stato scritto originariamente per l'anteprima L Android.

-   [Xamarin. Android 4.17](https://developer.xamarin.com/releases/android/xamarin.android_4/xamarin.android_4.17/) ha aggiunto il supporto di Android L anteprima.
-   [Xamarin. Android 4.20](https://developer.xamarin.com/releases/android/xamarin.android_4/xamarin.android_4.20/) ha aggiunto il supporto di Android Lollipop.

Xamarin supporta attivamente solo la versione stabile corrente degli strumenti di Xamarin. Le informazioni seguenti vengono fornite "come-è" per le versioni precedenti degli strumenti. Per informazioni aggiornate sulle versioni di Xamarin, controllare [qui](http://releases.xamarin.com/).

## <a name="missing-androidjar-for-api-level-21-in-android-l-preview"></a>"Mancante android.jar per il livello API 21", disponibile in anteprima L Android

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Il seguente messaggio di errore (o simile) potrebbe essere visualizzato:

```cmd
Error 1 Could not find android.jar for API Level 21.
```

Questo messaggio indica che la piattaforma Android SDK per API livello 21 non è installata. Installarla in Android SDK Manager (**strumenti > Apri Android SDK Manager...** ), o modificare il progetto xamarin. Android come destinazione una versione API installata.

Esistono alcune soluzioni alternative per risolvere questo problema:

1. Modificare il progetto in modo che è destinata a API 19 o inferiore.

2. Rinominare la cartella 21 di android da 21 di android per android-L. (Nella migliore delle ipotesi, questa deve essere usata solo come correzione temporanea e potrebbero non funzionare molto bene affatto.)

   **%LOCALAPPDATA%\\Android\\android-sdk\\platforms\\android-21**

3. Effettuare il downgrade temporaneamente nuovamente all'anteprima di Android API livello 21 "L" [1]:

    1.  Eliminare il **% LOCALAPPDATA %\\Android\\android-sdk\\piattaforme\\21 di android** 
    2.  Estrarre [1] in **c:\\gli utenti\\&lt;username&gt;\\AppData\\locale\\Android\\android-sdk\\piattaforme**  per creare un' **android-L** cartella.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Il seguente messaggio di errore (o simile) potrebbe essere visualizzato:

```bash
/Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: 
Error: Could not find android.jar for API Level 21.**
```

Ciò significa che la piattaforma Android SDK per API livello 21 non è installata. Installarla in Android SDK Manager (Strumenti > SDK Manager...), o modificare il progetto xamarin. Android come destinazione una versione API installata.

Esistono alcune soluzioni alternative per risolvere questo problema:

1. Modificare il progetto in modo che è destinata a API 19 o inferiore.

2. Rinominare la cartella 21 di android da 21 di android per android-L. (Nella migliore delle ipotesi, questa deve essere usata solo come correzione temporanea e potrebbero non funzionare molto bene affatto.)

   **~/Library/Developer/Xamarin/android-sdk-macosx/android-21**

3. Effettuare il downgrade temporaneamente nuovamente all'anteprima di Android API livello 21 "L" [1]:

    1.  Delete **/Users/username/Library/Developer/Xamarin/android-sdk-macosx/android-21**
    2.  Estrarre [1] in **/Users/username/Library/Developer/Xamarin/android-sdk-macosx** per creare un **android-L** cartella.

-----


[1] - [https://dl-ssl.google.com/android/repository/android-L_r04.zip](https://dl-ssl.google.com/android/repository/android-L_r04.zip)
