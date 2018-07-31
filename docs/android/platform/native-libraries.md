---
title: Uso di librerie Native
ms.prod: xamarin
ms.assetid: 7AA6CEC8-C09E-BBDA-FDD6-E40559143548
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 03/09/2018
ms.openlocfilehash: 9175996f516a980d915d1501b4b18ea23ec86cef
ms.sourcegitcommit: 51c274f37369d8965b68ff587e1c2d9865f85da7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39353580"
---
# <a name="using-native-libraries"></a>Uso di librerie Native

Xamarin. Android supporta l'uso di librerie native tramite il meccanismo standard di PInvoke. È anche possibile raggruppare le librerie native aggiuntive che non fanno parte del sistema operativo nel file con estensione apk.

Per distribuire una libreria nativa con un'applicazione xamarin. Android, aggiungere la libreria binaria al progetto e impostare relativi **Build Action** al **AndroidNativeLibrary**.

Per distribuire le librerie native con un progetto di libreria xamarin. Android, aggiungere la libreria binaria al progetto e impostare relativi **Build Action** al **EmbeddedNativeLibrary**.

Si noti che poiché Android supporta più interfacce binarie dell'applicazione (ABI), xamarin. Android è necessario sapere per quale ABI viene compilata la libreria nativa per.
Esistono due modi per farlo:

1.  Percorso "analisi"
1.  Usando un `AndroidNativeLibrary/Abi` elemento all'interno del file di progetto


Con l'analisi del percorso, il nome della directory padre della libreria nativa viene usato per specificare l'ABI di destinazione della libreria. Di conseguenza, se si aggiungono `lib/armeabi/libfoo.so` al progetto, quindi l'interfaccia ABI viene possibile "individuato" come `armeabi`.

In alternativa, è possibile modificare il file di progetto per specificare in modo esplicito l'interfaccia ABI da usare:

```xml
<ItemGroup>
    <AndroidNativeLibrary Include="path/to/libfoo.so">
        <Abi>armeabi</Abi>
    </AndroidNativeLibrary>
</ItemGroup>
```

Per altre informazioni sull'uso di librerie native, vedere [interoperabilità con le librerie native](http://www.mono-project.com/docs/advanced/pinvoke/).

## <a name="debugging-native-code-with-visual-studio-2017"></a>Debug del codice nativo con Visual Studio 2017

Se si usa *Visual Studio 2017* o versione successiva, non è necessario modificare i file di progetto come descritto in precedenza.
È possibile compilare ed eseguire il debug di C++ all'interno della soluzione xamarin. Android tramite l'aggiunta di un riferimento al progetto per C++ **la libreria condivisa dinamica (Android)** progetto. 

Per eseguire il debug di codice nativo C++ nel progetto, seguire questa procedura:

1. Fare doppio clic sul progetto **delle proprietà** e selezionare il **opzioni Android** pagina.
2. Scorrere verso il basso **opzioni di debug**.
3. Nel **Debugger** dal menu a discesa, seleziona **C++** (anziché il valore predefinito **.Net (Xamarin)**).

Gli sviluppatori di Visual Studio C++ possono vedere le [SanAngeles_NativeDebug](https://developer.xamarin.com/samples/monodroid/SanAngeles_NDK/) di esempio per provare il debug di C++ da Visual Studio 2017 con Xamarin; e fare riferimento al nostro [post di blog](https://blog.xamarin.com/build-and-debug-c-libraries-in-xamarin-android-apps-with-visual-studio-2015/) per altre informazioni.



## <a name="related-links"></a>Collegamenti correlati

- [SanAngeles_NativeDebug (esempio)](https://developer.xamarin.com/samples/monodroid/SanAngeles_NDK/)
- [Lo sviluppo di applicazioni Native di Xamarin. Android](https://blogs.msdn.microsoft.com/vcblog/2015/02/23/developing-xamarin-android-native-applications/)
