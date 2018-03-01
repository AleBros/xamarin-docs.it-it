---
title: Introduzione a iOS
ms.topic: article
ms.prod: xamarin
ms.assetid: D5453695-69C9-44BC-B226-5B86950956E2
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 11/14/2017
ms.openlocfilehash: c1b21e36d314b79402702f29428f2c337ddc1069
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="getting-started-with-ios"></a>Introduzione a iOS


## <a name="requirements"></a>Requisiti

Oltre ai requisiti dal nostro [introduzione Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) Guida sarà inoltre necessario:

* [Xamarin. IOS 10.11 +](https://jenkins.mono-project.com/view/Xamarin.MaciOS/job/xamarin-macios-builds-master/) dal nostro _master_ ramo.

## <a name="hello-world"></a>Hello world

Prima di un esempio semplice di hello world in c# è opportuno creare.

### <a name="create-c-sample"></a>Creazione di un esempio in c#

Aprire Visual Studio per Mac, creare un nuovo progetto libreria di classi di iOS, denominarla `hello-from-csharp`e salvarlo in `~/Projects/hello-from-csharp`.

Sostituire il codice di `MyClass.cs` file con il frammento seguente:

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

Compilare il progetto, l'assembly risultante verrà salvato come `~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll`.

### <a name="bind-the-managed-assembly"></a>Associare l'assembly gestito

Eseguire il embeddinator per creare un framework nativo per l'assembly gestito:

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=iOS --outdir=output -c --debug
```

Il framework verrà inserito in `~/Projects/hello-from-csharp/output/hello-from-csharp.framework`.

### <a name="use-the-generated-output-in-an-xcode-project"></a>Utilizzare l'output generato in un progetto Xcode

Xcode aprire e creare un nuovo iOS singola applicazione di visualizzazione, denominarlo `hello-from-csharp` e selezionare il **Objective-C** language.

Aprire il `~/Projects/hello-from-csharp/output` directory in Finder, selezionare `hello-from-csharp.framework`, trascinarlo al progetto Xcode e rilasciarlo di sopra di `hello-from-csharp` cartella nel progetto.

! [Trascinare e rilasciare framework] Images/Hello-from-CSharp-IOS-Drag-Drop-Framework.PNG)

Assicurarsi che `Copy items if needed` è selezionata nella finestra di dialogo che viene visualizzato e fare clic su `Finish`.

![Copiare gli elementi, se necessario](ios-images/hello-from-csharp-ios-copy-items-if-needed.png)

Selezionare il `hello-from-csharp` del progetto e passare al `hello-from-csharp` della destinazione **scheda Generale**. Nel **binari incorporati** sezione, aggiungere `hello-from-csharp.framework`.

![File binari incorporati](ios-images/hello-from-csharp-ios-embedded-binaries.png)

Aprire ViewController.m e sostituire il contenuto con:

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

Infine eseguire il progetto Xcode e simile al seguente verrà visualizzati:

![Hello c# di esempio in esecuzione nel simulatore](ios-images/hello-from-csharp-ios.png)
