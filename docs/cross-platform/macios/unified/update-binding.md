---
title: La migrazione di un'associazione per l'API unificata
description: In questo articolo vengono illustrati i passaggi necessari per aggiornare un progetto di associazione Xamarin esistente per supportare le API unificata per le applicazioni di xamarin. IOS e Xamarin.Mac.
ms.topic: article
ms.prod: xamarin
ms.assetid: 5E2A3251-D17F-4F9C-9EA0-6321FEBE8577
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 2a04dc047674b67b8f21571ed9e7890ddf773f64
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="migrating-a-binding-to-the-unified-api"></a>La migrazione di un'associazione per l'API unificata

_In questo articolo vengono illustrati i passaggi necessari per aggiornare un progetto di associazione Xamarin esistente per supportare le API unificata per le applicazioni di xamarin. IOS e Xamarin.Mac._

## <a name="overview"></a>Panoramica

A partire dal 1 ° febbraio 2015 Apple richiede che tutti gli invii di nuovo a iTunes e Mac App Store devono essere applicazioni a 64 bit. Di conseguenza, qualsiasi nuova applicazione di xamarin. IOS o Xamarin.Mac sarà necessario utilizzare la nuova API unificata anziché MonoTouch classico e MonoMac APIs esistente per il supporto a 64 bit.

Inoltre, qualsiasi progetto Binding Xamarin anche deve supportare le nuove API unificata da includere nel progetto Xamarin.Mac o a 64 bit di xamarin. IOS. In questo articolo illustra i passaggi necessari per aggiornare un progetto di associazione esistente per utilizzare l'API unificata.

## <a name="requirements"></a>Requisiti

È necessario completare i passaggi illustrati in questo articolo:

 -  **Visual Studio per Mac** -la versione più recente di Visual Studio per Mac installato e configurato nel computer di sviluppo.
 -  **Apple Mac** : un'Apple mac è necessario per compilare progetti di associazione per iOS e Mac.

Progetti di associazione non sono supportati in Visual studio in un computer Windows.

## <a name="modify-the-using-statements"></a>Modificare le istruzioni Using

Le API unificata rende più semplice che mai per condividere il codice tra Mac e iOS nonché in modo da supportare 32 e 64 bit le applicazioni con lo stesso binario. Rimuovendo il _MonoMac_ e _MonoTouch_ i prefissi degli spazi dei nomi, più semplice la condivisione avviene per i progetti di applicazione Xamarin.Mac e xamarin. IOS.

Di conseguenza sarà necessario modificare i contratti di associazione (e altri `.cs` file nel progetto associazione) per rimuovere il _MonoMac_ e _MonoTouch_ prefissi da nostri `using` istruzioni.

Ad esempio, data la seguente utilizzando le istruzioni in un contratto di associazione:

```csharp
using System;
using System.Drawing;
using MonoTouch.Foundation;
using MonoTouch.UIKit;
using MonoTouch.ObjCRuntime;
```

È necessario rimuovere il `MonoTouch` prefisso risultante come indicato di seguito:

```csharp
using System;
using System.Drawing;
using Foundation;
using UIKit;
using ObjCRuntime;
```

Nuovamente, sarà necessario eseguire questa operazione per qualsiasi `.cs` file nel progetto dell'associazione. Con questa modifica sul posto, il passaggio successivo consiste nell'aggiornare il progetto di associazione per l'utilizzo di nuovi tipi di dati nativo.

Per ulteriori informazioni sull'API unificata, vedere il [Unified API](~/cross-platform/macios/unified/index.md) documentazione. Per ulteriori informazioni sul supporto di 32 e 64 bit applicazioni e informazioni sui Framework per vedere il [32 e 64 bit considerazioni relative alla piattaforma](~/cross-platform/macios/32-and-64/index.md) documentazione.

## <a name="update-to-native-data-types"></a>Aggiornamento di tipi di dati nativi

Objective-C esegue il mapping di `NSInteger` tipo di dati `int32_t` nei sistemi a 32 bit e a `int64_t` nei sistemi a 64 bit. Per trovare questo comportamento, la nuova API unificata sostituisce utilizzi precedenti del `int` (che in .NET è definito come costante `System.Int32`) in un nuovo tipo di dati: `System.nint`.

Con il nuovo `nint` tipo di dati, l'API unificata introduce il `nuint` e `nfloat` tipi, per il mapping al `NSUInteger` e `CGFloat` nonché i tipi.

Considerato quanto illustrato sopra, è necessario rivedere API e garantire che qualsiasi istanza di `NSInteger`, `NSUInteger` e `CGFloat` che in precedenza eseguito il mapping a `int`, `uint` e `float` vengono aggiornati al nuovo `nint`, `nuint` e `nfloat` tipi.

Ad esempio, poiché una definizione di metodo Objective-C di:

```csharp
-(NSInteger) add:(NSInteger)operandUn and:(NSInteger) operandDeux;
```

Se il contratto di associazione precedenti la definizione seguente:

```csharp
[Export("add:and:")]
int Add(int operandUn, int operandDeux);
```

Verranno aggiornate la nuova associazione da:

```csharp
[Export("add:and:")]
nint Add(nint operandUn, nint operandDeux);
```
Se si sta eseguendo il mapping a una raccolta di parti 3rd versione più recente rispetto a ciò che si fosse stata inizialmente collegato a, è necessario esaminare il `.h` file di intestazione per la libreria e se le chiamate esplicite, in fase di chiusura a `int`, `int32_t`, `unsigned int`, `uint32_t` o `float` sono stati aggiornati per essere un `NSInteger`, `NSUInteger` o `CGFloat`. In questo caso, le stesse modifiche per il `nint`, `nuint` e `nfloat` tipi dovranno essere apportate anche i relativi mapping.

Per ulteriori informazioni su queste modifiche di tipo di dati, vedere il [tipi nativi](~/cross-platform/macios/nativetypes.md) documento.

## <a name="update-the-coregraphics-types"></a>Aggiornare i tipi di CoreGraphics

I tipi di dati di punto, le dimensioni e rettangolo utilizzati con `CoreGraphics` utilizzare 32 o 64 bit, a seconda del dispositivo sono in esecuzione in. Quando Xamarin associato originariamente la iOS e Mac API utilizzassimo strutture di dati esistenti che si sono verificati a corrispondono ai tipi di dati in `System.Drawing` (`RectangleF` , ad esempio).

A causa dei requisiti per il supporto a 64 bit e i nuovi tipi di dati nativi, le modifiche seguenti dovranno essere apportate al codice esistente quando si chiama `CoreGraphic` metodi:

- **CGRect** -utilizzo `CGRect` anziché `RectangleF` quando la definizione mobile punto aree rettangolari.
- **CGSize** -utilizzo `CGSize` anziché `SizeF` durante la definizione di dimensioni a virgola mobile (larghezza e altezza).
- **CGPoint** -utilizzo `CGPoint` anziché `PointF` quando la definizione mobile punto di posizione (coordinate X e Y).

Considerato quanto illustrato sopra, è necessario esaminare l'API e garantire che qualsiasi istanza di `CGRect`, `CGSize` o `CGPoint` che in precedenza è stato associato a `RectangleF`, `SizeF` o `PointF` essere modificato nel tipo nativo `CGRect`, `CGSize` o `CGPoint` direttamente.

Ad esempio, dato un inizializzatore di Objective-C di:

```csharp
- (id)initWithFrame:(CGRect)frame;
```

Se l'associazione precedente è incluso il codice seguente:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (RectangleF frame);

```

Si aggiorna il codice:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);

```

Con tutte le modifiche al codice approntate, è necessario modificare il progetto di associazione o verificare il file a cui associare le API unificata.

## <a name="modify-the-binding-project"></a>Modificare il progetto di associazione

Come passaggio finale per l'aggiornamento del progetto di associazione per utilizzare le API unificata, è necessario modificare il `MakeFile` utilizzati per compilare il progetto o il tipo di progetto Xamarin (se viene eseguita l'associazione da Visual Studio per Mac) e indicare _btouch_  a cui associare le API unificata anziché quelli classica.


### <a name="updating-a-makefile"></a>L'aggiornamento di un MakeFile

Se si sta usando un makefile per compilare il progetto di associazione in un Xamarin. DLL, è necessario includere il `--new-style` opzione della riga di comando e chiamare `btouch-native` anziché `btouch`.

Pertanto, data la seguente `MakeFile`:

```csharp
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

È necessario passare dalla chiamata `btouch` a `btouch-native`, pertanto è necessario regolare la definizione di macro come segue:

```csharp
BTOUCH=/Developer/MonoTouch/usr/bin/btouch-native
```

Verranno aggiornate la chiamata a `btouch` e aggiungere il `--new-style` opzione come indicato di seguito:

```csharp
XMBindingLibrary.dll: AssemblyInfo.cs XMBindingLibrarySample.cs extras.cs libXMBindingLibrarySampleUniversal.a
    $(BTOUCH) -unsafe --new-style -out:$@ XMBindingLibrarySample.cs -x=AssemblyInfo.cs -x=extras.cs --link-with=libXMBindingLibrarySampleUniversal.a,libXMBindingLibrarySampleUniversal.a
```

È ora possibile eseguire il nostro `MakeFile` come di consueto per compilare la nuova versione a 64 bit dell'API.

### <a name="updating-a-binding-project-type"></a>L'aggiornamento di un tipo di progetto di associazione

Se si sta usando Visual Studio per Mac associazione del modello di progetto per compilare l'API, è necessario aggiornare alla nuova versione dell'API unificata l'associazione del modello di progetto. Il modo più semplice per eseguire questa operazione è avviare un nuovo progetto di associazione API unificata e copia su tutte le impostazioni e il codice esistente.

Seguire questa procedura:

1. Avviare Visual Studio per Mac.
2. Selezionare **File** > **nuova** > **soluzione...**
3. Nella nuova finestra di dialogo soluzione, selezionare **iOS** > **Unified API** > **iOS progetto Binding**: 

    [![](update-binding-images/image01new.png "Nella nuova soluzione finestra di dialogo, selezionare iOS / Unified API / progetto di associazione di iOS")](update-binding-images/image01new.png#lightbox)
4. Nella finestra di dialogo 'Configurare il nuovo progetto' Immettere un **nome** per il nuovo progetto di associazione e scegliere il **OK** pulsante.
5. Includere la versione a 64 bit della libreria Objective-C come la creazione di associazioni per cui si trovano.
6. Copiare il codice sorgente dal progetto associazione API classica a 32 bit esistente (ad esempio il `ApiDefinition.cs` e `StructsAndEnums.cs` file).
7. Apportare le modifiche indicate sopra per i file del codice sorgente.

Con tutte le modifiche sul posto, è possibile compilare la nuova versione a 64 bit dell'API di come si farebbe con la versione a 32 bit.

## <a name="summary"></a>Riepilogo

In questo articolo è stato illustrato le modifiche che devono essere apportate a un progetto di associazione Xamarin esistente per supportare le nuove API unificata e i dispositivi a 64 bit e i passaggi necessari per compilare la nuova versione compatibile a 64 bit di un'API.



## <a name="related-links"></a>Collegamenti correlati

- [Mac e iOS](~/cross-platform/macios/index.md)
- [API unificata](~/cross-platform/macios/nativetypes.md)
- [Considerazioni relative alla piattaforma a 32/64 bit](~/cross-platform/macios/32-and-64/index.md)
- [L'aggiornamento delle App iOS esistente](~/cross-platform/macios/unified/updating-ios-apps.md)
- [API unificata](~/cross-platform/macios/unified/index.md)
- [BindingSample](https://developer.xamarin.com/samples/monotouch/BindingSample/)
