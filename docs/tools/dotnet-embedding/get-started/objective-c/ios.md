---
title: Guida introduttiva a iOS
description: Questo documento viene descritto come iniziare a utilizzare .NET incorporamento con iOS. Illustra i requisiti e presenta un'app di esempio per illustrare come associare un assembly gestito e usare l'output in un progetto Xcode.
ms.prod: xamarin
ms.assetid: D5453695-69C9-44BC-B226-5B86950956E2
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: 683f448c3e0cdba1bbe3ba68f0db3225675f5830
ms.sourcegitcommit: 3f2737f8abf9b855edf060474aa222e973abda3f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37066897"
---
# <a name="getting-started-with-ios"></a>Guida introduttiva a iOS

## <a name="requirements"></a>Requisiti

Oltre ai requisiti dal nostro [Guida introduttiva a Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) Guida, è anche necessario:

* [Xamarin. IOS 10.11](https://visualstudio.microsoft.com/xamarin/) o versione successiva

## <a name="hello-world"></a>Hello world

In primo luogo, compilazione di un esempio semplice hello world in c#.

### <a name="create-c-sample"></a>Creazione di un esempio c#

Aprire Visual Studio per Mac, creare un nuovo progetto di libreria di classi di iOS e denominarlo **hello da csharp**e salvarlo in un **~/Projects/hello-from-csharp**.

Sostituire il codice di **MyClass.cs** file con il frammento seguente:

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

Come descritto nel [installazione](~/tools/dotnet-embedding/get-started/install/install.md) Guida, questa operazione può essere eseguita come passaggio post-compilazione del progetto, con una destinazione di MSBuild personalizzata o manualmente:

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=iOS --outdir=output -c --debug
```

Il framework verrà inserito nelle **~/Projects/hello-from-csharp/output/hello-from-csharp.framework**.

### <a name="use-the-generated-output-in-an-xcode-project"></a>Utilizzare l'output generato in un progetto Xcode

Aprire Xcode, creare un nuovo iOS singola applicazione di visualizzazione, denominarlo **hello da csharp**e selezionare il **Objective-C** language.

Aprire la **~/Projects/hello-from-csharp/output** directory Finder, selezionare **hello da csharp.framework**, trascinarla al progetto Xcode e rilasciarla subito sopra il **hello da csharp**  cartella nel progetto.

! [Trascinamento della selezione framework] Images/Hello-from-CSharp-IOS-Drag-Drop-Framework.PNG)

Assicurarsi che **copiare gli elementi, se necessario** sia selezionata nella finestra di dialogo che viene visualizzato e fare clic su **fine**.

![Copiare gli elementi, se necessario](ios-images/hello-from-csharp-ios-copy-items-if-needed.png)

Selezionare il **hello da csharp** del progetto e passare al **hello da csharp** della destinazione **scheda Generale**. Nel **binari incorporati** sezione, aggiungere **hello da csharp.framework**.

![File binari incorporati](ios-images/hello-from-csharp-ios-embedded-binaries.png)

Aprire **ViewController.m**e sostituire il contenuto con:

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

Incorporamento di .NET non supporta attualmente bitcode in iOS, è abilitata per alcuni modelli di progetto Xcode. 

Disabilitarla nelle impostazioni di progetto:

![Opzione Bitcode](../../images/ios-bitcode-option.png)

Infine, eseguire il progetto Xcode e verrà visualizzato un codice simile al seguente:

![Hello tratto dall'esempio c# in esecuzione nel simulatore](ios-images/hello-from-csharp-ios.png)
