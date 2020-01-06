---
title: Uso di librerie native
ms.prod: xamarin
ms.assetid: 7AA6CEC8-C09E-BBDA-FDD6-E40559143548
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: b7d69e99327aa3d3e3e1f5e5dbc61697d1fb9b71
ms.sourcegitcommit: d0e6436edbf7c52d760027d5e0ccaba2531d9fef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75489167"
---
# <a name="using-native-libraries"></a>Uso di librerie native

Novell. Android supporta l'utilizzo di librerie native tramite il meccanismo PInvoke standard. È anche possibile aggregare librerie native aggiuntive che non fanno parte del sistema operativo nel file con estensione APK.

Per distribuire una libreria nativa con un'applicazione Novell. Android, aggiungere il file binario della libreria al progetto e impostare l' **azione di compilazione** su **AndroidNativeLibrary**.

Per distribuire una libreria nativa con un progetto di libreria Novell. Android, aggiungere il file binario della libreria al progetto e impostare l' **azione di compilazione** su **EmbeddedNativeLibrary**.

Si noti che poiché Android supporta più interfacce binarie dell'applicazione (ABI), Novell. Android deve essere in grado di stabilire per quale ABI è stata compilata la libreria nativa.
Esistono due modi per farlo:

1. Percorso "sniffing"
1. Utilizzando un elemento `AndroidNativeLibrary/Abi` all'interno del file di progetto

Con l'analisi del percorso, il nome della directory padre della libreria nativa viene usato per specificare l'ABI di destinazione della libreria. Di conseguenza, se si aggiungono `lib/armeabi/libfoo.so` al progetto, l'ABI verrà "sniffata" come `armeabi`.

In alternativa, è possibile modificare il file di progetto per specificare in modo esplicito l'ABI da usare:

```xml
<ItemGroup>
    <AndroidNativeLibrary Include="path/to/libfoo.so">
        <Abi>armeabi</Abi>
    </AndroidNativeLibrary>
</ItemGroup>
```

Per ulteriori informazioni sull'utilizzo delle librerie native, vedere [interoperabilità con librerie native](https://www.mono-project.com/docs/advanced/pinvoke/).

## <a name="debugging-native-code-with-visual-studio"></a>Debug del codice nativo con Visual Studio

Se si usa *Visual studio 2019* o *Visual Studio 2017*, non è necessario modificare i file di progetto come descritto in precedenza.
È possibile compilare ed eseguire C++ il debug all'interno della soluzione Novell. Android aggiungendo un riferimento di C++ progetto a un progetto di **libreria condivisa dinamica (Android)** .

Per eseguire il C++ debug del codice nativo nel progetto, attenersi alla procedura seguente:

1. Fare doppio clic su **Proprietà** progetto e selezionare la pagina **Opzioni Android** .
2. Scorrere verso il basso fino a **Opzioni di debug**.
3. Nel menu a discesa **debugger** selezionare **C++** , anziché il valore predefinito di **.NET (Novell)** .

Gli sviluppatori C++ di Visual Studio possono vedere l'esempio di [SanAngeles_NativeDebug](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk) per provare a eseguire il debug C++ da visual studio 2019 o Visual Studio 2017 con Novell; Per ulteriori informazioni, vedere il [post di Blog](https://blog.xamarin.com/build-and-debug-c-libraries-in-xamarin-android-apps-with-visual-studio-2015/) .

## <a name="related-links"></a>Collegamenti correlati

- [SanAngeles_NativeDebug (esempio)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk)
- [Sviluppo di applicazioni native Novell Android](https://blogs.msdn.microsoft.com/vcblog/2015/02/23/developing-xamarin-android-native-applications/)
