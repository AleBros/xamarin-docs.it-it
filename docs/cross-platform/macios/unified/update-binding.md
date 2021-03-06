---
title: Eseguire la migrazione di un binding all'API unificata
description: Questo articolo illustra i passaggi necessari per aggiornare un progetto di binding Xamarin esistente per supportare le API unificate per le applicazioni Xamarin.IOS e Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 5E2A3251-D17F-4F9C-9EA0-6321FEBE8577
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: c8f55dd2d300da80a57c06f15cf185558cfc5e41
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015039"
---
# <a name="migrating-a-binding-to-the-unified-api"></a>Eseguire la migrazione di un binding all'API unificata

_Questo articolo illustra i passaggi necessari per aggiornare un progetto di binding Xamarin esistente per supportare le API unificate per le applicazioni Xamarin.IOS e Xamarin.Mac._

## <a name="overview"></a>Panoramica

A partire dal 1 ° febbraio 2015 Apple richiede che tutti i nuovi invii a iTunes e Mac App Store siano applicazioni a 64 bit. Di conseguenza, tutte le nuove applicazioni Xamarin.iOS o Xamarin.Mac dovranno usare la nuova API unificata anziché le API MonoTouch e MonoMac classiche esistenti per supportare 64 bit.

Inoltre, qualsiasi progetto di binding Xamarin deve supportare anche le nuove API unificate da includere in un progetto Xamarin.iOS o Xamarin.Mac a 64 bit. In questo articolo vengono illustrati i passaggi necessari per aggiornare un progetto di binding esistente per l'utilizzo del API unificata.

## <a name="requirements"></a>Requisiti

Per completare i passaggi illustrati in questo articolo, è necessario quanto segue:

- **Visual Studio per Mac** : la versione più recente di Visual Studio per Mac installata e configurata nel computer di sviluppo.
- **Apple Mac** : è necessario un Mac Apple per compilare progetti di binding per iOS e Mac.

I progetti di binding non sono supportati in Visual Studio in un computer Windows.

## <a name="modify-the-using-statements"></a>Modificare le istruzioni using

Le API unificate rendono più semplice che mai condividere il codice tra Mac e iOS, oltre a consentire il supporto di applicazioni a 32 e 64 bit con lo stesso file binario. Eliminando i prefissi _MonoMac_ e _MonoTouch_ dagli spazi dei nomi, la condivisione più semplice viene eseguita nei progetti di applicazione Xamarin.Mac e Xamarin.iOS.

Di conseguenza, è necessario modificare i contratti di binding (e altri file di `.cs` nel progetto di binding) per rimuovere i prefissi _MonoMac_ e _MonoTouch_ dalle istruzioni `using`.

Si configurano, ad esempio, le istruzioni using seguenti in un contratto di associazione:

```csharp
using System;
using System.Drawing;
using MonoTouch.Foundation;
using MonoTouch.UIKit;
using MonoTouch.ObjCRuntime;
```

Il prefisso `MonoTouch` verrà eliminato con la seguente procedura:

```csharp
using System;
using System.Drawing;
using Foundation;
using UIKit;
using ObjCRuntime;
```

Anche in questo caso, è necessario eseguire questa operazione per qualsiasi file di `.cs` nel progetto di binding. Con questa modifica, il passaggio successivo consiste nell'aggiornare il progetto di binding in modo da usare i nuovi tipi di dati nativi.

Per ulteriori informazioni sulla API unificata, consultare la documentazione di [API unificata](~/cross-platform/macios/unified/index.md) . Per ulteriori informazioni su come supportare le applicazioni di bit 32 e 64 e informazioni sui Framework, vedere la documentazione relativa alle [considerazioni sulla piattaforma di bit 32 e 64](~/cross-platform/macios/32-and-64/index.md) .

## <a name="update-to-native-data-types"></a>Aggiornamento ai tipi di dati nativi

Objective-C esegue il mapping del tipo di dati `NSInteger` a `int32_t` su sistemi a 32 bit e `int64_t` nei sistemi a 64 bit. Per trovare la corrispondenza con questo comportamento, il nuovo API unificata sostituisce gli utilizzi precedenti di `int` (che in .NET è definito come sempre `System.Int32`) a un nuovo tipo di dati: `System.nint`.

Insieme al nuovo tipo di dati `nint`, il API unificata introduce i tipi di `nuint` e `nfloat` per eseguire il mapping anche ai tipi `NSUInteger` e `CGFloat`.

Dato quanto sopra, è necessario esaminare l'API e assicurarsi che qualsiasi istanza di `NSInteger`, `NSUInteger` e `CGFloat` di cui in precedenza è stato eseguito il mapping a `int`, `uint` e `float` venga aggiornata alla nuova `nint`, `nuint` e `nfloat` tipi.

Ad esempio, data una definizione di metodo Objective-C:

```csharp
-(NSInteger) add:(NSInteger)operandUn and:(NSInteger) operandDeux;
```

Se il contratto di associazione precedente aveva la definizione seguente:

```csharp
[Export("add:and:")]
int Add(int operandUn, int operandDeux);
```

Il nuovo binding verrà aggiornato in modo che sia:

```csharp
[Export("add:and:")]
nint Add(nint operandUn, nint operandDeux);
```

Se si esegue il mapping a una libreria di terze parti di una versione più recente rispetto a quella inizialmente collegata, è necessario esaminare i file di intestazione `.h` per la libreria e verificare se sono presenti chiamate esplicite in uscita per `int`, `int32_t``unsigned int`, `uint32_t` o `float` sono stati aggiornati per essere `NSInteger`, `NSUInteger` o `CGFloat`. In tal caso, le stesse modifiche apportate ai `nint`, `nuint` e i tipi di `nfloat` dovranno essere apportate anche ai relativi mapping.

Per ulteriori informazioni su queste modifiche ai tipi di dati, vedere il documento [tipi nativi](~/cross-platform/macios/nativetypes.md) .

## <a name="update-the-coregraphics-types"></a>Aggiornare i tipi CoreGraphics

I tipi di dati Point, Size e Rectangle usati con `CoreGraphics` usano 32 o 64 bit a seconda del dispositivo in cui sono in esecuzione. Quando Xamarin ha originariamente associato le API iOS e Mac, sono state usate strutture di dati esistenti che corrispondono ai tipi di dati in `System.Drawing`, ad esempio`RectangleF`.

A causa dei requisiti per supportare 64 bit e i nuovi tipi di dati nativi, è necessario apportare le seguenti modifiche al codice esistente quando si chiamano `CoreGraphic` metodi:

- **CGRect** : usare `CGRect` anziché `RectangleF` quando si definiscono aree rettangolari a virgola mobile.
- **CGSize** : usare `CGSize` anziché `SizeF` quando si definiscono le dimensioni a virgola mobile (larghezza e altezza).
- **CGPoint** : usare `CGPoint` anziché `PointF` quando si definisce una posizione a virgola mobile (coordinate X e Y).

Dato quanto sopra, sarà necessario esaminare l'API e verificare che qualsiasi istanza di `CGRect`, `CGSize` o `CGPoint` che in precedenza era associata a `RectangleF`, `SizeF` o `PointF` venga modificata nel tipo nativo `CGRect`, `CGSize` o `CGPoint` direttamente.

Ad esempio, dato un inizializzatore Objective-C di:

```csharp
- (id)initWithFrame:(CGRect)frame;
```

Se l'associazione precedente includeva il codice seguente:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (RectangleF frame);

```

Il codice verrà aggiornato per:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);

```

Dopo aver apportato tutte le modifiche al codice, è necessario modificare il progetto di binding o creare un file da associare alle API unificate.

## <a name="modify-the-binding-project"></a>Modificare il progetto di associazione

Come passaggio finale per l'aggiornamento del progetto di binding per l'uso delle API unificate, è necessario modificare il `MakeFile` usato per compilare il progetto o il tipo di progetto Xamarin (se si esegue il binding da Visual Studio per Mac) e impostare _btouch_ per l'associazione le API unificate invece di quelle classiche.

### <a name="updating-a-makefile"></a>Aggiornamento di un MakeFile

Se si usa un makefile per compilare il progetto di binding in un Xamarin.DLL, sarà necessario includere l'opzione della riga di comando `--new-style` e chiamare `btouch-native` invece di `btouch`.

Quindi, data la `MakeFile`seguente:

<!--markdownlint-disable MD010 -->
```makefile
BINDDIR=/src/binding
XBUILD=/Applications/Xcode.app/Contents/Developer/usr/bin/xcodebuild
PROJECT_ROOT=XMBindingLibrarySample
PROJECT=$(PROJECT_ROOT)/XMBindingLibrarySample.xcodeproj
TARGET=XMBindingLibrarySample
BTOUCH=/Developer/MonoTouch/usr/bin/btouch

all: XMBindingLibrary.dll

libXMBindingLibrarySample-i386.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphonesimulator -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphonesimulator/lib$(TARGET).a $@

libXMBindingLibrarySample-arm64.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch arm64 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

libXMBindingLibrarySample-armv7.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch armv7 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

libXMBindingLibrarySampleUniversal.a: libXMBindingLibrarySample-armv7.a libXMBindingLibrarySample-i386.a libXMBindingLibrarySample-arm64.a
    lipo -create -output $@ $^

XMBindingLibrary.dll: AssemblyInfo.cs XMBindingLibrarySample.cs extras.cs libXMBindingLibrarySampleUniversal.a
    $(BTOUCH) -unsafe -out:$@ XMBindingLibrarySample.cs -x=AssemblyInfo.cs -x=extras.cs --link-with=libXMBindingLibrarySampleUniversal.a,libXMBindingLibrarySampleUniversal.a

clean:
    -rm -f *.a *.dll
```
<!--markdownlint-enable MD010 -->

È necessario passare dalla chiamata `btouch` al `btouch-native`, quindi modificare la definizione della macro come indicato di seguito:

```makefile
BTOUCH=/Developer/MonoTouch/usr/bin/btouch-native
```

Si aggiornerà la chiamata a `btouch` e si aggiungerà l'opzione `--new-style` come indicato di seguito:

<!--markdownlint-disable MD010 -->
```makefile
XMBindingLibrary.dll: AssemblyInfo.cs XMBindingLibrarySample.cs extras.cs libXMBindingLibrarySampleUniversal.a
    $(BTOUCH) -unsafe --new-style -out:$@ XMBindingLibrarySample.cs -x=AssemblyInfo.cs -x=extras.cs --link-with=libXMBindingLibrarySampleUniversal.a,libXMBindingLibrarySampleUniversal.a
```
<!--markdownlint-enable MD010 -->

È ora possibile eseguire il `MakeFile` come di consueto per compilare la nuova versione di 64 bit dell'API.

### <a name="updating-a-binding-project-type"></a>Aggiornamento di un tipo di progetto di associazione

Se si usa un modello di progetto di binding Visual Studio per Mac per compilare l'API, sarà necessario eseguire l'aggiornamento alla nuova versione API unificata del modello di progetto di binding. Il modo più semplice per eseguire questa operazione consiste nell'avviare un nuovo progetto di binding di API unificata e copiare tutte le impostazioni e il codice esistenti.

Procedere come descritto di seguito:

1. Avviare Visual Studio per Mac.
2. Seleziona **File** > **nuova** **soluzione > ...**
3. Nella finestra di dialogo nuova soluzione selezionare **ios** > **API unificata** > il **progetto di binding iOS**: 

    [![](update-binding-images/image01new.png "In the New Solution Dialog Box, select iOS / Unified API / iOS Binding Project")](update-binding-images/image01new.png#lightbox)
4. Nella finestra di dialogo "Configura nuovo progetto" immettere un **nome** per il nuovo progetto di binding e fare clic sul pulsante **OK** .
5. Includere la versione a 64 bit della libreria Objective-C per cui si intende creare associazioni.
6. Copiare il codice sorgente dal progetto di associazione API classica bit 32 esistente (ad esempio i file `ApiDefinition.cs` e `StructsAndEnums.cs`).
7. Apportare le modifiche indicate in precedenza ai file del codice sorgente.

Con tutte queste modifiche, è possibile compilare la nuova versione di 64 bit dell'API, come si farebbe con la versione 32 bit.

## <a name="summary"></a>Riepilogo

In questo articolo sono state illustrate le modifiche che devono essere apportate a un progetto di associazione Xamarin esistente per supportare le nuove API unificate e i dispositivi a 64 bit e i passaggi necessari per compilare la nuova versione compatibile con 64 bit di un'API.

## <a name="related-links"></a>Collegamenti correlati

- [Mac e iOS](~/cross-platform/macios/index.md)
- [API unificata](~/cross-platform/macios/nativetypes.md)
- [Considerazioni sulla piattaforma a 32/64 bit](~/cross-platform/macios/32-and-64/index.md)
- [Aggiornamento di app iOS esistenti](~/cross-platform/macios/unified/updating-ios-apps.md)
- [API unificata](~/cross-platform/macios/unified/index.md)
- [BindingSample](https://docs.microsoft.com/samples/xamarin/ios-samples/bindingsample/)
