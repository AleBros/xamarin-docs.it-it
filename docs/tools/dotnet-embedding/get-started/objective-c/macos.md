---
title: Introduzione a macOS
description: Questo documento descrive come iniziare a usare .NET incorporamento con macOS. Illustra i requisiti e presenta un'applicazione di esempio per illustrare come eseguire il binding dell'assembly gestito e usare l'output generato in un progetto Xcode.
ms.prod: xamarin
ms.assetid: AE51F523-74F4-4EC0-B531-30B71C4D36DF
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 3de47aa57df29f52f71508977ae017dff009c282
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61181547"
---
# <a name="getting-started-with-macos"></a>Introduzione a macOS

## <a name="what-you-will-need"></a>Che cosa è necessario

* Seguire le istruzioni di [Guida introduttiva a Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) Guida.

## <a name="hello-world"></a>Hello world

Innanzitutto, compilare un esempio di semplice hello world in c#.

### <a name="create-c-sample"></a>Creare codice c# di esempio

Aprire Visual Studio per Mac, creare un nuovo progetto di libreria di classi Mac denominato **hello-da-csharp**e salvarlo **~/Projects/hello-from-csharp**.

Sostituire il codice nel **MyClass.cs** file con il frammento seguente:

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

Come descritto nel [installazione](~/tools/dotnet-embedding/get-started/install/install.md) Guida, questa operazione può essere eseguita come passaggio di post-compilazione nel progetto, con una destinazione di MSBuild personalizzata o manualmente:

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=macOS-modern --abi=x86_64 --outdir=output -c --debug
```

Il framework di disponibilità verrà inserito in **~/Projects/hello-from-csharp/output/hello-from-csharp.framework**.

### <a name="use-the-generated-output-in-an-xcode-project"></a>Usare l'output generato in un progetto Xcode

Aprire Xcode e creare una nuova applicazione Cocoa. Denominarlo **hello-da-csharp** e selezionare il **Objective-C** language.

Aprire il **~/Projects/hello-from-csharp/output** directory nel Finder, selezionare **hello-da-csharp.framework**, trascinarla al progetto Xcode e rilasciarlo subito sopra il **hello-da-csharp**  cartella nel progetto.

![Framework di trascinamento della selezione](macos-images/hello-from-csharp-mac-drag-drop-framework.png)

Assicurarsi che **copia elementi se necessario** sia selezionata nella finestra di dialogo visualizzata e fare clic su **fine**.

![Copia elementi se necessario](macos-images/hello-from-csharp-mac-copy-items-if-needed.png)

Selezionare il **hello-da-csharp** del progetto e passare alle **hello-da-csharp** della destinazione **generali** scheda. Nel **binari incorporati** , quindi aggiungere **hello-da-csharp.framework**.

![File binari incorporati](macos-images/hello-from-csharp-mac-embedded-binaries.png)

Aprire **viewcontroller. M**e sostituire il contenuto con:

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

Infine, eseguire il progetto Xcode e verrà visualizzato un valore simile al seguente:

![Saluti da codice c# di esempio in esecuzione nel simulatore](macos-images/hello-from-csharp-mac.png)

Un esempio più completo e migliorarla [è disponibile qui](https://github.com/mono/Embeddinator-4000/tree/objc/samples/mac/weather).
