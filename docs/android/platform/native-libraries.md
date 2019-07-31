---
title: Uso di librerie native
ms.prod: xamarin
ms.assetid: 7AA6CEC8-C09E-BBDA-FDD6-E40559143548
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: d3e5b36f2cbc48dac09b55bfba8c3613db12bbc8
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68643278"
---
# <a name="using-native-libraries"></a>Uso di librerie native

Novell. Android supporta l'utilizzo di librerie native tramite il meccanismo PInvoke standard. È anche possibile aggregare librerie native aggiuntive che non fanno parte del sistema operativo nel file con estensione APK.

Per distribuire una libreria nativa con un'applicazione Novell. Android, aggiungere il file binario della libreria al progetto e impostare l' **azione di compilazione** su **AndroidNativeLibrary**.

Per distribuire una libreria nativa con un progetto di libreria Novell. Android, aggiungere il file binario della libreria al progetto e impostare l' **azione di compilazione** su **EmbeddedNativeLibrary**.

Si noti che poiché Android supporta più interfacce binarie dell'applicazione (ABI), Novell. Android deve essere in grado di stabilire per quale ABI è stata compilata la libreria nativa.
Esistono due modi per farlo:

1.  Percorso "sniffing"
1.  Utilizzando un `AndroidNativeLibrary/Abi` elemento nel file di progetto


Con l'analisi del percorso, il nome della directory padre della libreria nativa viene usato per specificare l'ABI di destinazione della libreria. Quindi, se si aggiunge `lib/armeabi/libfoo.so` al progetto, l'Abi verrà "sniffata" come. `armeabi`

In alternativa, è possibile modificare il file di progetto per specificare in modo esplicito l'ABI da usare:

```xml
<ItemGroup>
    <AndroidNativeLibrary Include="path/to/libfoo.so">
        <Abi>armeabi</Abi>
    </AndroidNativeLibrary>
</ItemGroup>
```

Per ulteriori informazioni sull'utilizzo delle librerie native, vedere interoperabilità [con librerie native](https://www.mono-project.com/docs/advanced/pinvoke/).

## <a name="debugging-native-code-with-visual-studio"></a>Debug del codice nativo con Visual Studio

Se si usa *Visual studio 2019* o *Visual Studio 2017*, non è necessario modificare i file di progetto come descritto in precedenza.
È possibile compilare ed eseguire C++ il debug all'interno della soluzione Novell. Android aggiungendo un riferimento di C++ progetto a un progetto di **libreria condivisa dinamica (Android)** .

Per eseguire il C++ debug del codice nativo nel progetto, attenersi alla procedura seguente:

1. Fare doppio clic su **Proprietà** progetto e selezionare la pagina **Opzioni Android** .
2. Scorrere verso il basso fino a **Opzioni di debug**.
3. Nel menu a discesa **debugger** selezionare **C++** , anziché il valore predefinito di **.NET (Novell)** .

Gli sviluppatori C++ di Visual Studio possono vedere l'esempio [SanAngeles_NativeDebug](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk) per C++ provare a eseguire il debug da visual studio 2019 o Visual Studio 2017 con Novell; Per ulteriori informazioni, vedere il [post di Blog](https://blog.xamarin.com/build-and-debug-c-libraries-in-xamarin-android-apps-with-visual-studio-2015/) .



## <a name="related-links"></a>Collegamenti correlati

- [SanAngeles_NativeDebug (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk)
- [Sviluppo di applicazioni native Novell Android](https://blogs.msdn.microsoft.com/vcblog/2015/02/23/developing-xamarin-android-native-applications/)
