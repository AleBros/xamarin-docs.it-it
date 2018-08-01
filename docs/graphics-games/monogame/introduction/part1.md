---
title: 'Parte 1: creazione di un MonoGame multipiattaforma'
description: Questa procedura dettagliata viene illustrato come creare un nuovo progetto per iOS e Android utilizzando MonoGame. Il risultato è un Visual Studio per la soluzione Mac con un progetto di codice condiviso multipiattaforma, nonché un unico progetto per ogni piattaforma. Questo progetto verrà visualizzato un errore irreversibile vuoto quando eseguita.
ms.prod: xamarin
ms.assetid: FC69E69B-04D4-45DF-9BBF-2A6CDEAD9B2F
author: charlespetzold
ms.author: chape
ms.date: 03/28/2017
ms.openlocfilehash: bd7990b94e678c205f9ce636f4eb0d28180fc6ec
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
ms.locfileid: "33921989"
---
# <a name="part-1--creating-a-cross-platform-monogame"></a>Parte 1: creazione di un MonoGame multipiattaforma

_Questa procedura dettagliata viene illustrato come creare un nuovo progetto per iOS e Android utilizzando MonoGame. Il risultato è un Visual Studio per la soluzione Mac con un progetto di codice condiviso multipiattaforma, nonché un unico progetto per ogni piattaforma. Questo progetto verrà visualizzato un errore irreversibile vuoto quando eseguita._

MonoGame consente lo sviluppo di giochi multipiattaforma con gran parte di riutilizzo del codice. Questa procedura dettagliata sarà incentrata su come configurare una soluzione contenente progetti per iOS e Android, nonché un progetto di codice condiviso per il codice multipiattaforma.

Al termine, viene dispone di un progetto con la struttura appropriata per l'esecuzione della logica di aggiornamento del gioco e gioco logica di disegno 30 frame al secondo. E può essere utilizzato come progetto di base per qualsiasi progetto MonoGame. Il progetto sarà simile al seguente quando eseguita:

![Schermata di blu vuota](part1-images/image1.png)

## <a name="adding-monogame-to-visual-studio-for-mac"></a>Aggiunta di MonoGame a Visual Studio per Mac

MonoGame possono essere aggiunti come un componente aggiuntivo per Visual Studio per Mac. In Mac, selezionare **Visual Studio per Mac** > **Gestione componenti aggiuntivi...**  . In Windows, gli strumenti di select * * * * > **Gestione componenti aggiuntivi...**  . Selezionare il **raccolta** scheda, espandere il **lo sviluppo di giochi** categoria e selezionare **MonoGame Addin**, quindi fare clic su **installare**:

![Visual Studio per la raccolta di estensioni Mac selezionando MonoGame](part1-images/image2.png)

> [!IMPORTANT]
> **Nota**: se il **allo sviluppo di giochi** sezione non viene visualizzata in Gestione aggiuntivo, è possibile scaricare e installare la versione più recente da manualmente: http://www.monogame.net/downloads/. Si potrebbe essere necessario riavviare Visual Studio per Mac per i modelli di visualizzazione.

Una volta installato, i modelli di MonoGame apparirà in Visual Studio per Mac, come verrà illustrato nella sezione successiva.

## <a name="creating-a-new-solution"></a>Creazione di una nuova soluzione

In Visual Studio per selezionare Mac **File > nuova soluzione**. Nel **nuovo progetto** finestra di dialogo, fare clic su **varie**, scorrere verso il **generale** sezione, selezionare il * * applicazione universale per dispositivi mobili MonoGame * * opzione e fare clic su Avanti.

![Finestra Nuovo progetto, creazione di un'applicazione MonoGame](part1-images/image3.png)

Denominare il progetto WalkingGame e fare clic su Crea:

![Finestra Nuovo progetto il nome e percorso di prelievo](part1-images/image4.png)

A questo punto del progetto verrà eseguite come altri progetto Android o iOS. Il progetto deve essere eseguita la visualizzazione di uno sfondo blu fiordaliso:

![Sfondo applicazione blu vuota](part1-images/image5.png)

## <a name="fixing-android-compile-errors"></a>Correzione degli errori di compilazione Android

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

## <a name="summary"></a>Riepilogo

Questa procedura dettagliata viene descritto come creare un progetto di MonoGame multipiattaforma usando Visual Studio per Mac. Il risultato è un errore irreversibile vuoto. Questo progetto può essere utilizzato come punto di partenza per iOS e Android gioco.

## <a name="related-links"></a>Collegamenti correlati

- [MonoGame Android NuGet](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [IOS MonoGame NuGet](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [Documentazione MonoGame API](http://www.monogame.net/documentation/?page=main)
