---
title: Riferimento a librerie native in Xamarin.iOS
description: Questo documento illustra come collegare le librerie C native in un'applicazione Xamarin.iOS. Viene descritto come compilare librerie native universali e accedere ai metodi C da C#.
ms.prod: xamarin
ms.assetid: 1DA80280-E78A-EC4B-8673-C249C8425CF5
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 07/28/2016
ms.openlocfilehash: c8b882430d5d7d02e444acd00cfdacfc7b29a42b
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031632"
---
# <a name="referencing-native-libraries-in-xamarinios"></a>Riferimento a librerie native in Xamarin.iOS

Xamarin.iOS supporta il collegamento con le librerie C native e le librerie Objective-C. Questo documento illustra come collegare le librerie C native al progetto Xamarin.iOS. Per informazioni su come eseguire la stessa operazione per le librerie Objective-C, vedere il documento relativo all' [associazione di tipi Objective-c](~/ios/platform/binding-objective-c/index.md) .

<a name="building_native" />

## <a name="building-universal-native-libraries-i386-armv7-and-arm64"></a>Compilazione di librerie Universal native (i386, ARMv7 e ARM64)

Spesso è consigliabile compilare le librerie native per ogni piattaforma supportata per lo sviluppo di iOS (i386 per il simulatore e ARMv7/ARM64 per i dispositivi stessi). Se è già stato ottenuto un progetto Xcode per la libreria, è molto semplice.

Per compilare la versione i386 della libreria nativa, eseguire il comando seguente da un terminale:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphonesimulator -arch i386 -configuration Release clean build
```

Verrà generata una libreria statica nativa in `MyProject.xcodeproj/build/Release-iphonesimulator/`. Copiare (o spostare) il file di archivio della libreria (libMyLibrary. a) in un luogo sicuro per un uso successivo, assegnargli un nome univoco, ad esempio **libMyLibrary-i386. a**, in modo che non si scontri con le versioni arm64 e ARMv7 della stessa libreria che verrà compilata successivamente.

Per compilare la versione ARM64 della libreria nativa, eseguire il comando seguente:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch arm64 -configuration Release clean build
```

Questa volta la libreria nativa predefinita si troverà in `MyProject.xcodeproj/build/Release-iphoneos/`. Ancora una volta, copiare (o spostare) questo file in un percorso sicuro, rinominando il file come **libMyLibrary-arm64. a** in modo che non si scontri.

A questo punto, compilare la versione ARMv7 della libreria:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch armv7 -configuration Release clean build
```

Copiare (o spostare) il file di libreria risultante nello stesso percorso in cui sono state spostate le altre 2 versioni della libreria, rinominando il file in un elemento come **libMyLibrary-ARMv7. a**.

Per creare un file binario universale, è possibile usare lo strumento `lipo` come segue:

```bash
lipo -create -output libMyLibrary.a libMyLibrary-i386.a libMyLibrary-arm64.a libMyLibrary-armv7.a
```

Questa operazione crea `libMyLibrary.a` che sarà una libreria universale (FAT) che sarà adatta per l'uso per tutte le destinazioni di sviluppo iOS.

### <a name="missing-required-architecture-i386"></a>Manca l'architettura necessaria i386

Se si sta ricevendo un messaggio `does not implement methodSignatureForSelector` o `does not implement doesNotRecognizeSelector` nell'output del runtime quando si tenta di usare una libreria Objective-C nel simulatore iOS, la libreria non è probabilmente stata compilata per l'architettura i386 (vedere [compilazione di librerie native native](#building_native) sezione precedente).

Per controllare le architetture supportate da una libreria specifica, usare il comando seguente nel terminale:

```bash
lipo -info /full/path/to/libraryname.a
```

Dove `/full/path/to/` è il percorso completo della libreria utilizzata e `libraryname.a` è il nome della libreria in questione.

Se si dispone dell'origine per la libreria, sarà necessario compilarla e AGGREGARLA anche per l'architettura i386, se si vuole testare l'app nel simulatore iOS.

### <a name="linking-your-library"></a>Collegamento della libreria

Qualsiasi libreria di terze parti che si utilizza deve essere collegata in modo statico all'applicazione. 

Se si desidera collegare in modo statico la libreria "libMyLibrary. a" ottenuta da Internet o compilata con Xcode, è necessario eseguire alcune operazioni:

- Portare la libreria nel progetto
- Configurare Xamarin.iOS per collegare la libreria
- Accedere ai metodi dalla libreria.

Per **portare la libreria nel progetto**, selezionare il progetto in Esplora soluzioni e premere **comando + opzione + a**. Passare a libMyLibrary. a e aggiungerlo al progetto. Quando richiesto, indicare a Visual Studio per Mac o a Visual Studio di copiarlo nel progetto. Dopo averlo aggiunto, trovare libFoo. a nel progetto, fare clic con il pulsante destro del mouse su di esso e impostare l' **azione di compilazione** su **nessuno**.

Per **configurare Xamarin.iOS per collegare la libreria**, nelle opzioni del progetto per l'eseguibile finale (non la libreria stessa, ma il programma finale), è necessario aggiungere l'argomento aggiuntivo della **compilazione iOS**(queste sono parte delle opzioni del progetto) "-gcc_flags" opzione seguita da una stringa racchiusa tra virgolette contenente tutte le librerie aggiuntive necessarie per il programma, ad esempio:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load ${ProjectDir}/libMyLibrary.a"
```

Nell'esempio precedente viene collegato **libMyLibrary. a**

È possibile usare `-gcc_flags` per specificare qualsiasi set di argomenti della riga di comando da passare al compilatore GCC usato per eseguire il collegamento finale del file eseguibile. Questa riga di comando, ad esempio, fa riferimento anche a CFNetwork Framework:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

Se la libreria nativa contiene C++ codice, è necessario passare anche il flag-cxx in "argomenti aggiuntivi" in modo che Xamarin.iOS sappia di usare il compilatore corretto. Per C++ le opzioni precedenti avrà un aspetto simile al seguente:

```bash
-cxx -gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

<a name="Accessing_C_Methods_from_C#" />

## <a name="accessing-c-methods-from-c35"></a>Accesso ai metodi C da C&#35;

Sono disponibili due tipi di librerie native in iOS:

- Librerie condivise che fanno parte del sistema operativo.

- Librerie statiche fornite con l'applicazione.

Per accedere ai metodi definiti in uno di questi casi, si usa la [funzionalità P/Invoke di mono](https://www.mono-project.com/docs/advanced/pinvoke/) , che è la stessa tecnologia che si utilizzerebbe in .NET, che è approssimativamente:

- Determinare quale funzione C si vuole richiamare
- Determinare la firma
- Determinare la libreria in cui risiede
- Scrivere la dichiarazione P/Invoke appropriata

Quando si usa P/Invoke è necessario specificare il percorso della libreria con cui si esegue il collegamento. Quando si usano le librerie condivise iOS, è possibile impostare come hardcoded il percorso oppure è possibile usare le costanti di praticità definite nella `Constants`, queste costanti dovrebbero coprire le librerie condivise iOS.

Ad esempio, se si desidera richiamare il metodo UIRectFrameUsingBlendMode dalla libreria UIKit di Apple con questa firma in C:

```csharp
void UIRectFrameUsingBlendMode (CGRect rect, CGBlendMode mode);
```

La dichiarazione P/Invoke avrà un aspetto simile al seguente:

```csharp
[DllImport (Constants.UIKitLibrary,EntryPoint="UIRectFrameUsingBlendMode")]
public extern static void RectFrameUsingBlendMode (RectangleF rect, CGBlendMode blendMode);
```

Constants. UIKitLibrary è semplicemente una costante definita come "/System/Library/Frameworks/UIKit.framework/UIKit", il EntryPoint consente di specificare facoltativamente il nome esterno (UIRectFramUsingBlendMode) esponendo un nome diverso in C#, il RectFrameUsingBlendMode più breve.

<a name="Accessing_C_Dylibs" />

### <a name="accessing-c-dylibs"></a>Accesso a C dylib

Se è necessario utilizzare un dylib C nell'applicazione Xamarin.iOS, è necessario eseguire una configurazione aggiuntiva prima di chiamare l'attributo `DllImport`.

Se, ad esempio, si dispone di un `Animal.dylib` con un metodo `Animal_Version` che verrà chiamato nell'applicazione, è necessario informare Xamarin.iOS del percorso della libreria prima di provare a utilizzarlo.

A tale scopo, modificare il file `Main.CS` e renderlo simile al seguente:

```csharp
static void Main (string[] args)
{
    // Load Dylib
    MonoTouch.ObjCRuntime.Dlfcn.dlopen ("/full/path/to/Animal.dylib", 0);

    // Start application
    UIApplication.Main (args, null, "AppDelegate");
}
```

Dove `/full/path/to/` è il percorso completo del dylib utilizzato. Con questo codice, è possibile collegarsi al metodo `Animal_Version` come indicato di seguito:

```csharp
[DllImport("Animal.dylib", EntryPoint="Animal_Version")]
public static extern double AnimalLibraryVersion();
```

<a name="Static_Libraries" />

### <a name="static-libraries"></a>Librerie statiche

Poiché è possibile usare solo librerie statiche in iOS, non esiste alcuna libreria condivisa esterna con cui eseguire il collegamento, quindi il parametro Path nell'attributo DllImport deve usare il nome speciale `__Internal` (si notino i due caratteri di sottolineatura all'inizio del nome) invece di nome del percorso.

Questa operazione impone a DllImport di cercare il simbolo del metodo a cui si fa riferimento nel programma corrente, anziché tentare di caricarlo da una libreria condivisa.
