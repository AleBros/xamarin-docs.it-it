---
title: 'Parte 1: creazione di un MonoGame multipiattaforma'
description: Questa procedura dettagliata viene illustrato come creare un nuovo progetto per iOS e Android utilizzando MonoGame. Il risultato è un Visual Studio per la soluzione Mac con un progetto di codice condiviso multipiattaforma, nonché un unico progetto per ogni piattaforma. Questo progetto verrà visualizzato un errore irreversibile vuoto quando eseguita.
ms.prod: xamarin
ms.assetid: FC69E69B-04D4-45DF-9BBF-2A6CDEAD9B2F
ms.technology: xamarin-cross-platform
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: 0cd12f23f8cb269b2a41a08bf641db08e18fb82b
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="part-1--creating-a-cross-platform-monogame"></a>Parte 1: creazione di un MonoGame multipiattaforma

_Questa procedura dettagliata viene illustrato come creare un nuovo progetto per iOS e Android utilizzando MonoGame. Il risultato è un Visual Studio per la soluzione Mac con un progetto di codice condiviso multipiattaforma, nonché un unico progetto per ogni piattaforma. Questo progetto verrà visualizzato un errore irreversibile vuoto quando eseguita._

MonoGame consente lo sviluppo di giochi multipiattaforma con gran parte di riutilizzo del codice. Questa procedura dettagliata sarà incentrata su come configurare una soluzione contenente progetti per iOS e Android, nonché un progetto di codice condiviso per il codice multipiattaforma.

Al termine, viene dispone di un progetto con la struttura appropriata per l'esecuzione della logica di aggiornamento del gioco e gioco logica di disegno 30 frame al secondo. E può essere utilizzato come progetto di base per qualsiasi progetto MonoGame. Il progetto sarà simile al seguente quando eseguita:

![](part1-images/image1.png "Il progetto sarà simile al seguente quando eseguita")


# <a name="adding-monogame-to-visual-studio-for-mac"></a>Aggiunta di MonoGame a Visual Studio per Mac

MonoGame possono essere aggiunti come un componente aggiuntivo per Visual Studio per Mac. In Mac, selezionare **Visual Studio per Mac** > **Gestione componenti aggiuntivi...**  . In Windows, gli strumenti di select * * * * > **Gestione componenti aggiuntivi...**  . Selezionare il **raccolta** scheda, espandere il **lo sviluppo di giochi** categoria e selezionare **MonoGame Addin**, quindi fare clic su **installare**:

![](part1-images/image2.png "Selezionare la scheda di raccolta, espandere la categoria di sviluppo di giochi e selezionare MonoGame del componente aggiuntivo, quindi fare clic su Installa")

> [!IMPORTANT]
> **Nota**: se il **allo sviluppo di giochi** sezione non viene visualizzata in Gestione aggiuntivo, è possibile scaricare e installare la versione più recente da manualmente: http://www.monogame.net/downloads/. Si potrebbe essere necessario riavviare Visual Studio per Mac per i modelli di visualizzazione.



Una volta installato, i modelli di MonoGame apparirà in Visual Studio per Mac, come verrà illustrato nella sezione successiva.


# <a name="creating-a-new-solution"></a>Creazione di una nuova soluzione

In Visual Studio per selezionare Mac **File > nuova soluzione**. Nel **nuovo progetto** finestra di dialogo, fare clic su **varie**, scorrere verso il **generale** sezione, selezionare il * * applicazione universale per dispositivi mobili MonoGame * * opzione e fare clic su Avanti.

![](part1-images/image3.png "Nella finestra di dialogo Nuovo progetto, fare clic su varie, scorrere fino alla sezione generale, seleziona l'opzione di applicazione Universal MonoGame Mobile e fare clic su Avanti")

Denominare il progetto WalkingGame e fare clic su Crea:

![](part1-images/image4.png "Denominare il progetto WalkingGame e fare clic su Crea")

A questo punto del progetto verrà eseguite come altri progetto Android o iOS. Il progetto deve essere eseguita la visualizzazione di uno sfondo blu fiordaliso:

![](part1-images/image5.png "Il progetto deve essere eseguita la visualizzazione di uno sfondo blu fiordaliso")


# <a name="fixing-android-compile-errors"></a>Correzione degli errori di compilazione Android

La versione corrente dei modelli del MonoGame include alcuni errori di sintassi in di Android `Activity1.cs` file. Per risolvere questi problemi, sostituire il `OnCreate` funzione con le operazioni seguenti:


```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    var g = new Game1();
    SetContentView((View)g.Services.GetService(typeof(View)));
    g.Run();
}
```


# <a name="summary"></a>Riepilogo

Questa procedura dettagliata viene descritto come creare un progetto di MonoGame multipiattaforma usando Visual Studio per Mac. Il risultato è un errore irreversibile vuoto. Questo progetto può essere utilizzato come punto di partenza per iOS e Android gioco.

## <a name="related-links"></a>Collegamenti correlati

- [MonoGame Android NuGet](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [IOS MonoGame NuGet](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [Documentazione MonoGame API](http://www.monogame.net/documentation/?page=main)
