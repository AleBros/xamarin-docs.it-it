---
title: Introduzione a macOS
description: Questo documento descrive come iniziare a utilizzare .NET incorporamento con macOS. Illustra i requisiti e presenta un'applicazione di esempio per illustrare come associare l'assembly gestito e usare l'output generato in un progetto Xcode.
ms.prod: xamarin
ms.assetid: AE51F523-74F4-4EC0-B531-30B71C4D36DF
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 38049eae5e420e5f3610341c2682fa92d2ac426e
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34793689"
---
# <a name="getting-started-with-macos"></a>Introduzione a macOS

## <a name="what-you-will-need"></a>È necessario

* Seguire le istruzioni il [Guida introduttiva a Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) Guida.

## <a name="hello-world"></a>Hello world

In primo luogo, compilazione di un esempio semplice hello world in c#.

### <a name="create-c-sample"></a>Creazione di un esempio in c#

Aprire Visual Studio per Mac, creare un nuovo progetto libreria di classi Mac denominato **hello da csharp**e salvarlo in un **~/Projects/hello-from-csharp**.

Sostituire il codice di **MyClass.cs** file con il frammento seguente:

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

Compilare il progetto. L'assembly risultante verrà salvato come **~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll**.

### <a name="bind-the-managed-assembly"></a>Associare l'assembly gestito

Dopo aver creato un assembly gestito, associarlo richiamando l'incorporamento di .NET.

Come descritto nel [installazione](~/tools/dotnet-embedding/get-started/install/install.md) Guida, questa operazione può essere eseguita come passaggio post-compilazione del progetto, con una destinazione di MSBuild personalizzata o manualmente:

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=macOS-modern --abi=x86_64 --outdir=output -c --debug
```

Il framework verrà inserito nelle **~/Projects/hello-from-csharp/output/hello-from-csharp.framework**.

### <a name="use-the-generated-output-in-an-xcode-project"></a>Utilizzare l'output generato in un progetto Xcode

Aprire Xcode e creare una nuova applicazione Cocoa. Denominarlo **hello da csharp** e selezionare il **Objective-C** language.

Aprire la **~/Projects/hello-from-csharp/output** directory Finder, selezionare **hello da csharp.framework**, trascinarla al progetto Xcode e rilasciarla subito sopra il **hello da csharp**  cartella nel progetto.

![Framework di trascinamento della selezione](macos-images/hello-from-csharp-mac-drag-drop-framework.png)

Assicurarsi che **copiare gli elementi, se necessario** sia selezionata nella finestra di dialogo che viene visualizzato e fare clic su **fine**.

![Copiare gli elementi, se necessario](macos-images/hello-from-csharp-mac-copy-items-if-needed.png)

Selezionare il **hello da csharp** del progetto e passare al **hello da csharp** della destinazione **generale** scheda. Nel **binari incorporati** sezione, aggiungere **hello da csharp.framework**.

![File binari incorporati](macos-images/hello-from-csharp-mac-embedded-binaries.png)

Aprire **ViewController.m**e sostituire il contenuto con:

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

Infine, eseguire il progetto Xcode e verrà visualizzato un codice simile al seguente:

![Hello c# di esempio in esecuzione nel simulatore](macos-images/hello-from-csharp-mac.png)

Un esempio più completo e fretta [è disponibile qui](https://github.com/mono/Embeddinator-4000/tree/objc/samples/mac/weather).
