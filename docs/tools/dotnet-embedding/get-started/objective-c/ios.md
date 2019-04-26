---
title: Introduzione a iOS
description: Questo documento descrive come iniziare a usare .NET incorporamento con iOS. Illustra i requisiti e presenta un'app di esempio per illustrare come associare un assembly gestito e usare l'output in un progetto Xcode.
ms.prod: xamarin
ms.assetid: D5453695-69C9-44BC-B226-5B86950956E2
author: lobrien
ms.author: laobri
ms.date: 11/14/2017
ms.openlocfilehash: 009772ac88ad57bab53fb71c9705b71f0f8acc8b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61318702"
---
# <a name="getting-started-with-ios"></a>Introduzione a iOS

## <a name="requirements"></a>Requisiti

Oltre ai requisiti dal nostro [introduttiva con Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) Guida, è anche necessario:

* [Xamarin. IOS 10.11](https://visualstudio.microsoft.com/xamarin/) o versione successiva

## <a name="hello-world"></a>Hello world

Innanzitutto, compilare un esempio di semplice hello world in c#.

### <a name="create-c-sample"></a>Creare codice c# di esempio

Aprire Visual Studio per Mac, creare un nuovo progetto libreria di classi iOS, denominarlo **hello-da-csharp**e salvarlo **~/Projects/hello-from-csharp**.

Sostituire il codice nel **MyClass.cs** file con il frammento seguente:

```csharp
using UIKit;
public class MyUIView : UITextView
{
    public MyUIView ()
    {
        Text = "Hello from C#";
    }
}
```

Compilare il progetto e l'assembly risultante verrà salvato come **~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll**.

### <a name="bind-the-managed-assembly"></a>Associare l'assembly gestito

Dopo aver creato un assembly gestito, associarlo richiamando l'incorporamento di .NET.

Come descritto nel [installazione](~/tools/dotnet-embedding/get-started/install/install.md) Guida, questa operazione può essere eseguita come passaggio di post-compilazione nel progetto, con una destinazione di MSBuild personalizzata o manualmente:

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=iOS --outdir=output -c --debug
```

Il framework di disponibilità verrà inserito in **~/Projects/hello-from-csharp/output/hello-from-csharp.framework**.

### <a name="use-the-generated-output-in-an-xcode-project"></a>Usare l'output generato in un progetto Xcode

Aprire Xcode, creare un nuova applicazione visualizzazione singola iOS, denominarlo **hello-da-csharp**e selezionare il **Objective-C** language.

Aprire il **~/Projects/hello-from-csharp/output** directory nel Finder, selezionare **hello-da-csharp.framework**, trascinarla al progetto Xcode e rilasciarlo subito sopra il **hello-da-csharp**  cartella nel progetto.

![Framework di trascinamento della selezione](ios-images/hello-from-csharp-ios-drag-drop-framework.png)

Assicurarsi che **copia elementi se necessario** sia selezionata nella finestra di dialogo visualizzata e fare clic su **fine**.

![Copia elementi se necessario](ios-images/hello-from-csharp-ios-copy-items-if-needed.png)

Selezionare il **hello-da-csharp** del progetto e passare alle **hello-da-csharp** della destinazione **scheda Generale**. Nel **binari incorporati** , quindi aggiungere **hello-da-csharp.framework**.

![File binari incorporati](ios-images/hello-from-csharp-ios-embedded-binaries.png)

Aprire **viewcontroller. M**e sostituire il contenuto con:

```objective-c
#import "ViewController.h"
#include "hello-from-csharp/hello-from-csharp.h"

@interface ViewController ()
@end

@implementation ViewController
- (void)viewDidLoad {
    [super viewDidLoad];

    MyUIView *view = [[MyUIView alloc] init];
    view.frame = CGRectMake(0, 200, 200, 200);
    [self.view addSubview: view];
}
@end
```

Incorporamento di .NET non supporta attualmente bitcode su iOS, che è abilitato per alcuni modelli di progetto Xcode. 

Disabilitarlo nelle impostazioni del progetto:

![Opzione Bitcode](../../images/ios-bitcode-option.png)

Infine, eseguire il progetto Xcode e verrà visualizzato un valore simile al seguente:

![Saluti da codice c# di esempio in esecuzione nel simulatore](ios-images/hello-from-csharp-ios.png)
