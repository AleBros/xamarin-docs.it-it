---
title: Introduzione ad iOS
description: Questo documento descrive come iniziare a usare l'incorporamento di .NET con iOS. Vengono illustrati i requisiti e viene presentata un'app di esempio per illustrare come associare un assembly gestito e usare l'output in un progetto Xcode.
ms.prod: xamarin
ms.assetid: D5453695-69C9-44BC-B226-5B86950956E2
author: davidortinau
ms.author: daortin
ms.date: 11/14/2017
ms.openlocfilehash: 5697d20077b746d9d33db985111c2d04908d5d01
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73029755"
---
# <a name="getting-started-with-ios"></a>Introduzione ad iOS

## <a name="requirements"></a>Requisiti

Oltre ai requisiti della Guida [introduttiva a Objective-C](~/tools/dotnet-embedding/get-started/objective-c/index.md) , è necessario anche:

* [Xamarin.iOS 10,11](https://visualstudio.microsoft.com/xamarin/) o versione successiva

## <a name="hello-world"></a>Hello world

Per prima cosa, creare un semplice esempio Hello World in C#.

### <a name="create-c-sample"></a>Crea C# esempio

Aprire Visual Studio per Mac, creare un nuovo progetto libreria di classi iOS, denominarlo **Hello-from-CSharp**e salvarlo in **~/Projects/Hello-from-CSharp**.

Sostituire il codice nel file **MyClass.cs** con il frammento di codice seguente:

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

Compilare il progetto e l'assembly risultante verrà salvato come **~/Projects/Hello-from-CSharp/Hello-from-CSharp/bin/debug/Hello-from-CSharp.dll**.

### <a name="bind-the-managed-assembly"></a>Associare l'assembly gestito

Quando si dispone di un assembly gestito, associarlo richiamando l'incorporamento .NET.

Come descritto nella Guida all' [installazione](~/tools/dotnet-embedding/get-started/install/install.md) , questa operazione può essere eseguita come passaggio di post-compilazione nel progetto, con una destinazione di MSBuild personalizzata o manualmente:

```shell
cd ~/Projects/hello-from-csharp
objcgen ~/Projects/hello-from-csharp/hello-from-csharp/bin/Debug/hello-from-csharp.dll --target=framework --platform=iOS --outdir=output -c --debug
```

Il Framework verrà inserito in **~/Projects/Hello-from-CSharp/output/Hello-from-CSharp.Framework**.

### <a name="use-the-generated-output-in-an-xcode-project"></a>Usare l'output generato in un progetto Xcode

Aprire Xcode, creare una nuova applicazione di visualizzazione singola iOS, denominarla **Hello-from-CSharp**e selezionare il linguaggio **Objective-C** .

Aprire la directory **~/Projects/Hello-from-CSharp/output** in Finder, selezionare **Hello-from-CSharp. Framework**, trascinarlo nel progetto Xcode e rilasciarlo appena sopra la cartella **Hello-from-CSharp** nel progetto.

![Trascinamento della selezione del Framework](ios-images/hello-from-csharp-ios-drag-drop-framework.png)

Assicurarsi che **Copia elementi se necessario** sia selezionato nella finestra di dialogo visualizzata, quindi fare clic su **fine**.

![Copia elementi se necessario](ios-images/hello-from-csharp-ios-copy-items-if-needed.png)

Selezionare il progetto **Hello-from-CSharp** e passare alla **scheda generale**della destinazione **Hello-from-CSharp** . Nella sezione **file binario incorporato** aggiungere **Hello-from-CSharp. Framework**.

![File binari incorporati](ios-images/hello-from-csharp-ios-embedded-binaries.png)

Aprire **ViewController. m**e sostituire il contenuto con:

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

L'incorporamento .NET attualmente non supporta bitcode in iOS, che è abilitato per alcuni modelli di progetto Xcode. 

Disabilitarlo nelle impostazioni del progetto:

![Bitcode-opzione](../../images/ios-bitcode-option.png)

Infine, eseguire il progetto Xcode e verrà visualizzato un risultato simile al seguente:

![Hello C# dall'esempio in esecuzione nel simulatore](ios-images/hello-from-csharp-ios.png)
