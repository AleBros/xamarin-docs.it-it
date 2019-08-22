---
title: Attributo Debuggable
ms.prod: xamarin
ms.assetid: 1ABF90F1-6A70-45AE-9271-D90DC42807D0
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/05/2018
ms.openlocfilehash: e0fb6c1ced4953fd03939f56eb4011d956dfcca6
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69525607"
---
# <a name="debuggable-attribute"></a>Attributo Debuggable



Per consentire il debug, Android supporta Java Debug Wire Protocol (JDWP). Si tratta di una tecnologia che consente agli strumenti come ADB di comunicare con JVM. Anche se JDWP è importante durante lo sviluppo, è consigliabile disabilitarlo prima che l'applicazione viene pubblicata.

JDWP può essere il valore dell'attributo `android:debuggable` in un'applicazione Android. Xamarin.Android offre i modi seguenti per impostare questo attributo:

1. Creare un file `AndroidManifext.xml` in cui impostare l'attributo `android:debuggable`.
2. Includere `ApplicationAttribute` in un file `.CS`, ad esempio `[assembly: Application(Debuggable=false)]`.


Se sono presenti sia `AndroidManifest.xml` che `ApplicationAttribute`, i contenuti di `AndroidManifest.xml` sono prioritari rispetto a quanto specificato da `ApplicationAttribute`.

Se né `AndroidManifest.xml` né `ApplicationAttribute` sono presenti, il valore predefinito dell'attributo `android:debuggable` dipende dal fatto che vengano generati o meno i simboli di debug. Se i simboli di debug sono presenti, Xamarin.Android imposterà l'attributo `android:debuggable` su `true`.

Si noti che il valore dell'attributo `android:debuggable` NON dipende necessariamente dalla configurazione della build. Le build di rilascio possono avere l'attributo `android:debuggable` impostato su true.


## <a name="related-links"></a>Collegamenti correlati

- [Debuggable apps in the Android market](http://labs.mwrinfosecurity.com/blog/2011/07/07/debuggable-apps-in-android-market/) (App di cui è possibile eseguire il debug in Android Market)
