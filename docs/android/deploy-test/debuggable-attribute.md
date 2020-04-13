---
title: Attributo Debuggable
ms.prod: xamarin
ms.assetid: 1ABF90F1-6A70-45AE-9271-D90DC42807D0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 12/13/2019
ms.openlocfilehash: 6e54dba0c832596818c5163dc4e14ad1e659e040
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "75487971"
---
# <a name="debuggable-attribute"></a>Attributo Debuggable

Per consentire il debug, Android supporta Java Debug Wire Protocol (JDWP). Si tratta di una tecnologia che consente agli strumenti come ADB di comunicare con JVM. Sebbene JDWP sia importante durante lo sviluppo, deve essere disabilitato prima della pubblicazione dell'applicazione.

JDWP può essere configurato dal `android:debuggable` valore dell'attributo in un'applicazione Android. Scegliere _uno_ dei tre modi seguenti per impostare questo attributo in Xamarin.Android:

## <a name="androidmanifestxml"></a>AndroidManifest.xml

Creare o `AndroidManifext.xml` aprire un `android:debuggable` file e impostare l'attributo. Prestare particolare attenzione a non spedire la build di rilascio con il debug abilitato.

## <a name="add-an-application-class-attribute"></a>Aggiungere un attributo della classe ApplicationAdd an Application class attribute

Se l'app Xamarin.Android ha `[Application]` una classe con `[Application(Debuggable = true)]`un attributo, aggiorna l'attributo su . Impostarlo `false` su per disabilitare.

## <a name="add-an-assembly-attribute"></a>Aggiungere un attributo assembly

Se l'app Xamarin.Android NON `[Application]` dispone già di un `[assembly: Application(Debuggable=true)]` attributo di classe, aggiungere un attributo a livello di assembly in un file c. Impostarlo `false` su per disabilitare.

## <a name="summary"></a>Riepilogo

Se sono presenti sia `AndroidManifest.xml` che `ApplicationAttribute`, i contenuti di `AndroidManifest.xml` sono prioritari rispetto a quanto specificato da `ApplicationAttribute`.

Se si aggiungono sia un _attributo_ di classe che un attributo di assembly, si verifica un errore del compilatore:If you add both a class attribute and an assembly attribute, there will be a compiler error:

```error
"Error The "GenerateJavaStubs" task failed unexpectedly.
System.InvalidOperationException: Application cannot have both a type with an [Application] attribute and an [assembly:Application] attribute."
```

Per impostazione predefinita, se né il `AndroidManifest.xml` né il `ApplicationAttribute` è presente , il valore dell'attributo `android:debuggable` dipende dalla generazione o meno dei simboli di debug. Se sono presenti simboli di debug, Xamarin.Android imposterà l'attributo su `android:debuggable` `true` per l'utente.

> [!WARNING]
> Il valore `android:debuggable` dell'attributo NON dipende necessariamente dalla configurazione di compilazione. Le build di rilascio possono avere l'attributo `android:debuggable` impostato su true. Se si usa un attributo per impostare questo valore, è possibile scegliere di eseguire il wrapping dell'attributo in una direttiva del compilatore:If you use an attribute to set this value, you can choose to wrap the attribute in a compiler directive:
> 
> ```csharp
> #if DEBUG
> [Application(Debuggable = true)]
> #else
> [Application(Debuggable = false)]
> #endif
> ```

## <a name="related-links"></a>Collegamenti correlati

- [Debuggable apps in the Android market](https://labs.f-secure.com/archive/debuggable-apps-in-android-market/) (App di cui è possibile eseguire il debug in Android Market)
