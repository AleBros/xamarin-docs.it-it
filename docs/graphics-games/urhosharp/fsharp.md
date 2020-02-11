---
title: Programmazione di UrhoSharp conF#
description: Questo documento descrive come creare una semplice applicazione UrhoSharp Hello World usando F# in Visual Studio per Mac.
ms.prod: xamarin
ms.assetid: F976AB09-0697-4408-999A-633977FEFF64
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: d87749bd74cf2c478e96284060fed7386d10b853
ms.sourcegitcommit: 0df727caf941f1fa0aca680ec871bfe7a9089e7c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69621009"
---
# <a name="programming-urhosharp-with-f"></a>Programmazione di UrhoSharp con F\#

UrhoSharp può essere programmato F# usando le stesse librerie e i concetti usati dai C# programmatori. L'articolo [uso di UrhoSharp](~/graphics-games/urhosharp/using.md) offre una panoramica del motore UrhoSharp e deve essere letto prima di questo articolo.

Analogamente a molte librerie originate nel C++ mondo, molte funzioni UrhoSharp restituiscono valori booleani o numeri interi che indicano l'esito positivo o negativo. Usare `|> ignore` per ignorare questi valori.

Il [programma di esempio](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp) è un "Hello World" per UrhoSharp F#di.

## <a name="creating-an-empty-project"></a>Creazione di un progetto vuoto

Non F# sono ancora disponibili modelli per UrhoSharp, quindi per creare il proprio progetto UrhoSharp è possibile iniziare con l' [esempio](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp) o attenersi alla procedura seguente:

1. Da Visual Studio per Mac creare una nuova **soluzione**. Scegliere **iOS > app > app visualizzazione singola** e selezionare **F#** come lingua di implementazione. 
1. Eliminare il file **Main. Storyboard** . Aprire il file **info. plist** e nel riquadro **informazioni sulla distribuzione di iPhone/iPod** eliminare la `Main` stringa nell'elenco a discesa dell' **interfaccia principale** .
1. Eliminare anche il file **ViewController. FS** .

## <a name="building-hello-world-in-urho"></a>Compilazione di Hello World in Urho

A questo punto è possibile iniziare a definire le classi del gioco. Come minimo, sarà necessario definire una sottoclasse di ed eseguire `Urho.Application` l'override del `Start` relativo metodo. Per creare questo file, fare clic con il pulsante F# destro del mouse sul progetto, scegliere **Aggiungi nuovo file** e aggiungere una F# classe vuota al progetto. Il nuovo file verrà aggiunto alla fine dell'elenco dei file nel progetto, ma è necessario trascinarlo in modo che venga visualizzato *prima* che venga usato in **AppDelegate. FS**.

1. Aggiungere un riferimento al pacchetto NuGet Urho.
1. Da un progetto Urho esistente, copiare le directory (Large) **CoreData/** e i **dati/** nella directory/ **risorse** del progetto. Nel F# progetto, fare clic con il pulsante destro del mouse sulla cartella **risorse** e utilizzare **Aggiungi/Aggiungi cartella esistente** per aggiungere tutti questi file al progetto.

La struttura del progetto dovrebbe ora essere simile alla seguente:

![](fsharp-images/solutionpane.png "La struttura del progetto dovrebbe ora essere simile a")

Definire la classe appena creata come sottotipo di ed eseguire `Urho.Application` l'override del `Start` relativo metodo:

```fsharp
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

Il codice è molto semplice. Usa la `Urho.Gui.Text` classe per visualizzare una stringa allineata al centro con una determinata dimensione del tipo di carattere e del colore. 

Prima che questo codice possa essere eseguito, tuttavia, è necessario inizializzare UrhoSharp. 

Aprire il file AppDelegate. FS e modificare il `FinishedLaunching` metodo come indicato di seguito:

```fsharp
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

`ApplicationOptions.Default` Fornisce le opzioni predefinite per un'applicazione in modalità orizzontale. Passarli `Application` `HelloWorld` `inherit Application(o)` al costruttore predefinito per la sottoclasse (si noti che quando è stata definita la classe, la riga chiama il costruttore della classe base). `ApplicationOptions`

Il `Run` metodo`Application` di avvia il programma. Viene definito come la restituzione `int`di un oggetto, che può essere `ignore`reindirizzato a.

Il programma risultante dovrebbe essere simile a questo screenshot:

![Screenshot del programma risultante](fsharp-images/helloworldfsharp.png)

## <a name="related-links"></a>Collegamenti correlati

- [Sfogliare su GitHub (esempio)](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp)
