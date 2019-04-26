---
title: Eseguire la migrazione di un binding all'API unificata
description: Questo articolo illustra i passaggi necessari per aggiornare un progetto esistente di Binding Xamarin per supportare le API unificata per le applicazioni xamarin. IOS e xamarin. Mac.
ms.prod: xamarin
ms.assetid: 5E2A3251-D17F-4F9C-9EA0-6321FEBE8577
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 2d57f27bf0d3aaa2a7ba14f23481a8f2bb2d87f2
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61261154"
---
# <a name="migrating-a-binding-to-the-unified-api"></a>Eseguire la migrazione di un binding all'API unificata

_Questo articolo illustra i passaggi necessari per aggiornare un progetto esistente di Binding Xamarin per supportare le API unificata per le applicazioni xamarin. IOS e xamarin. Mac._

## <a name="overview"></a>Panoramica

A partire dall'1 febbraio 2015 Apple richiede che tutti gli invii di nuovo a iTunes e Store di App Mac devono essere applicazioni a 64 bit. Di conseguenza, qualsiasi nuova applicazione xamarin. IOS o xamarin. Mac dovrà usare la nuova API unificata anziché MonoTouch classica esistente e APIs MonoMac per il supporto a 64 bit.

Inoltre, qualsiasi progetto di Binding Xamarin deve supportare anche le nuove API unificata da includere nel progetto xamarin. Mac o xamarin. IOS a 64 bit. Questo articolo illustra i passaggi necessari per aggiornare un progetto di associazione esistente per usare l'API unificata.

## <a name="requirements"></a>Requisiti

Di seguito è necessario per completare la procedura descritta in questo articolo:

 -  **Visual Studio per Mac** -la versione più recente di Visual Studio per Mac è installato e configurato nel computer di sviluppo.
 -  **Apple Mac** : un'Apple mac è necessario per compilare i progetti di Binding per iOS e Mac.

Progetti di binding non sono supportati in Visual studio in un computer Windows.

## <a name="modify-the-using-statements"></a>Modificare le istruzioni Using

Le API unificata rende più facile che mai per condividere codice tra Mac e iOS nonché in modo da supportare 32 e 64 bit delle applicazioni con lo stesso binario. Con il rilascio di _MonoMac_ e _MonoTouch_ i prefissi degli spazi dei nomi, più semplice la condivisione avviene tra i progetti di applicazione xamarin. Mac e xamarin. IOS.

Di conseguenza è necessario modificare uno dei nostri contratti associazione (e altri `.cs` file nel progetto di associazione) per rimuovere il _MonoMac_ e _MonoTouch_ prefissi dal nostro `using` istruzioni.

Ad esempio, data la seguente utilizzando le istruzioni in un contratto di associazione:

```csharp
using System;
using System.Drawing;
using MonoTouch.Foundation;
using MonoTouch.UIKit;
using MonoTouch.ObjCRuntime;
```

È necessario rimuovere il `MonoTouch` prefisso risultante nelle risorse seguenti:

```csharp
using System;
using System.Drawing;
using Foundation;
using UIKit;
using ObjCRuntime;
```

Anche in questo caso, è necessario eseguire questa operazione per qualsiasi `.cs` file nel progetto di associazione. Con questa modifica, il passaggio successivo consiste nell'aggiornare il progetto di associazione per l'uso di nuovi tipi di dati nativo.

Per altre informazioni sull'API unificata, vedere la [API unificata](~/cross-platform/macios/unified/index.md) documentazione. Per altre informazioni sul supporto di 32 e 64 bit delle applicazioni e informazioni sui Framework per vedere le [32 e 64 bit considerazioni sulle piattaforme](~/cross-platform/macios/32-and-64/index.md) documentazione.

## <a name="update-to-native-data-types"></a>Aggiornamento di tipi di dati nativi

Objective-C esegue il mapping di `NSInteger` tipo di dati `int32_t` nei sistemi a 32 bit e a `int64_t` nei sistemi a 64 bit. Per trovare questo comportamento, la nuova API unificata sostituisce l'utilizzi precedenti del `int` (che in .NET viene definito come devono essere sempre `System.Int32`) in un nuovo tipo di dati: `System.nint`.

Con la nuova `nint` introduce l'API unificata di tipo di dati, il `nuint` e `nfloat` tipi, per il mapping per il `NSUInteger` e `CGFloat` anche tipi di.

Considerato quanto illustrato sopra, è necessario esaminare l'API e garantire che qualsiasi istanza di `NSInteger`, `NSUInteger` e `CGFloat` che è stata mappata al `int`, `uint` e `float` aggiornato al nuovo `nint`, `nuint` e `nfloat` tipi.

Ad esempio, data una definizione di metodo Objective-C di:

```csharp
-(NSInteger) add:(NSInteger)operandUn and:(NSInteger) operandDeux;
```

Se il contratto di associazione precedenti era la definizione seguente:

```csharp
[Export("add:and:")]
int Add(int operandUn, int operandDeux);
```

Verranno aggiornate la nuova associazione da:

```csharp
[Export("add:and:")]
nint Add(nint operandUn, nint operandDeux);
```
Se viene mappato a una libreria di terze parti 3rd versione più recente rispetto a ciò che abbiamo fosse stato eseguito inizialmente collegato, è necessario esaminare i `.h` file di intestazione per la libreria e considerare se eventuali chiamate esplicite in fase di chiusura per `int`, `int32_t`, `unsigned int`, `uint32_t` o `float` sono stati aggiornati per essere un `NSInteger`, `NSUInteger` o un `CGFloat`. In questo caso, le stesse modifiche per il `nint`, `nuint` e `nfloat` tipi dovranno essere apportate anche i relativi mapping.

Per altre informazioni su queste modifiche ai tipi di dati, vedere la [i tipi nativi](~/cross-platform/macios/nativetypes.md) documento.

## <a name="update-the-coregraphics-types"></a>Aggiornare i tipi di CoreGraphics

I tipi di dati punto, le dimensioni e rettangolo utilizzati con `CoreGraphics` utilizzare 32 o 64 bit a seconda del dispositivo di cui vengono eseguiti. Quando Xamarin associati originariamente le API di Mac e iOS è stato usato di strutture di dati esistenti che si sono verificati in modo che corrisponda i tipi di dati in `System.Drawing` (`RectangleF` , ad esempio).

A causa dei requisiti per il supporto a 64 bit e i nuovi tipi di dati nativi, le modifiche seguenti dovranno essere apportate al codice esistente quando si chiama `CoreGraphic` metodi:

- **CGRect** -utilizzare `CGRect` invece di `RectangleF` quando la definizione di Mobile scegliere aree rettangolari.
- **CGSize** -utilizzare `CGSize` invece di `SizeF` quando si definiscono le dimensioni in punti a virgola mobile (larghezza e altezza).
- **CGPoint** -utilizzare `CGPoint` invece di `PointF` quando la definizione di Mobile punto percorso (coordinate X e Y).

Considerato quanto illustrato sopra, è necessario esaminare l'API e garantire che qualsiasi istanza di `CGRect`, `CGSize` oppure `CGPoint` che è stato precedentemente associato a `RectangleF`, `SizeF` oppure `PointF` essere modificato nel tipo nativo `CGRect`, `CGSize` o `CGPoint` direttamente.

Ad esempio, dato un inizializzatore di Objective-C di:

```csharp
- (id)initWithFrame:(CGRect)frame;
```

Se l'associazione precedente è incluso il codice seguente:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (RectangleF frame);

```

Verranno aggiornate nel codice:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);

```

Con tutte le modifiche al codice ora posto, è necessario modificare il progetto di associazione o rendere i file da associare usando le API unificata.

## <a name="modify-the-binding-project"></a>Modificare il progetto di associazione

Come passaggio finale per aggiornare il progetto di associazione per usare le API unificata, è necessario modificare il `MakeFile` che viene usato per compilare il progetto o il tipo di progetto Xamarin (se viene eseguita l'associazione dall'interno di Visual Studio per Mac) e indicare a _btouch_  associare usando le API unificata anziché quelli classica.


### <a name="updating-a-makefile"></a>L'aggiornamento di un MakeFile

Se si sta usando un makefile per compilare il progetto di associazione in un Xamarin. DLL, è necessario includere il `--new-style` opzione della riga di comando e chiamare `btouch-native` invece di `btouch`.

Quindi, data la seguente `MakeFile`:

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

È necessario passare dalla chiamata `btouch` a `btouch-native`, pertanto è necessario regolare la definizione di macro come indicato di seguito:

```csharp
BTOUCH=/Developer/MonoTouch/usr/bin/btouch-native
```

Verranno aggiornate la chiamata a `btouch` e aggiungere il `--new-style` opzione come indicato di seguito:

```csharp
XMBindingLibrary.dll: AssemblyInfo.cs XMBindingLibrarySample.cs extras.cs libXMBindingLibrarySampleUniversal.a
    $(BTOUCH) -unsafe --new-style -out:$@ XMBindingLibrarySample.cs -x=AssemblyInfo.cs -x=extras.cs --link-with=libXMBindingLibrarySampleUniversal.a,libXMBindingLibrarySampleUniversal.a
```

Ora è possibile eseguire il `MakeFile` come di consueto per compilare la nuova versione a 64 bit dell'API.

### <a name="updating-a-binding-project-type"></a>L'aggiornamento di un tipo di progetto di associazione

Se si sta usando Visual Studio per Mac di associazione del modello di progetto per compilare l'API, è necessario eseguire l'aggiornamento alla nuova versione dell'API unificata del modello di progetto di associazione. Il modo più semplice per eseguire questa operazione è avviare un nuovo progetto di associazione API unificata e copia su tutte le impostazioni e il codice esistente.

Seguire questa procedura:

1. Avviare Visual Studio per Mac.
2. Selezionare **File** > **nuova** > **soluzione...**
3. Nella finestra di dialogo nuova soluzione, selezionare **iOS** > **API unificata** > **associazione progetto iOS**: 

    [![](update-binding-images/image01new.png "Nella finestra di dialogo nuova soluzione, selezionare iOS / API unificata / progetto di Binding iOS")](update-binding-images/image01new.png#lightbox)
4. Nella finestra di dialogo 'Configura il nuovo progetto' Immettere un **Name** per il nuovo progetto di associazione e scegliere il **OK** pulsante.
5. Includere la versione a 64 bit della libreria Objective-C che si sta per essere la creazione di associazioni per.
6. Copiare il codice sorgente dal progetto associazione API classica esistente a 32 bit (ad esempio la `ApiDefinition.cs` e `StructsAndEnums.cs` file).
7. Apportare le modifiche indicate sopra per i file del codice sorgente.

Con tutte queste modifiche posto, è possibile compilare la nuova versione a 64 bit dell'API come si farebbe con la versione a 32 bit.

## <a name="summary"></a>Riepilogo

In questo articolo sono stati mostrati le modifiche da apportare a un progetto esistente di Binding Xamarin per supportare la nuova API unificata e i dispositivi a 64 bit e i passaggi necessari per compilare la nuova versione compatibile a 64 bit di un'API.



## <a name="related-links"></a>Collegamenti correlati

- [IOS e Mac](~/cross-platform/macios/index.md)
- [API unificata](~/cross-platform/macios/nativetypes.md)
- [Considerazioni sulle piattaforme a 32 o 64 bit](~/cross-platform/macios/32-and-64/index.md)
- [L'aggiornamento di App iOS esistenti](~/cross-platform/macios/unified/updating-ios-apps.md)
- [API unificata](~/cross-platform/macios/unified/index.md)
- [BindingSample](https://developer.xamarin.com/samples/monotouch/BindingSample/)
