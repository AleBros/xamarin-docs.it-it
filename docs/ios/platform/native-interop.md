---
title: Riferimenti alle librerie Native in xamarin. IOS
description: Questo documento illustra come collegare librerie C native in un'applicazione xamarin. IOS. Viene descritto come compilare librerie native di universale e accedere ai metodi di C da C#.
ms.prod: xamarin
ms.assetid: 1DA80280-E78A-EC4B-8673-C249C8425CF5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/28/2016
ms.openlocfilehash: 42cf10e592a37f9a979d05d7784553059923646c
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57667321"
---
# <a name="referencing-native-libraries-in-xamarinios"></a>Riferimenti alle librerie Native in xamarin. IOS

Xamarin. IOS supporta il collegamento con le librerie C native e librerie Objective-C. Questo documento illustra come collegare le librerie C native con il progetto xamarin. IOS. Per informazioni su come eseguire la stessa per le librerie Objective-C, vedere la [Binding Objective-C tipi](~/ios/platform/binding-objective-c/index.md) documento.

<a name="building_native" />

## <a name="building-universal-native-libraries-i386-armv7-and-arm64"></a>Compilazione di librerie Native universale (i386 ARMv7 e ARM64)

È spesso opportuno per compilare le librerie native per ognuna delle piattaforme supportate per lo sviluppo iOS (i386 per il simulatore e ARMv7/ARM64 per i dispositivi stessi). Se è già disponibile un progetto Xcode per la libreria, questa è un'operazione molto semplice per eseguire operazioni.

Per compilare la versione i386 della libreria nativa, eseguire il comando seguente da un terminale:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphonesimulator -arch i386 -configuration Release clean build
```

Ciò comporterà una libreria statica nativa in `MyProject.xcodeproj/build/Release-iphonesimulator/`. Copiare il file di archivio di libreria (libMyLibrary.a) da una posizione sicura per un uso successivo, assegnandogli un nome univoco (o passare) (ad esempio **libMyLibrary i386.a**) in modo che non in conflitto con le versioni arm64 e armv7 della stessa libreria che verranno Creare adesso.

Per compilare la versione ARM64 della libreria nativa, eseguire il comando seguente:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch arm64 -configuration Release clean build
```

Questa volta la libreria nativa compilata si troverà in `MyProject.xcodeproj/build/Release-iphoneos/`. Ancora una volta, copiare (o passare) questo file in una posizione sicura, rinominarlo in qualcosa simile **libMyLibrary arm64.a** in modo che non in conflitto.

A questo punto compilare la versione ARMv7 della libreria:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch armv7 -configuration Release clean build
```

Copiare il file di libreria risultante (o spostare) nello stesso percorso è stato spostato le 2 versioni della libreria, rinominarlo in qualcosa simile **libMyLibrary armv7.a**.

Per rendere un universal binario, è possibile usare il `lipo` strumento come segue:

```bash
lipo -create -output libMyLibrary.a libMyLibrary-i386.a libMyLibrary-arm64.a libMyLibrary-armv7.a
```

Ciò consente di creare `libMyLibrary.a` che sarà una libreria (fat) universale che sarà opportuno usare per tutte le destinazioni di sviluppo di iOS.


### <a name="missing-required-architecture-i386"></a>I386 architettura obbligatorio mancante

Se si riceve un `does not implement methodSignatureForSelector` o `does not implement doesNotRecognizeSelector` messaggio nell'Output della fase di esecuzione quando si tenta di utilizzare una libreria Objective-C nel simulatore, la libreria iOS probabilmente non è stato compilato per l'architettura i386 (vedere il [predefiniti universale Le librerie native](#building_native) sezione precedente).

Per controllare le architetture supportate da una determinata libreria, usare il comando seguente nel terminale:

```bash
lipo -info /full/path/to/libraryname.a
```

In cui `/full/path/to/` è il percorso completo per la libreria consumato e `libraryname.a` è il nome della libreria in questione.

Se si dispone di origine alla libreria, è necessario compilare e aggregarla per l'architettura i386, anche se si desidera testare l'app nel simulatore iOS.

### <a name="linking-your-library"></a>La libreria di collegamento

Qualsiasi libreria di terze parti che è utilizzare deve essere collegato staticamente all'applicazione. 

Se si desidera collegare in modo statico la libreria "libMyLibrary.a" che è stato ottenuto da Internet o build con Xcode, è necessario eseguire alcune operazioni:

-  Rendere la libreria del progetto
-  Configurare xamarin. IOS per collegare la libreria
-  Accedere ai metodi dalla libreria.


Per **portare la libreria nel progetto**, selezionare il progetto da Esplora soluzioni e premere **opzione di comando + + a**. Passare al libMyLibrary.a e aggiungerlo al progetto. Quando richiesto, indicare a Visual Studio per Mac o Visual Studio per copiarla nel progetto. Dopo averlo aggiunto, trovare il libFoo.a nel progetto, fare clic con il pulsante destro su di esso e impostare il **Build Action** al **none**.

Per **xamarin. IOS Configura per collegare la libreria**, in Opzioni progetto per il file eseguibile finale (non la stessa libreria, ma il programma finale) è necessario aggiungere nella **compilazione iOS**dell'argomento aggiuntivo (questi sono parte delle opzioni di progetto) il "-gcc_flags" opzione seguita da una stringa tra virgolette contenente tutte le librerie aggiuntive che sono necessari per il programma, ad esempio:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load ${ProjectDir}/libMyLibrary.a"
```

Nell'esempio precedente verrà creato un collegamento **libMyLibrary.a**

È possibile usare `-gcc_flags` specificare qualsiasi set di argomenti della riga di comando da passare al compilatore GCC utilizzato per eseguire il collegamento finale del file eseguibile. Questa riga di comando, ad esempio, fa riferimento anche a framework CFNetwork:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

Se la libreria nativa contiene codice C++ è anche necessario passare il flag - cxx gli "argomenti aggiuntivi" in modo che xamarin. IOS è in grado di usare il compilatore corretto. Le opzioni riportate sopra sarebbe simile per C++:

```bash
-cxx -gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

<a name="Accessing_C_Methods_from_C#" />

## <a name="accessing-c-methods-from-c35"></a>Accedere ai metodi di C da C&#35;

Sono disponibili due tipi di librerie native in iOS:

-  Librerie condivise che fanno parte del sistema operativo.

-  Librerie statiche che è fornito con l'applicazione.


Per accedere ai metodi definiti in uno di questi, si utilizza [funzionalità di P/Invoke di Mono](https://www.mono-project.com/docs/advanced/pinvoke/) che è la stessa tecnologia che verrebbero utilizzate in .NET, che corrisponde approssimativamente a:

-  Determinare quale funzione C si vuole richiamare
-  Determinare la firma
-  Determinare quale libreria si trova in
-  Scrivere la dichiarazione P/Invoke appropriata


Quando si utilizza P/Invoke è necessario specificare il percorso della libreria che si desidera collegarsi. Quando con iOS librerie condivise, è possibile impostare come hardcoded il percorso o è possibile usare le costanti di praticità che abbiamo definito nel nostro [classe costanti](https://developer.xamarin.com/api/type/Constants/), queste costanti devono coprire le librerie di iOS condivisi.

Ad esempio, se si desidera richiamare il metodo UIRectFrameUsingBlendMode dalla libreria UIKit di Apple che ha la firma in c:

```csharp
void UIRectFrameUsingBlendMode (CGRect rect, CGBlendMode mode);
```

La dichiarazione P/Invoke si presenta come segue:

```csharp
[DllImport (Constants.UIKitLibrary,EntryPoint="UIRectFrameUsingBlendMode")]
public extern static void RectFrameUsingBlendMode (RectangleF rect, CGBlendMode blendMode);
```

Il Constants.UIKitLibrary è semplicemente una costante definita come "/ System/Library/Frameworks/UIKit.framework/UIKit", il punto di ingresso consente di specificare facoltativamente il nome esterno (UIRectFramUsingBlendMode) quando si espongono un nome diverso in C#, il RectFrameUsingBlendMode più breve.

<a name="Accessing_C_Dylibs" />

### <a name="accessing-c-dylibs"></a>L'accesso a Dylibs C

Se si ha l'esigenza di utilizzare un Dylib C nell'applicazione xamarin. IOS, è un bit del programma di installazione aggiuntivi che è necessario prima di chiamare il `DllImport` attributo.

Ad esempio, se è disponibile un' `Animal.dylib` con un `Animal_Version` metodo Ti chiameremo nella nostra applicazione, è necessario informare xamarin. IOS del percorso della libreria prima di provare a usarla.

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

In cui `/full/path/to/` è il percorso completo di Dylib consumata. Con questo codice, è possibile quindi eseguire un collegamento per il `Animal_Version` metodo come indicato di seguito:

```csharp
[DllImport("Animal.dylib", EntryPoint="Animal_Version")]
public static extern double AnimalLibraryVersion();
```

<a name="Static_Libraries" />

### <a name="static-libraries"></a>Librerie statiche

Poiché è possibile utilizzare solo le librerie statiche in iOS, non è nessuna libreria condivisa esterna effettuare il collegamento, in modo che il parametro path nell'attributo DllImport deve usare il nome speciale `__Internal` (notare i caratteri di sottolineatura doppio all'inizio del nome) rispetto a il nome del percorso.

In tal modo DllImport per cercare il simbolo del metodo cui viene fatto riferimento nel programma corrente, anziché tentare di caricarlo da una libreria condivisa.

