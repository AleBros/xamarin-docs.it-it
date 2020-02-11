---
title: Attributo Debuggable
ms.prod: xamarin
ms.assetid: 1ABF90F1-6A70-45AE-9271-D90DC42807D0
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 12/13/2019
ms.openlocfilehash: 6e54dba0c832596818c5163dc4e14ad1e659e040
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75487971"
---
# <a name="debuggable-attribute"></a>Attributo Debuggable

Per consentire il debug, Android supporta Java Debug Wire Protocol (JDWP). Si tratta di una tecnologia che consente agli strumenti come ADB di comunicare con JVM. Mentre JDWP è importante durante lo sviluppo, è necessario disabilitarlo prima di pubblicare l'applicazione.

JDWP può essere configurato in base al valore dell'attributo `android:debuggable` in un'applicazione Android. Scegliere _uno_ dei tre modi seguenti per impostare questo attributo in Xamarin.Android:

## <a name="androidmanifestxml"></a>AndroidManifest.xml

Creare o aprire `AndroidManifext.xml` file e impostare l'attributo `android:debuggable`. Prestare particolare attenzione a non inviare la build di rilascio con il debug abilitato.

## <a name="add-an-application-class-attribute"></a>Aggiungere un attributo della classe dell'applicazione

Se l'app Xamarin.Android dispone di una classe con un attributo `[Application]`, aggiornare l'attributo a `[Application(Debuggable = true)]`. Impostarlo su `false` per disabilitarlo.

## <a name="add-an-assembly-attribute"></a>Aggiungere un attributo di assembly

Se l'app Xamarin.Android non dispone già di un attributo `[Application]` Class, aggiungere un attributo a livello di assembly `[assembly: Application(Debuggable=true)]` in un file c#. Impostarlo su `false` per disabilitarlo.

## <a name="summary"></a>Riepilogo

Se sono presenti sia `AndroidManifest.xml` che `ApplicationAttribute`, i contenuti di `AndroidManifest.xml` sono prioritari rispetto a quanto specificato da `ApplicationAttribute`.

Se si aggiungono sia un attributo di classe _che_ un attributo di assembly, si verifica un errore del compilatore:

```error
"Error The "GenerateJavaStubs" task failed unexpectedly.
System.InvalidOperationException: Application cannot have both a type with an [Application] attribute and an [assembly:Application] attribute."
```

Per impostazione predefinita, se non è presente né il `AndroidManifest.xml` né il `ApplicationAttribute`, il valore dell'attributo `android:debuggable` dipende dal fatto che vengano generati o meno i simboli di debug. Se sono presenti simboli di debug, Xamarin.Android imposterà l'attributo `android:debuggable` su `true`.

> [!WARNING]
> Il valore dell'attributo `android:debuggable` non dipende necessariamente dalla configurazione della compilazione. Le build di rilascio possono avere l'attributo `android:debuggable` impostato su true. Se si usa un attributo per impostare questo valore, è possibile scegliere di eseguire il wrapping dell'attributo in una direttiva del compilatore:
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
