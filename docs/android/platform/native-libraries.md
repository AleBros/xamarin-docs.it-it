---
title: Uso di librerie native
ms.prod: xamarin
ms.assetid: 7AA6CEC8-C09E-BBDA-FDD6-E40559143548
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: b7d69e99327aa3d3e3e1f5e5dbc61697d1fb9b71
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "75489167"
---
# <a name="using-native-libraries"></a>Uso di librerie native

Xamarin.Android supporta l'uso di librerie native tramite il meccanismo PInvoke standard. È inoltre possibile raggruppare librerie native aggiuntive che non fanno parte del sistema operativo nel file con estensione apk.

Per distribuire una libreria nativa con un'applicazione Xamarin.Android, aggiungere il file binario della libreria al progetto e impostarne **l'azione** di compilazione su **AndroidNativeLibrary**.

Per distribuire una libreria nativa con un progetto di libreria Xamarin.Android , aggiungere il file binario della libreria al progetto e impostarne **l'azione** di compilazione su **EmbeddedNativeLibrary**.

Si noti che poiché Android supporta più interfacce aseguito (ABI) dell'applicazione, Xamarin.Android deve sapere per quale ABI è costruita la libreria nativa.
Esistono due modi per farlo:

1. Percorso "sniffing"
1. Utilizzando un `AndroidNativeLibrary/Abi` elemento all'interno del file di progetto

Con l'analisi del percorso, il nome della directory padre della libreria nativa viene usato per specificare l'ABI di destinazione della libreria. Pertanto, se `lib/armeabi/libfoo.so` si aggiunge al progetto, l'ABI `armeabi`verrà "annusato" come .

In alternativa, è possibile modificare il file di progetto per specificare in modo esplicito l'ABI da utilizzare:

```xml
<ItemGroup>
    <AndroidNativeLibrary Include="path/to/libfoo.so">
        <Abi>armeabi</Abi>
    </AndroidNativeLibrary>
</ItemGroup>
```

Per ulteriori informazioni sull'utilizzo delle librerie native, vedere [Interoperabilità con le librerie native](https://www.mono-project.com/docs/advanced/pinvoke/).

## <a name="debugging-native-code-with-visual-studio"></a>Debug di codice nativo con Visual StudioDebugging Native Code with Visual Studio

Se si utilizza *Visual Studio 2019* o *Visual Studio 2017,* non è necessario modificare i file di progetto come descritto in precedenza.
È possibile compilare ed eseguire il debug di C, all'interno della soluzione Xamarin.Android, aggiungendo un riferimento al progetto a un progetto di libreria **condivisa dinamica (Android)** di C.

Per eseguire il debug di codice nativo c'è nel progetto, attenersi alla seguente procedura:

1. Fare doppio clic su **Proprietà** progetto e selezionare la pagina **Opzioni Android.**
2. Scorrere verso il basso fino a **Opzioni di debug**.
3. Nel menu a discesa **Debugger** , selezionare **C ,** anziché il valore predefinito **di .NET (Xamarin)**).

Gli sviluppatori di Visual Studio C [SanAngeles_NativeDebug.](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk) e fare riferimento al nostro post sul [blog](https://blog.xamarin.com/build-and-debug-c-libraries-in-xamarin-android-apps-with-visual-studio-2015/) per ulteriori informazioni.

## <a name="related-links"></a>Collegamenti correlati

- [SanAngeles_NativeDebug (esempio)SanAngeles_NativeDebug (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/sanangeles-ndk)
- [Sviluppo di applicazioni native Android](https://blogs.msdn.microsoft.com/vcblog/2015/02/23/developing-xamarin-android-native-applications/)
