---
title: Framework incorporati in xamarin. IOS
description: Questo documento viene descritto come condividere il codice con Framework incorporati in un'applicazione xamarin. IOS. Questa operazione può essere eseguita con lo strumento mtouch o riferimenti nativi.
ms.prod: xamarin
ms.assetid: F8C61020-4106-46F1-AECB-B56C909F42CB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/05/2018
ms.openlocfilehash: b59fd7c1a9e5f528878b90e1a76fabe5a79bab81
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50108242"
---
# <a name="embedded-frameworks-in-xamarinios"></a>Framework incorporati in xamarin. IOS

_Questo documento descrive come gli sviluppatori di applicazioni possono incorporare Framework utente nelle proprie app._

Con iOS 8.0 Apple è stato possibile creare un framework incorporato per condividere codice tra le estensioni app e l'app principale in Xcode.

Xamarin. IOS 9.0 aggiunge il supporto per l'utilizzo di questi Framework incorporati (creati con Xcode) nelle App xamarin. IOS. *Verrà **non** è possibile creare Framework incorporati da qualsiasi tipo di progetti xamarin. IOS, utilizzano solo Framework (Objective-C) native esistenti.*

Esistono due modi per utilizzare Framework in xamarin. ios:

- Passare il framework per lo strumento mtouch, aggiungendo il codice seguente per gli argomenti aggiuntivi di mtouch del progetto **compilazione iOS** opzioni:

  ```csharp
  --framework:/Path/To/My.Framework
  ```

  Deve essere impostata per ogni configurazione di progetto.

- Aggiungere i riferimenti nativi dal menu di scelta rapida

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Pulsante destro del mouse sul progetto e passare per aggiungere i riferimenti nativi

![](embedded-frameworks-images/xam-native-refs.png "Selezionare Aggiungi riferimenti nativi in Visual Studio per Mac")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Pulsante destro del mouse sul progetto e passare per aggiungere i riferimenti nativi

![](embedded-frameworks-images/vs-native-refs.png "Selezionare Aggiungi riferimenti nativi in Visual Studio")

-----

  Questa tecnica funziona per tutte le configurazioni.

Nelle versioni future di Visual Studio per Mac e gli strumenti Xamarin per Visual Studio sarà possibile usare Framework da all'interno dell'IDE (senza dover modificare manualmente i file di progetto).

Alcuni progetti di esempio sono reperibili nel [github](https://github.com/rolfbjarne/embedded-frameworks)

## <a name="limitations"></a>Limitazioni

- Framework incorporati sono supportati solo nelle [unificato](~/cross-platform/macios/unified/index.md) progetti.
- Framework incorporati sono supportati solo nei progetti a una destinazione di distribuzione di almeno iOS 8.0.
- Se un'estensione richiede un framework incorporato, quindi l'app contenitore deve avere anche un riferimento a framework, in caso contrario, che il framework non verrà incluso nel bundle dell'app.

## <a name="the-mono-runtime"></a>Il runtime di Mono

Internamente xamarin. IOS consente di sfruttare questa funzionalità per il collegamento con il runtime di Mono come framework, invece di collegare il runtime di Mono in modo statico in ogni estensione e l'app contenitore.

Questa operazione viene eseguita automaticamente se l'app contenitore è un'app unificato, contiene le estensioni e la distribuzione di destinazione è iOS 8.0 o versione successiva.

Le app senza estensioni del collegamento continuerà con il runtime di Mono in modo statico, perché si verifica una riduzione delle dimensioni per l'uso di un framework se è presente solo un'app farvi riferimento.

Questo comportamento può essere ignorato dallo sviluppatore dell'app, aggiungendo il codice seguente come argomento aggiuntivi di mtouch nelle opzioni di compilazione iOS del progetto:

- `--mono:static`: Collega in modo statico con il runtime di Mono.
- `--mono:framework`: Il collegamento con il runtime di Mono come framework.

Uno scenario per il collegamento con il runtime di Mono come framework anche per le app senza le estensioni è quello di ridurre le dimensioni dei file eseguibili, per ovviare a eventuali limiti di dimensioni che Apple applica sul file eseguibile. Per riferimento, il runtime di Mono aggiunge circa 1.7MB per ogni architettura (esempio di xamarin. IOS 8.12, tuttavia his varia tra le versioni e anche tra le App). Il framework Mono aggiunge circa 2.3MB per ogni architettura, che significa che per un'applicazione single-architettura senza alcuna estensione, rendendo il collegamento di app con il runtime di Mono risultante come un framework di compattare il file eseguibile da ~1.7MB, ma aggiungere un framework ~2.3MB, in ~0.6MB più grande tutte app.

