---
title: Attributo Debuggable
ms.topic: article
ms.prod: xamarin
ms.assetid: 3BE5EE1E-3FF6-4E95-7C9F-7B443EE3E94C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/05/2018
ms.openlocfilehash: db09ebe29b6c404bac892fd76389faf0b9e03d5b
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="debuggable-attribute"></a>Attributo Debuggable

<a name="Overview" />


Per consentire il debug, Android supporta Java Debug Wire Protocol (JDWP). Si tratta di una tecnologia che consente agli strumenti come ADB di comunicare con JVM. Anche se JDWP è importante durante lo sviluppo, è consigliabile disabilitarlo prima che l'applicazione viene pubblicata.

JDWP può essere il valore dell'attributo `android:debuggable` in un'applicazione Android. Xamarin.Android offre i modi seguenti per impostare questo attributo:

1.  Creare un file `AndroidManifext.xml` in cui impostare l'attributo `android:debuggable`.
1.  Includere `ApplicationAttribute` in un file `.CS`, ad esempio `[assembly: Application(Debuggable=false)]`.


Se sono presenti sia `AndroidManifest.xml` che `ApplicationAttribute`, i contenuti di `AndroidManifest.xml` sono prioritari rispetto a quanto specificato da `ApplicationAttribute`.

Se né `AndroidManifest.xml` né `ApplicationAttribute` sono presenti, il valore predefinito dell'attributo `android:debuggable` dipende dal fatto che vengano generati o meno i simboli di debug. Se i simboli di debug sono presenti, Xamarin.Android imposterà l'attributo `android:debuggable` su `true`.

Si noti che il valore dell'attributo `android:debuggable` NON dipende necessariamente dalla configurazione della build. Le build di rilascio possono avere l'attributo `android:debuggable` impostato su true.


## <a name="related-links"></a>Collegamenti correlati

- [Debuggable apps in the Android market](http://labs.mwrinfosecurity.com/blog/2011/07/07/debuggable-apps-in-android-market/) (App di cui è possibile eseguire il debug in Android Market)
