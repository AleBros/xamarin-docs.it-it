---
title: 'Programmazione di UrhoSharp con F #'
description: "Questo documento descrive come creare un'applicazione di UrhoSharp semplice hello world con F # in Visual Studio per Mac."
ms.prod: xamarin
ms.assetid: F976AB09-0697-4408-999A-633977FEFF64
author: charlespetzold
ms.author: chape
ms.date: 03/29/2017
ms.openlocfilehash: a4e1a31a2591c799a153e1333e4a4a4a0719a107
ms.sourcegitcommit: e98a9ce8b716796f15de7cec8c9465c4b6bb2997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2018
ms.locfileid: "39111199"
---
# <a name="programming-urhosharp-with-f"></a>Programmazione di UrhoSharp con F #

È possibile programmare UrhoSharp con F # usando le stesse librerie e i concetti usati per i programmatori c#. Il [uso di UrhoSharp](~/graphics-games/urhosharp/using.md) articolo offre una panoramica del motore di UrhoSharp e deve essere letto prima di questo articolo.

Ad esempio molte librerie che hanno origine in tutto il mondo di C++, molte funzioni di UrhoSharp restituiscono valori booleani o interi che indica esito positivo o negativo. È consigliabile usare `|> ignore` ignorare tali valori.

Il [programma di esempio](https://github.com/xamarin/recipes/tree/master/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp) è un "Hello World" di UrhoSharp con F #.

## <a name="creating-an-empty-project"></a>Creazione di un progetto vuoto

Non sono disponibili modelli per UrhoSharp F # è ancora disponibile, pertanto, per creare il proprio progetto di UrhoSharp è possibile avviare con il [esempio](https://github.com/xamarin/recipes/tree/master/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp) o seguire questa procedura:

1. Da Visual Studio per Mac, creare una nuova **soluzione**. Scegli **iOS > App > App visualizzazione singola** e selezionare **F #** come linguaggio di implementazione. 
1. Eliminare il **Main. Storyboard** file. Aprire il **Info. plist** file e nel **iPhone / iPod le informazioni sulla distribuzione** riquadro, eliminare il `Main` stringa nel **interfaccia principale** elenco a discesa.
1. Eliminare il **ViewController.fs** anche file.

## <a name="building-hello-world-in-urho"></a>Compilazione Hello World in Urho

A questo punto si è pronti iniziare a definire le classi del gioco. Come minimo, è necessario definire una sottoclasse `Urho.Application` ed eseguire l'override relativo `Start` (metodo). Per creare questo file, fare clic su progetto F #, scegliere **Aggiungi nuovo file...**  e aggiungere una classe F # vuota al progetto. Il nuovo file verrà aggiunto alla fine dell'elenco di file nel progetto, ma è necessario trascinare in modo che appaia *prima* viene usato in **AppDelegate.fs**.

1. Aggiungere un riferimento al pacchetto Urho NuGet.
1. Da un progetto esistente Urho, copiare le directory (grande) **CoreData /** e **dati /** all'interno del progetto **risorse /** directory. Nel progetto F #, fare clic sui **le risorse** cartella e utilizzare **Aggiungi / Aggiungi cartella esistente** per aggiungere tutti questi file al progetto.

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

- [Sfoglia su GitHub (esempio)](https://github.com/xamarinhttps://developer.xamarin.com/recipes/tree/master/cross-platform/urho/urho-fsharp/HelloWorldUrhoFsharp)
