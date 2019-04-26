---
title: Programmazione di UrhoSharp conF#
description: Questo documento descrive come creare un semplice hello world UrhoSharp dell'applicazione tramite F# in Visual Studio per Mac.
ms.prod: xamarin
ms.assetid: F976AB09-0697-4408-999A-633977FEFF64
author: conceptdev
ms.author: crdun
ms.date: 03/29/2017
ms.openlocfilehash: 99e8f8bf04465d0d61086139ba9889eae141207e
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61160596"
---
# <a name="programming-urhosharp-with-f"></a>Programmazione di UrhoSharp conF#

Può essere programmato UrhoSharp con F# utilizzando le stesse librerie e i concetti usati da C# i programmatori. Il [uso di UrhoSharp](~/graphics-games/urhosharp/using.md) articolo offre una panoramica del motore di UrhoSharp e deve essere letto prima di questo articolo.

Ad esempio molte librerie che hanno origine in tutto il mondo di C++, molte funzioni di UrhoSharp restituiscono valori booleani o interi che indica esito positivo o negativo. È consigliabile usare `|> ignore` ignorare tali valori.

Il [programma di esempio](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp) è un "Hello World" di UrhoSharp da F#.

## <a name="creating-an-empty-project"></a>Creazione di un progetto vuoto

Esistono nessun F# modelli per UrhoSharp ancora disponibile, pertanto, per creare il proprio progetto di UrhoSharp è possibile avviare con il [campione](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp) o seguire questa procedura:

1. Da Visual Studio per Mac, creare una nuova **soluzione**. Scegli **iOS > App > App visualizzazione singola** e selezionare **F#** come linguaggio di implementazione. 
1. Eliminare il **Main. Storyboard** file. Aprire il **Info. plist** file e nel **iPhone / iPod le informazioni sulla distribuzione** riquadro, eliminare il `Main` stringa nel **interfaccia principale** elenco a discesa.
1. Eliminare il **ViewController.fs** anche file.

## <a name="building-hello-world-in-urho"></a>Compilazione Hello World in Urho

A questo punto si è pronti iniziare a definire le classi del gioco. Come minimo, è necessario definire una sottoclasse `Urho.Application` ed eseguire l'override relativo `Start` (metodo). Per creare questo file, fare clic su di F# del progetto, scegliere **Aggiungi nuovo file...**  e aggiungere un oggetto vuoto F# classe al progetto. Il nuovo file verrà aggiunto alla fine dell'elenco di file nel progetto, ma è necessario trascinare in modo che appaia *prima* viene usato in **AppDelegate.fs**.

1. Aggiungere un riferimento al pacchetto Urho NuGet.
1. Da un progetto esistente Urho, copiare le directory (grande) **CoreData /** e **dati /** all'interno del progetto **risorse /** directory. Nel F# del progetto, fare clic su di **le risorse** cartella e utilizzare **Aggiungi / Aggiungi cartella esistente** per aggiungere tutti questi file al progetto.

La struttura del progetto sarà ora simile:

![](fsharp-images/solutionpane.png "La struttura del progetto deve ora apparire come")

Definire una classe appena creata come sottotipo di `Urho.Application` ed eseguire l'override relativo `Start` metodo:

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

Il codice è molto semplice. Usa il `Urho.Gui.Text` classe per visualizzare una stringa allineata al centro con una determinata dimensione di carattere e colori. 

Prima di poter eseguire questo codice, tuttavia, è necessario inizializzare UrhoSharp. 

Aprire il file AppDelegate.fs e modificare il `FinishedLaunching` metodo come indicato di seguito:

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

Il `ApplicationOptions.Default` fornisce le opzioni predefinite per un'applicazione in modalità orizzontale. Passarli `ApplicationOptions` al costruttore predefinito per il `Application` sottoclasse (si noti che al momento della definizione di `HelloWorld` classe, la riga `inherit Application(o)` chiama il costruttore di classe di base). 

Il `Run` metodo di `Application` avvia il programma. Viene definito come restituzione di un `int`, che può essere inviato tramite pipe a `ignore`. 

Il programma risulta dovrebbe essere simile:

![](fsharp-images/helloworldfsharp.png "Il programma risulta dovrebbe essere simile")








## <a name="related-links"></a>Collegamenti correlati

- [Sfoglia su GitHub (esempio)](https://github.com/xamarin/recipes/tree/master/Recipes/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp)
