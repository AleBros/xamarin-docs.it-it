---
title: Attributo Debuggable
ms.topic: article
ms.prod: xamarin
ms.assetid: 1ABF90F1-6A70-45AE-9271-D90DC42807D0
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: 65037029d01d499421fd825f72347ae1bebd9966
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="debuggable-attribute"></a>Attributo Debuggable



Per consentire il debug, Android supporta Java Debug Wire Protocol (JDWP). Si tratta di una tecnologia che consente agli strumenti come ADB di comunicare con JVM. Anche se JDWP è importante durante lo sviluppo, è consigliabile disabilitarlo prima che l'applicazione viene pubblicata.

JDWP può essere il valore dell'attributo `android:debuggable` in un'applicazione Android. Xamarin.Android offre i modi seguenti per impostare questo attributo:

1.  Creare un file `AndroidManifext.xml` in cui impostare l'attributo `android:debuggable`.
1.  Includere `ApplicationAttribute` in un file `.CS`, ad esempio `[assembly: Application(Debuggable=false)]`.


Se sono presenti sia `AndroidManifest.xml` che `ApplicationAttribute`, i contenuti di `AndroidManifest.xml` sono prioritari rispetto a quanto specificato da `ApplicationAttribute`.

Se né `AndroidManifest.xml` né `ApplicationAttribute` sono presenti, il valore predefinito dell'attributo `android:debuggable` dipende dal fatto che vengano generati o meno i simboli di debug. Se i simboli di debug sono presenti, Xamarin.Android imposterà l'attributo `android:debuggable` su `true`.

Si noti che il valore dell'attributo `android:debuggable` NON dipende necessariamente dalla configurazione della build. Le build di rilascio possono avere l'attributo `android:debuggable` impostato su true.


## <a name="related-links"></a>Collegamenti correlati

- [Debuggable apps in the Android market](http://labs.mwrinfosecurity.com/blog/2011/07/07/debuggable-apps-in-android-market/) (App di cui è possibile eseguire il debug in Android Market)
