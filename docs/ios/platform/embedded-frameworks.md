---
title: Framework incorporati in Xamarin.iOS
description: Questo documento descrive come condividere il codice con Framework incorporati in un'applicazione Xamarin.iOS. Questa operazione può essere eseguita con lo strumento mTouch o i riferimenti nativi.
ms.prod: xamarin
ms.assetid: F8C61020-4106-46F1-AECB-B56C909F42CB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/05/2018
ms.openlocfilehash: cf74c31b149c24bc6e515c0f00803a60b10d5d1c
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032541"
---
# <a name="embedded-frameworks-in-xamarinios"></a>Framework incorporati in Xamarin.iOS

_Questo documento descrive in che modo gli sviluppatori di applicazioni possono incorporare i Framework utente nelle proprie app._

Con iOS 8,0 Apple ha reso possibile la creazione di un Framework incorporato per condividere il codice tra le estensioni dell'app e l'app principale in Xcode.

Xamarin.iOS 9,0 aggiunge il supporto per l'utilizzo di questi Framework incorporati (creati con Xcode) nelle app Xamarin.iOS. ***Non** sarà possibile creare Framework incorporati da qualsiasi tipo di progetto Xamarin.iOS, utilizzare solo framework nativi (Objective-C) esistenti.*

Esistono due modi per utilizzare i Framework in Xamarin.iOS:

- Passare il Framework allo strumento mTouch, aggiungendo il codice seguente agli argomenti mTouch aggiuntivi nelle opzioni di **compilazione iOS** del progetto:

  ```csharp
  --framework:/Path/To/My.Framework
  ```

  Questa impostazione deve essere impostata per ogni configurazione di progetto.

- Aggiungere riferimenti nativi dal menu di scelta rapida

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Fare clic con il pulsante destro del mouse sul progetto e selezionare Aggiungi riferimenti nativi

![](embedded-frameworks-images/xam-native-refs.png "Select Add native references in Visual Studio for Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Fare clic con il pulsante destro del mouse sul progetto e selezionare Aggiungi riferimenti nativi

![](embedded-frameworks-images/vs-native-refs.png "Select Add native references in Visual Studio")

-----

  Questa operazione funzionerà per tutte le configurazioni.

Nelle future versioni di Visual Studio per Mac e Novell Tools per Visual Studio sarà possibile utilizzare i Framework dall'IDE (senza modificare manualmente i file di progetto).

È possibile trovare alcuni progetti di esempio in [GitHub](https://github.com/rolfbjarne/embedded-frameworks)

## <a name="limitations"></a>Limitazioni

- I Framework incorporati sono supportati solo nei progetti [unificati](~/cross-platform/macios/unified/index.md) .
- I Framework incorporati sono supportati solo nei progetti con una destinazione di distribuzione di almeno iOS 8,0.
- Se un'estensione richiede un Framework incorporato, l'app contenitore deve avere anche un riferimento al Framework. in caso contrario, il Framework non verrà incluso nel bundle dell'app.

## <a name="the-mono-runtime"></a>Runtime di mono

Internamente Xamarin.iOS sfrutta questa funzionalità per eseguire il collegamento al runtime di mono come Framework, anziché collegare il runtime di mono in modo statico in ogni estensione e nell'app contenitore.

Questa operazione viene eseguita automaticamente se l'app contenitore è un'app unificata, contiene estensioni e la distribuzione di destinazione è iOS 8,0 o versione successiva.

Le app senza estensioni continueranno a collegarsi in modo statico dal runtime di mono, perché esiste una riduzione delle dimensioni per l'uso di un Framework se è presente solo un'app che vi fa riferimento.

Questo comportamento può essere sostituito dallo sviluppatore di app, aggiungendo quanto segue come argomento mTouch aggiuntivo nelle opzioni di compilazione iOS del progetto:

- `--mono:static`: i collegamenti al runtime Mono in modo statico.
- `--mono:framework`: collega al runtime di mono come Framework.

Uno scenario per il collegamento con il runtime di mono come Framework anche per le app senza estensioni consiste nel ridurre le dimensioni del file eseguibile, in modo da superare le limitazioni di dimensione imposte da Apple sull'eseguibile. Per riferimento, il runtime di mono aggiunge approssimativamente 1.7 MB per architettura (a partire da Xamarin.iOS 8,12, ma varia tra le versioni e anche tra le app). Mono Framework aggiunge circa 2,3 MB per architettura, il che significa che per un'app con una singola architettura senza estensioni, il collegamento dell'app con il runtime di mono come Framework ridurrà il file eseguibile di ~ 1.7 MB, ma aggiungerà un Framework ~ 2,3 MB, risultante in una complessivamente di ~ 0.6 MB di app di dimensioni maggiori.
