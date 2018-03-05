---
title: 'Programmazione UrhoSharp con F #'
description: 'Come creare una semplice applicazione UrhoSharp con F # in Visual Studio per Mac'
ms.topic: article
ms.prod: xamarin
ms.assetid: F976AB09-0697-4408-999A-633977FEFF64
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.openlocfilehash: 98bbe42b0fddbbc4a431b777a6ff87cc6bea6a84
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="programming-urhosharp-with-f"></a>Programmazione UrhoSharp con F #

_Come creare una semplice applicazione UrhoSharp con F # in Visual Studio per Mac_

UrhoSharp può essere programmato con F # utilizzando le stesse librerie e i concetti usati dai programmatori c#. Il [UrhoSharp utilizzando](~/graphics-games/urhosharp/using.md) articolo viene fornita una panoramica del motore di UrhoSharp e deve essere letto prima di questo articolo.

Come molte librerie che ha avuto origine nel mondo C++, molte funzioni UrhoSharp restituiscono valori booleani o numeri interi che indica l'esito positivo o negativo. È consigliabile utilizzare `|> ignore` per ignorare tali valori.

Il [programma di esempio](https://github.com/xamarin/recipes/tree/master/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp) è un "Hello World" per UrhoSharp da F #.

## <a name="creating-an-empty-project"></a>Creazione di un progetto vuoto

Non sono disponibili modelli di F # per UrhoSharp ancora disponibile, pertanto, per creare un progetto UrhoSharp puoi entrambi iniziano con la [esempio](https://github.com/xamarin/recipes/tree/master/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp) o eseguire la procedura seguente:

1. Da Visual Studio per Mac, creare un nuovo **soluzione**. Scegliere **iOS > App > singola vista App** e selezionare **F #** come linguaggio di implementazione. 
1. Eliminare il **Main** file. Aprire il **Info. plist** file e il **iPhone / iPod le informazioni sulla distribuzione** riquadro, eliminare il `Main` stringa nel **interfaccia principale** elenco a discesa.
1. Eliminare il **ViewController.fs** anche i file.

## <a name="building-hello-world-in-urho"></a>Compilazione Hello World in Urho

A questo punto si è pronti iniziare a definire le classi del gioco. Come minimo, è necessario definire una sottoclasse di `Urho.Application` ed eseguire l'override relativo `Start` metodo. Per creare questo file, fare clic su progetto F #, scegliere **Aggiungi nuovo file...**  e aggiungere una classe F # vuota al progetto. Il nuovo file verrà aggiunto alla fine dell'elenco di file nel progetto, ma è necessario trascinare in modo che venga visualizzato *prima* viene utilizzato in **AppDelegate.fs**.

1. Aggiungere un riferimento al pacchetto Urho NuGet.
1. Da un progetto esistente Urho, copiare le directory (grande) **CoreData /** e **dati /** all'interno del progetto **risorse /** directory. Nel progetto F #, fare clic su di **risorse** cartella e utilizzare **Add / Aggiungi cartella esistente** per aggiungere tutti i file al progetto.

La struttura del progetto sarà ora simile:

![](fsharp-images/solutionpane.png "La struttura del progetto dovrebbe essere simile a")

Definire la classe appena creata come sottotipo di `Urho.Application` ed eseguire l'override relativo `Start` metodo:

```csharp
namespace HelloWorldUrho1

open Urho
open Urho.Gui
open Urho.iOS

type HelloWorld(o : ApplicationOptions) =
    inherit Urho.Application(o) 

override this.Start() = 
        let cache = this.ResourceCache
        let helloText = new Text()

        helloText.Value <- "Hello World from Urho3D, Mono, and F#"
        helloText.HorizontalAlignment <- HorizontalAlignment.Center
        helloText.VerticalAlignment <- VerticalAlignment.Center

        helloText.SetColor (new Color(0.f, 1.f, 0.f))
        let f = cache.GetFont("Fonts/Anonymous Pro.ttf")

        helloText.SetFont(f, 30) |> ignore
                  
        this.UI.Root.AddChild(helloText)
            
```

Il codice è molto semplice. Usa il `Urho.Gui.Text` classe per visualizzare una stringa allineata al centro con una determinata dimensione di carattere e colori. 

Prima di esecuzione questo codice, tuttavia, è necessario inizializzare UrhoSharp. 

Aprire il file AppDelegate.fs e modificare il `FinishedLaunching` metodo come indicato di seguito:

```csharp
namespace HelloWorldUrho1

open System
open UIKit
open Foundation
open Urho
open Urho.iOS

[<Register ("AppDelegate")>]
type AppDelegate () =
    inherit UIApplicationDelegate ()

    override this.FinishedLaunching (app, options) =
        let o = ApplicationOptions.Default
     
        let g = new HelloWorld(o)
        g.Run() |> ignore
       
        true
```

Il `ApplicationOptions.Default` fornisce le opzioni predefinite per un'applicazione in modalità orizzontale. Passare questi `ApplicationOptions` al costruttore predefinito per il `Application` sottoclasse (si noti che al momento di definire il `HelloWorld` classe, la riga `inherit Application(o)` chiama il costruttore di classe di base). 

Il `Run` metodo i `Application` avvia il programma. Viene definito come restituire un `int`, che può essere reindirizzato al `ignore`. 

Il programma risulta dovrebbe essere simile:

![](fsharp-images/helloworldfsharp.png "Il programma risulta dovrebbe essere simile")








## <a name="related-links"></a>Collegamenti correlati

- [Sfoglia su GitHub (esempio)](https://github.com/xamarinhttps://developer.xamarin.com/recipes/tree/master/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp)
