---
title: 'Parte 1: creazione di un monogame multipiattaforma'
description: Questa procedura dettagliata illustra come creare un nuovo progetto per iOS e Android usando monogame. Il risultato è una soluzione di Visual Studio per Mac con un progetto di codice condiviso multipiattaforma e un progetto per ogni piattaforma. Questo progetto visualizzerà una schermata blu vuota quando viene eseguita.
ms.prod: xamarin
ms.assetid: FC69E69B-04D4-45DF-9BBF-2A6CDEAD9B2F
author: conceptdev
ms.author: crdun
ms.date: 03/28/2017
ms.openlocfilehash: d72c428bb4b8c88365180c5c3c50b107eed2b21d
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "68978450"
---
# <a name="part-1--creating-a-cross-platform-monogame"></a>Parte 1: creazione di un monogame multipiattaforma

_Questa procedura dettagliata illustra come creare un nuovo progetto per iOS e Android usando monogame. Il risultato è una soluzione di Visual Studio per Mac con un progetto di codice condiviso multipiattaforma e un progetto per ogni piattaforma. Questo progetto visualizzerà una schermata blu vuota quando viene eseguita._

Monogame consente lo sviluppo di giochi multipiattaforma con grande parte del riutilizzo del codice. Questa procedura dettagliata si concentra sulla configurazione di una soluzione che contiene progetti per iOS e Android, nonché un progetto di codice condiviso per il codice multipiattaforma.

Al termine, il progetto ha la struttura corretta per eseguire la logica di aggiornamento del gioco e la logica di disegno del gioco a 30 fotogrammi al secondo. Può essere usato come progetto di base per qualsiasi progetto MonoGame. Il progetto sarà simile al seguente quando viene eseguito:

![Schermata blu vuota](part1-images/image1.png)

## <a name="adding-monogame-to-visual-studio"></a>Aggiunta di monogame a Visual Studio

> [!IMPORTANT]
> Monogame non è installato per impostazione predefinita in Visual Studio 2019 o Visual Studio per Mac.
>
> È necessario scaricare e installare manualmente la versione più recente http://www.monogame.net/downloads/ di, quindi eseguire il programma di installazione. Potrebbe essere necessario riavviare Visual Studio per visualizzare i modelli.
>
> La sezione **sviluppo del gioco** dovrebbe quindi essere visualizzata nella **Gestione componenti**aggiuntivi.

Per abilitare il componente aggiuntivo monogame per Visual Studio per Mac, selezionare **Visual Studio per Mac** > **Gestione componenti aggiuntivi...** . Per Visual Studio 2019 in Windows, selezionare **strumenti** > **Gestione componenti aggiuntivi...** . Selezionare la scheda **raccolta** , espandere la categoria **sviluppo gioco** e selezionare **componente aggiuntivo monogame**, quindi fare clic su **Installa...** :

![Raccolta di estensioni Visual Studio per Mac selezione di monogame](part1-images/image2.png)

Una volta installato, i modelli monogame verranno visualizzati in Visual Studio per Mac, come si vedrà nella sezione successiva.

## <a name="creating-a-new-solution"></a>Creazione di una nuova soluzione

In Visual Studio per Mac selezionare **File > nuova soluzione**. Nella finestra di dialogo **nuovo progetto** fare clic su **varie**, scorrere fino alla sezione **generale** , selezionare l'opzione **applicazione universale monogame per dispositivi mobili** e fare clic su Avanti.

![Finestra di dialogo nuovo progetto Creazione di un'applicazione monogame](part1-images/image3.png)

Denominare il progetto WalkingGame e fare clic su Crea:

![Finestra di dialogo nuovo progetto selezione di un nome e un percorso](part1-images/image4.png)

Il progetto verrà ora eseguito esattamente come qualsiasi altro progetto iOS o Android. Il progetto deve essere eseguito visualizzando uno sfondo blu fiordaliso:

![Sfondo app blu vuota](part1-images/image5.png)

## <a name="fixing-android-compile-errors"></a>Correzione degli errori di compilazione di Android

La versione corrente dei modelli monogame include alcuni errori di sintassi nel `Activity1.cs` file di Android. Per risolvere questi problemi, sostituire la `OnCreate` funzione con il codice seguente:

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

Questa procedura dettagliata ha illustrato come creare un progetto MonoGame multipiattaforma usando Visual Studio per Mac. Il risultato è una schermata blu vuota. Questo progetto può essere usato come punto di partenza per qualsiasi gioco iOS e Android.

## <a name="related-links"></a>Collegamenti correlati

- [NuGet Android monogame](https://www.nuget.org/packages/MonoGame.Framework.Android/)
- [NuGet iOS monogame](https://www.nuget.org/packages/MonoGame.Framework.iOS/)
- [Documentazione dell'API monogame](http://www.monogame.net/documentation/?page=main)
