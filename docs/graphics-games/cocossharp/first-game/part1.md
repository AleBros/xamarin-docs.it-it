---
title: Creazione di un progetto CocosSharp multipiattaforma
description: 'Questa procedura dettagliata viene illustrato come creare una nuova soluzione CocosSharp multipiattaforma. Il risultato di questa procedura dettagliata è un Visual Studio per la soluzione Mac che include tre progetti: un progetto libreria di classi portabile, un progetto Android specifici e un progetto iOS specifico. Il progetto risultante visualizzerà una schermata nera vuota quando eseguita.'
ms.topic: article
ms.prod: xamarin
ms.assetid: 37C97693-B0A8-4064-97B6-A6FAB5BA4FB7
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/27/2017
ms.openlocfilehash: 2906035ce9bd44d111b89ccfe7443896775315b7
ms.sourcegitcommit: 7b88081a979381094c771421253d8a388b2afc16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2018
---
# <a name="creating-a-multi-platform-cocossharp-project"></a>Creazione di un progetto CocosSharp multipiattaforma

_Questa procedura dettagliata viene illustrato come creare una nuova soluzione CocosSharp multipiattaforma. Il risultato di questa procedura dettagliata è un Visual Studio per la soluzione Mac che include tre progetti: un progetto libreria di classi portabile, un progetto Android specifici e un progetto iOS specifico. Il progetto risultante visualizzerà una schermata nera vuota quando eseguita._

Il motore di giochi 2D CocosSharp consente codice e il contenuto da condividere tra più piattaforme. Questa procedura dettagliata viene illustrato come creare un progetto con il supporto iOS e Android development. In particolare, questa procedura dettagliata verrà trattati i seguenti argomenti:

 - L'installazione CocosSharp
 - Creazione di una nuova soluzione
 - Metodo `LoadGame`

# <a name="installing-cocossharp"></a>L'installazione CocosSharp

In primo luogo, verrà aggiunto CocosSharp a Visual Studio per Mac. Se in esecuzione su un Mac, selezionare **Visual Studio per Mac** > **Gestione componenti aggiuntivi...**  . Se in esecuzione su Windows, selezionare **strumenti** > **Gestione componenti aggiuntivi...**  . Fare clic sul **raccolta** espandere il **CocosSharp elemento**selezionare **modelli di progetto CocosSharp**e infine fare clic su **installare...**  .

![Componente aggiuntivo CocosSharp](part1-images/xamarinstudioaddinsmac.png "")

# <a name="creating-a-new-solution"></a>Creazione di una nuova soluzione

Ora che CocosSharp è installato, verrà creata una soluzione. In Visual Studio per Mac, selezionare **File** > **New** > **soluzione...** . Selezionare il **App** opzione sotto il **multipiattaforma** sezione, selezionare **progetto vuoto CocosSharp**e quindi fare clic su **Avanti**:

![](part1-images/image1.png "Selezionare l'opzione App della sezione multipiattaforma, selezionare progetto vuoto CocosSharp e quindi fare clic su Avanti")

Immettere il nome **BouncingGame** per il **nome progetto**, quindi fare clic su **crea**:

![](part1-images/image2.png "Immettere il nome BouncingGame per il nome del progetto, quindi fare clic su Crea")

Dopo aver creato il progetto e Visual Studio per Mac, è possibile compilare ed eseguire il codice per visualizzare uno sfondo grigio: 

![](part1-images/image3.png "Una volta che il progetto è stato creato e Visual Studio per Mac, compilare ed eseguire il codice per visualizzare uno sfondo grigio")


# <a name="loadgame-method"></a>Metodo LoadGame

Il progetto CocosSharp predefinito include le classi specifiche di iOS e Android per la configurazione di un `CCGameView`, che consente di avviare CocosSharp. Il `CCGameView` istanza viene creata in modo specifico della piattaforma: crea il progetto iOS il `CCGameView` nel `Main.storyboard` file, mentre Android crea il `CCGameView` nel `Main.axml` file. Ogni piattaforma che usa l'istanza di CCGameView in un `LoadGame` metodo che esegue alcune operazioni fondamentali. Anche se è non modificare questo codice, diamo un'occhiata alcuni dettagli importanti:

 - Il codice imposta il `gameView.DesignResolution` a 1024 per 768. Questo consente di standardizzare il posizionamento su tutti i dispositivi, indipendentemente dalle proporzioni, risoluzione fisica o l'orientamento del dispositivo corrente. 
 - Il codice aggiunge alcuni percorsi di ricerca. I percorsi di ricerca consentono inoltre di contenuto deve essere caricato senza i prefissi di directory. Ad esempio, poiché il `"Sounds"` percorso viene aggiunto come un percorso di ricerca, quindi un file nella directory `"Content/Sounds/mysound.xnb"` semplicemente possono essere caricati come `"mysound.xnb"`. Sono simili ai percorsi di ricerca `using` istruzioni nel codice: consentono di ridurre il codice, ma possono anche introdurre ambiguità.
 - Nel codice viene creata una `GameLayer` istanza. `GameLayer` è un `CCLayer`-ereditare una classe in cui verrà aggiunta tutta la logica del gioco. Giochi maggiori possono richiedere più `CCLayer` istanze o multiplo `CCScene` istanze (che può contenere più `CCLayer` istanze), ma verranno trattate in un singolo `CCLayer` per il gioco.

#  <a name="summary"></a>Riepilogo

Questa procedura dettagliata viene descritto come creare un progetto di CocosSharp multipiattaforma usando Visual Studio per Mac. Il risultato è una schermata vuota che può essere utilizzata come punto di partenza per qualsiasi progetto di gioco.