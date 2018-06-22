---
title: La versione di xamarin aggiunto il supporto di simbolo?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 63B6E10C-098D-4C82-9253-07CA62EA85A5
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 065c68a373f67bb352b59dc88ef89daec8b51ef8
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30764469"
---
# <a name="what-version-of-xamarinandroid-added-lollipop-support"></a>La versione di xamarin aggiunto il supporto di simbolo?

**Nota:** questa guida è stato inizialmente scritto per l'anteprima L Android.

-   [Xamarin 4.17](https://developer.xamarin.com/releases/android/xamarin.android_4/xamarin.android_4.17/) aggiunto il supporto Android L anteprima.
-   [Xamarin 4.20](https://developer.xamarin.com/releases/android/xamarin.android_4/xamarin.android_4.20/) aggiunto il supporto Android simbolo.

Xamarin supporta attivamente solo la versione stabile corrente degli strumenti di Xamarin. Le informazioni riportate di seguito viene fornite "come-è" per le versioni precedenti degli strumenti. Per informazioni aggiornate sulle versioni di Xamarin, controllare [qui](http://releases.xamarin.com/).

## <a name="missing-androidjar-for-api-level-21-in-android-l-preview"></a>"Mancante android.jar per il livello API 21", in anteprima L Android

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Il seguente messaggio di errore (o simile) potrebbero essere visualizzate:

```cmd
Error 1 Could not find android.jar for API Level 21.
```

Questo messaggio indica che la piattaforma Android SDK per livello API 21 non è installata. L'installazione in Android SDK Manager (Strumenti > Open Android SDK Manager...), o modificare il progetto di xamarin per indirizzare la versione dell'API che è installata.

Esistono alcune soluzioni alternative per questo problema:

1. Modificare il progetto in modo che faccia riferimento API 19 o inferiore.

2. Rinominare la cartella android-21-21 android android-L. (Nella migliore delle ipotesi, deve essere utilizzato solo come correzione temporanea e potrebbe non funzionare molto bene affatto.)

   **%LOCALAPPDATA%\\Android\\android-sdk\\platforms\\android-21**

3. Effettuare il downgrade temporaneamente al livello API Android 21 "L" Anteprima [1]:

    1.  Eliminare il **% LOCALAPPDATA %\\Android\\android sdk\\piattaforme\\android-21** 
    2.  Estrarre [1] in **c:\\utenti\\<username>\\AppData\\locale\\Android\\android sdk\\piattaforme** per creare un **android-L** cartella.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Il seguente messaggio di errore (o simile) potrebbero essere visualizzate:

```bash
/Library/Frameworks/Mono.framework/External/xbuild/Xamarin/Android/Xamarin.Android.Common.targets: 
Error: Could not find android.jar for API Level 21.**
```

Ciò significa che la piattaforma Android SDK per livello API 21 non è installata. L'installazione in Android SDK Manager (Strumenti > SDK Manager...), o modificare il progetto di xamarin per indirizzare la versione dell'API che è installata.

Esistono alcune soluzioni alternative per questo problema:

1. Modificare il progetto in modo che faccia riferimento API 19 o inferiore.

2. Rinominare la cartella android-21-21 android android-L. (Nella migliore delle ipotesi, deve essere utilizzato solo come correzione temporanea e potrebbe non funzionare molto bene affatto.)

   **~/Library/Developer/Xamarin/android-sdk-macosx/android-21**

3. Effettuare il downgrade temporaneamente al livello API Android 21 "L" Anteprima [1]:

    1.  Delete **/Users/username/Library/Developer/Xamarin/android-sdk-macosx/android-21**
    2.  Estrarre [1] in **/Users/username/Library/Developer/Xamarin/android-sdk-macosx** per creare un **android-L** cartella.

-----


[1] - [https://dl-ssl.google.com/android/repository/android-L_r04.zip](https://dl-ssl.google.com/android/repository/android-L_r04.zip)
