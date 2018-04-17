---
title: Introduzione a macOS
ms.prod: xamarin
ms.assetid: AE51F523-74F4-4EC0-B531-30B71C4D36DF
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: f75ced921cd240e280b5dd6f7366ccceefb5e40e
ms.sourcegitcommit: bc39d85b4585fcb291bd30b8004b3f7edcac4602
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="getting-started-with-macos"></a>Introduzione a macOS


## <a name="what-you-will-need"></a>È necessario

* Seguire le istruzioni il [Guida introduttiva a Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) Guida.

## <a name="hello-world"></a>Hello world

In primo luogo, compilazione di un esempio semplice hello world in c#.

### <a name="create-c-sample"></a>Creazione di un esempio in c#

Aprire Visual Studio per Mac, creare un nuovo progetto libreria di classi Mac denominato **hello da csharp**e salvarlo in un **~/Projects/hello-from-csharp**.

Sostituire il codice di `MyClass.cs` file con il frammento seguente:

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

Eseguire il embeddinator per creare un framework nativo per l'assembly gestito:

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

Infine eseguire il progetto Xcode e simile al seguente verrà visualizzati:

![Hello c# di esempio in esecuzione nel simulatore](macos-images/hello-from-csharp-mac.png)

È disponibile un esempio più completo e fretta [qui](https://github.com/mono/Embeddinator-4000/tree/objc/samples/mac/weather).
