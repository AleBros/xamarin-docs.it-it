---
title: Utilizzo di librerie Native
ms.prod: xamarin
ms.assetid: 7AA6CEC8-C09E-BBDA-FDD6-E40559143548
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 0fa66f3a16047c18af19cb7257c778b498bc0c9b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30774811"
---
# <a name="using-native-libraries"></a>Utilizzo di librerie Native

Xamarin supporta l'utilizzo di librerie native tramite il meccanismo standard di PInvoke. È anche possibile raggruppare le librerie native aggiuntive che non fanno parte del sistema operativo nel file con estensione apk.

Per distribuire una libreria nativa con un'applicazione di xamarin, aggiungere la libreria binaria al progetto e impostare il relativo **azione di compilazione** a **AndroidNativeLibrary**.

Per distribuire una libreria nativa con un progetto libreria di xamarin, aggiungere la libreria binaria al progetto e impostare il relativo **azione di compilazione** a **EmbeddedNativeLibrary**.

Si noti che poiché Android supporta più Application Binary Interface (ABIs), xamarin deve conoscere quali ABI è stata compilata per la libreria nativa.
Esistono due modi per farlo:

1.  Percorso "analisi"
1.  Utilizzando un `AndroidNativeLibrary/Abi` elemento all'interno del file di progetto


Con l'analisi del percorso, il nome della directory padre della libreria nativa viene usato per specificare l'ABI di destinazione della libreria. Pertanto, se si aggiungono `lib/armeabi/libfoo.so` al progetto, quindi l'ABI verrà essere "rilevata" come `armeabi`.

In alternativa, è possibile modificare il file di progetto per specificare in modo esplicito l'interfaccia ABI da usare:

```xml
<ItemGroup>
    <AndroidNativeLibrary Include="path/to/libfoo.so">
        <Abi>armeabi</Abi>
    </AndroidNativeLibrary>
</ItemGroup>
```

Per ulteriori informazioni sull'utilizzo di librerie native, vedere [interoperabilità alle librerie native](http://www.mono-project.com/docs/advanced/pinvoke/).

## <a name="debugging-native-code-with-visual-studio-2015"></a>Debug del codice nativo con Visual Studio 2015

Se si utilizza *Visual Studio 2015*, non è necessario modificare i file di progetto (come descritto in precedenza).
È possibile compilare ed eseguire il debug C++ all'interno della soluzione xamarin, semplicemente aggiungendo un riferimento al progetto di C++ **libreria condivisa dinamica (Android)** progetto.

Gli sviluppatori di Visual Studio C++ è possono visualizzare il [SanAngeles_NativeDebug](https://developer.xamarin.com/samples/monodroid/SanAngeles_NDK/) di esempio per eseguire il debug di C++ da Visual Studio 2015 con Xamarin; e fare riferimento al nostro [post di blog](https://blog.xamarin.com/build-and-debug-c-libraries-in-xamarin-android-apps-with-visual-studio-2015/) per ulteriori informazioni.



## <a name="related-links"></a>Collegamenti correlati

- [SanAngeles_NativeDebug (esempio)](https://developer.xamarin.com/samples/monodroid/SanAngeles_NDK/)
