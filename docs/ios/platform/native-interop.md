---
title: Riferimento alle librerie Native
ms.topic: article
ms.prod: xamarin
ms.assetid: 1DA80280-E78A-EC4B-8673-C249C8425CF5
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 07/28/2016
ms.openlocfilehash: 9299d2b37825298d3defa18a9f5137e11b29f6ce
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="referencing-native-libraries"></a>Riferimento alle librerie Native

Xamarin supporta il collegamento con librerie native di C e librerie Objective-C. Questo documento viene descritto come collegare le librerie C native con il progetto xamarin. IOS. Per informazioni sull'esecuzione lo stesso per le librerie di Objective-C, vedere il nostro [associazione tipi Objective-C](~/ios/platform/binding-objective-c/index.md) documento.

<a name="building_native" />

## <a name="building-universal-native-libraries-i386-armv7-and-arm64"></a>Compilazione di Universal librerie Native (i386 ARMv7 e ARM64)

Spesso è consigliabile per compilare librerie native per ognuna delle piattaforme supportate per lo sviluppo iOS (i386 per il simulatore e ARMv7/ARM64 per i dispositivi stessi). Se hai già un progetto Xcode per la raccolta, questo è molto semplice per eseguire.

Per compilare la versione i386 della libreria nativa, eseguire il comando seguente da un terminale:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphonesimulator -arch i386 -configuration Release clean build
```

Il risultato sarà una libreria statica nativa in `MyProject.xcodeproj/build/Release-iphonesimulator/`. Copiare il file di archivio di libreria (libMyLibrary.a) a un punto sicuro per un uso successivo, assegnargli un nome univoco (o spostare) (ad esempio **libMyLibrary i386.a**) in modo che non essere in conflitto con le versioni arm64 e armv7 della stessa libreria che sarà Compilare quindi.

Per compilare la versione ARM64 della libreria nativa, eseguire il comando seguente:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch arm64 -configuration Release clean build
```

Questa volta la libreria nativa compilata viene collocata nella `MyProject.xcodeproj/build/Release-iphoneos/`. In questo caso, copiare (o spostare) questo file in una posizione sicura, ridenominazione esempio **libMyLibrary arm64.a** in modo che non essere in conflitto.

A questo punto compilare la versione ARMv7 della libreria:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch armv7 -configuration Release clean build
```

Copiare il file di libreria (o spostare) nello stesso percorso è stato spostato i 2 versioni della libreria, ridenominazione esempio **libMyLibrary armv7.a**.

Per rendere un universal binario, è possibile utilizzare il `lipo` strumento come segue:

```bash
lipo -create -output libMyLibrary.a libMyLibrary-i386.a libMyLibrary-arm64.a libMyLibrary-armv7.a
```

Questo crea `libMyLibrary.a` che sarà una libreria (fat) universale che sarà appropriata da utilizzare per tutte le destinazioni di sviluppo iOS.


### <a name="missing-required-architecture-i386"></a>I386 architettura obbligatorio mancante

Se si riceve un `does not implement methodSignatureForSelector` o `does not implement doesNotRecognizeSelector` messaggio nell'Output di Runtime quando si tenta di utilizzare una libreria di Objective-C nel simulatore, la libreria iOS probabilmente non è stata compilata per l'architettura i386 (vedere il [compilazione universale Librerie native](#building_native) sezione precedente).

Per controllare le architetture supportate da una determinata libreria, usare il comando seguente nei servizi Terminal:

```bash
lipo -info /full/path/to/libraryname.a
```

Dove `/full/path/to/` è il percorso completo per la libreria consumato e `libraryname.a` è il nome della libreria in questione.

Se si dispone dell'origine per la libreria, è necessario compilare e aggregare, per l'architettura i386, anche se si desidera testare l'app nel simulatore iOS.

### <a name="linking-your-library"></a>La libreria di collegamento

Qualsiasi libreria di terze parti che usi deve essere collegato staticamente all'applicazione. 

Se si desidera collegare in modo statico la libreria "libMyLibrary.a" che è stato ottenuto da Internet o dalla compilazione con Xcode, è necessario eseguire alcune operazioni:

-  Rendere la libreria del progetto
-  Configurazione di xamarin. IOS per la libreria di collegamento
-  Per accedere ai metodi dalla libreria.


Per **rendere la libreria del progetto**, selezionare il progetto da Esplora soluzioni e premere **comando + opzione +**. Individuare il libMyLibrary.a e aggiungerlo al progetto. Quando richiesto, indicare a Visual Studio per Mac o Visual Studio per copiarlo nel progetto. Dopo averlo aggiunto, trovare il libFoo.a nel progetto, fare clic con il pulsante destro su di esso e impostare il **azione di compilazione** a **Nessuno**.

Per **xamarin Configura per collegare la libreria**, sulle opzioni di progetto per il file eseguibile finale (non la stessa libreria, ma il programma finale) è necessario aggiungere **compilazione iOS**dell'argomento aggiuntivo (si tratta di fa parte delle opzioni di progetto) di "-gcc_flags" opzione seguita da una stringa tra virgolette contenente tutte le librerie aggiuntive sono necessarie per il programma, ad esempio:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load ${ProjectDir}/libMyLibrary.a"
```

L'esempio precedente verrà creato un collegamento **libMyLibrary.a**

È possibile utilizzare `-gcc_flags` per specificare qualsiasi set di argomenti della riga di comando da passare al compilatore GCC utilizzato per eseguire il collegamento dell'eseguibile finale. Questa riga di comando, ad esempio, fa riferimento anche a CFNetwork framework:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

Se la libreria nativa contiene codice C++ è anche necessario passare il flag - cxx gli argomenti"aggiuntive" in modo che xamarin è in grado di usare il compilatore corretto. Le opzioni precedenti sarebbe simile per C++:

```bash
-cxx -gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

<a name="Accessing_C_Methods_from_C#" />

## <a name="accessing-c-methods-from-c35"></a>L'accesso ai metodi di C da C &#35;

Sono disponibili due tipi di librerie native in iOS:

-  Librerie condivise che fanno parte del sistema operativo.

-  Librerie statiche a cui si effettua la spedizione con l'applicazione.


Per accedere ai metodi definiti in uno di questi, si utilizza [funzionalità P/Invoke del Mono](http://www.mono-project.com/Interop_with_Native_Libraries) che è la stessa tecnologia che utilizzerebbe per .NET, è simile a:

-  Determinare quale funzione C da richiamare
-  Determinare la firma
-  Determinare la libreria si trova in
-  Scrivere la dichiarazione P/Invoke appropriata


Quando si utilizza P/Invoke è necessario specificare il percorso della libreria che si desidera collegarsi. Quando usare iOS librerie condivise, è possibile impostare come hardcoded il percorso o è possibile utilizzare le costanti di praticità che sono state definite in questo [classe costanti](https://developer.xamarin.com/api/type/Constants/), queste costanti dovrebbero coprire le librerie condivise di iOS.

Ad esempio, se si desiderava richiamare il metodo UIRectFrameUsingBlendMode dalla libreria di UIKit di Apple con questa firma in c:

```csharp
void UIRectFrameUsingBlendMode (CGRect rect, CGBlendMode mode);
```

La dichiarazione P/Invoke è analogo al seguente:

```csharp
[DllImport (Constants.UIKitLibrary,EntryPoint="UIRectFrameUsingBlendMode")]
public extern static void RectFrameUsingBlendMode (RectangleF rect, CGBlendMode blendMode);
```

Il Constants.UIKitLibrary è semplicemente una costante definita come "/ System/Library/Frameworks/UIKit.framework/UIKit", il punto di ingresso consente di specificare facoltativamente il nome esterno (UIRectFramUsingBlendMode) l'esposizione di un nome diverso in c#, il più breve RectFrameUsingBlendMode.

<a name="Accessing_C_Dylibs" />

### <a name="accessing-c-dylibs"></a>L'accesso a Dylib C

Se si ha l'esigenza di utilizzare un Dylib C xamarin nell'applicazione in uso, è un po' di configurazione aggiuntivi che è necessario prima di chiamare il `DllImport` attributo.

Ad esempio, se si dispone di un `Animal.dylib` con un `Animal_Version` metodo Ti chiameremo nell'applicazione, è necessario informare xamarin della posizione della raccolta prima di tentare di utilizzarlo.

A tale scopo, modificare il `Main.CS` file e renderlo simile al seguente:

```csharp
static void Main (string[] args)
{
    // Load Dylib
    MonoTouch.ObjCRuntime.Dlfcn.dlopen ("/full/path/to/Animal.dylib", 0);

    // Start application
    UIApplication.Main (args, null, "AppDelegate");
}
```

Dove `/full/path/to/` è il percorso completo di Dylib viene utilizzato. Con questo codice, è possibile quindi collegare il `Animal_Version` metodo come indicato di seguito:

```csharp
[DllImport("Animal.dylib", EntryPoint="Animal_Version")]
public static extern double AnimalLibraryVersion();
```

<a name="Static_Libraries" />

### <a name="static-libraries"></a>Librerie statiche

Poiché è possibile utilizzare solo le librerie statiche in iOS, non è presente alcuna libreria condivisa esterna per eseguire il collegamento è il parametro path nell'attributo DllImport deve utilizzare il nome speciale `__Internal` (nota di caratteri di sottolineatura doppio all'inizio del nome) rispetto a il nome del percorso.

In questo modo DllImport per cercare il simbolo del metodo cui si fa riferimento nel programma corrente, anziché tentare di caricarlo da una libreria condivisa.

