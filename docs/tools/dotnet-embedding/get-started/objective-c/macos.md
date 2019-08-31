---
title: Introduzione a macOS
description: Questo documento descrive come iniziare a usare l'incorporamento di .NET con macOS. Vengono illustrati i requisiti e viene presentata un'applicazione di esempio per illustrare come associare l'assembly gestito e utilizzare l'output generato in un progetto Xcode.
ms.prod: xamarin
ms.assetid: AE51F523-74F4-4EC0-B531-30B71C4D36DF
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: ee40a5ef3504e5d274a34ec2d9569026e5d40551
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199867"
---
# <a name="getting-started-with-macos"></a>Introduzione a macOS

## <a name="what-you-will-need"></a>Elementi necessari

* Seguire le istruzioni nella Guida introduttiva a [Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) .

## <a name="hello-world"></a>Hello world

Per prima cosa, creare un semplice esempio Hello World in C#.

### <a name="create-c-sample"></a>Crea C# esempio

Aprire Visual Studio per Mac, creare un nuovo progetto di libreria di classi Mac denominato **Hello-from-CSharp**e salvarlo in **~/Projects/Hello-from-CSharp**.

Sostituire il codice nel file **MyClass.cs** con il frammento di codice seguente:

```csharp
using AppKit;
public class MyNSView : NSTextView
{
    public MyNSView ()
    {
        Value = "Hello from C#";
    }
}
```

Compilare il progetto. L'assembly risultante verrà salvato come **~/Projects/Hello-from-CSharp/Hello-from-CSharp/bin/debug/Hello-from-CSharp.dll**.

### <a name="bind-the-managed-assembly"></a>Associare l'assembly gestito

Quando si dispone di un assembly gestito, associarlo richiamando l'incorporamento .NET.

Come descritto nella Guida all' [installazione](~/tools/dotnet-embedding/get-started/install/install.md) , questa operazione può essere eseguita come passaggio di post-compilazione nel progetto, con una destinazione di MSBuild personalizzata o manualmente:

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=macOS-modern --abi=x86_64 --outdir=output -c --debug
```

Il Framework verrà inserito in **~/Projects/Hello-from-CSharp/output/Hello-from-CSharp.Framework**.

### <a name="use-the-generated-output-in-an-xcode-project"></a>Usare l'output generato in un progetto Xcode

Aprire Xcode e creare una nuova applicazione Cocoa. Denominarlo **Hello-from-CSharp** e selezionare il linguaggio **Objective-C** .

Aprire la directory **~/Projects/Hello-from-CSharp/output** in Finder, selezionare **Hello-from-CSharp. Framework**, trascinarlo nel progetto Xcode e rilasciarlo appena sopra la cartella **Hello-from-CSharp** nel progetto.

![Trascinamento della selezione del Framework](macos-images/hello-from-csharp-mac-drag-drop-framework.png)

Assicurarsi che **Copia elementi se necessario** sia selezionato nella finestra di dialogo visualizzata, quindi fare clic su **fine**.

![Copia elementi se necessario](macos-images/hello-from-csharp-mac-copy-items-if-needed.png)

Selezionare il progetto **Hello-from-CSharp** e passare alla scheda **generale** della destinazione **Hello-from-CSharp** . Nella sezione **file binario incorporato** aggiungere **Hello-from-CSharp. Framework**.

![File binari incorporati](macos-images/hello-from-csharp-mac-embedded-binaries.png)

Aprire **ViewController. m**e sostituire il contenuto con:

```objc
#import "ViewController.h"

#include "hello-from-csharp/hello-from-csharp.h"

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    
    MyNSView *view = [[MyNSView alloc] init];
    view.frame = CGRectMake(0, 200, 200, 200);
    [self.view addSubview: view];
}

@end
```

Infine, eseguire il progetto Xcode e verrà visualizzato un risultato simile al seguente:

![Hello C# dall'esempio in esecuzione nel simulatore](macos-images/hello-from-csharp-mac.png)

Un esempio più completo e migliore [è disponibile qui](https://github.com/mono/Embeddinator-4000/tree/objc/samples/mac/weather).
