---
title: 'Parte 1: creazione di un MonoGame multipiattaforma'
description: Questa procedura dettagliata viene illustrato come creare un nuovo progetto per iOS e Android con MonoGame. Il risultato è un Visual Studio per la soluzione Mac con un progetto di codice condiviso multipiattaforma, nonché un unico progetto per ogni piattaforma. Questo progetto visualizzerà una schermata blu vuota quando eseguita.
ms.prod: xamarin
ms.assetid: FC69E69B-04D4-45DF-9BBF-2A6CDEAD9B2F
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: 82b1408cafedf98a8619e8e039ba00b332f74516
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61381844"
---
# <a name="part-1--creating-a-cross-platform-monogame"></a>Parte 1: creazione di un MonoGame multipiattaforma

_Questa procedura dettagliata viene illustrato come creare un nuovo progetto per iOS e Android con MonoGame. Il risultato è un Visual Studio per la soluzione Mac con un progetto di codice condiviso multipiattaforma, nonché un unico progetto per ogni piattaforma. Questo progetto visualizzerà una schermata blu vuota quando eseguita._

MonoGame consente lo sviluppo di giochi multipiattaforma con gran parte del riutilizzo del codice. Questa procedura dettagliata sarà incentrata su come configurare una soluzione contenente progetti per iOS e Android, nonché un progetto di codice condiviso per il codice multipiattaforma.

Al termine, verrà dispone di un progetto con la struttura appropriata per l'esecuzione di logica di aggiornamento del gioco e giochi per la logica di disegno a 30 fotogrammi al secondo. Può essere utilizzato come progetto di base per qualsiasi progetto MonoGame. Il progetto avrà un aspetto simile al seguente quando eseguita:

![Schermata blu vuota](part1-images/image1.png)

## <a name="adding-monogame-to-visual-studio-for-mac"></a>Aggiunta di MonoGame a Visual Studio per Mac

MonoGame può essere aggiunto come un componente aggiuntivo per Visual Studio per Mac. In Mac, selezionare **Visual Studio per Mac** > **Gestione componenti aggiuntivi...**  . In Windows, selezionare * * Strumenti * * > **Gestione componenti aggiuntivi...**  . Selezionare il **Gallery** scheda, quindi espandere il **lo sviluppo di giochi** categoria e selezionare **MonoGame Addin**, quindi fare clic su **installare**:

![Visual Studio per la raccolta di estensioni di Mac selezionando MonoGame](part1-images/image2.png)

> [!IMPORTANT]
> **Nota**: Se il **lo sviluppo di giochi** sezione non viene visualizzata nella gestione aggiuntivo, è possibile scaricare e installare la versione più recente da qui manualmente: http://www.monogame.net/downloads/. Si potrebbe essere necessario riavviare Visual Studio per Mac per i modelli di visualizzazione.

Una volta installato, MonoGame modelli verranno visualizzato in Visual Studio per Mac, come vedremo nella prossima sezione.

## <a name="creating-a-new-solution"></a>Creare una nuova soluzione

In Visual Studio per selezionare Mac **File > nuova soluzione**. Nel **nuovo progetto** finestra di dialogo, fare clic su **varie**, scorrere fino al **generale** selezionare la * * applicazione universale per dispositivi mobili MonoGame * *, l'opzione e fare clic su Avanti.

![Finestra Nuovo progetto, creazione di un'applicazione MonoGame](part1-images/image3.png)

Denominare il progetto WalkingGame e fare clic su Crea:

![Finestra Nuovo progetto scegliere un nome e percorso](part1-images/image4.png)

A questo punto il progetto verrà eseguito esattamente come qualsiasi altro iOS o Android project. Il progetto deve essere eseguito visualizzare uno sfondo blu fiordaliso:

![In background app blu vuota](part1-images/image5.png)

## <a name="fixing-android-compile-errors"></a>Correzione di errori di compilazione Android

La versione corrente dei modelli del MonoGame include alcuni errori di sintassi nella finestra di Android `Activity1.cs` file. Per risolvere questi problemi, sostituire il `OnCreate` funzione con il codice seguente:

```csharp
protected override void OnCreate (Bundle bundle)
{
    base.OnCreate (bundle);

    var g = new Game1();
    SetContentView((View)g.Services.GetService(typeof(View)));
    g.Run();
}
```

## <a name="summary"></a>Riepilogo

Questa procedura dettagliata viene descritto come creare un progetto MonoGame multipiattaforma usando Visual Studio per Mac. Il risultato di questo oggetto è una schermata blu vuota. Questo progetto può essere utilizzato come punto di partenza per qualsiasi iOS e Android gioco.

## <a name="related-links"></a>Collegamenti correlati

- [NuGet MonoGame Android](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [MonoGame iOS NuGet](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [Documentazione API MonoGame](http://www.monogame.net/documentation/?page=main)
