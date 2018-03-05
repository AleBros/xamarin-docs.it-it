---
title: Framework incorporato
description: Questo documento descrive come gli sviluppatori di applicazioni possono incorporare Framework utente nelle proprie app.
ms.topic: article
ms.prod: xamarin
ms.assetid: F8C61020-4106-46F1-AECB-B56C909F42CB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 62ddf665431a14ce7f4fe8db52cc6ee7a2c4635a
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="embedded-frameworks"></a>Framework incorporato

_Questo documento descrive come gli sviluppatori di applicazioni possono incorporare Framework utente nelle proprie app._

Con iOS 8.0 Apple è stato possibile creare un framework incorporato per condividere il codice tra le estensioni app e l'app principale in Xcode.

Xamarin. IOS 9.0 aggiunge il supporto per l'utilizzo di questi Framework incorporati (creati con Xcode) in App xamarin. IOS. *Si verifica un **non** possibile creare un framework incorporato da qualsiasi tipo di progetti di xamarin. IOS, utilizzano solo le infrastrutture esistenti (Objective-C) native.*

Esistono due modi per utilizzare Framework di xamarin:

- Passare il framework allo strumento mtouch, aggiungere il codice seguente per gli argomenti aggiuntivi mtouch del progetto **compilazione iOS** opzioni:

  ```csharp
  --framework:/Path/To/My.Framework
  ```

  È possibile impostare per ogni configurazione di progetto.

- Aggiungere i riferimenti nativi dal menu di scelta rapida

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Fare clic sul progetto e passare per aggiungere i riferimenti nativi

![](embedded-frameworks-images/xam-native-refs.png "Selezionare i riferimenti nativi componente in Visual Studio per Mac")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Fare clic sul progetto e passare per aggiungere i riferimenti nativi

![](embedded-frameworks-images/vs-native-refs.png "Selezionare i riferimenti nativi componente in Visual Studio")

-----

  Questa tecnica funziona per tutte le configurazioni.

Nelle versioni future di Visual Studio per Mac e gli strumenti di Xamarin per Visual Studio sarà possibile utilizzare Framework all'interno dell'IDE (senza modificare manualmente i file di progetto).

Alcuni progetti di esempio possono trovarsi in [github](https://github.com/rolfbjarne/embedded-frameworks)

## <a name="limitations"></a>Limitazioni

- Framework incorporati sono supportati solo nelle [unificato](~/cross-platform/macios/unified/index.md) progetti.
- Framework incorporati sono supportati solo nei progetti a una destinazione di distribuzione di almeno iOS 8.0.
- Se un'estensione richiede un framework incorporato, quindi l'applicazione contenitore deve avere anche un riferimento al framework, in caso contrario che il framework non verrà incluso nel bundle di app.

## <a name="the-mono-runtime"></a>Il runtime Mono

Internamente xamarin consente di sfruttare questa funzionalità per il collegamento con il runtime Mono come un framework, anziché di quello di runtime Mono in modo statico in ogni estensione e l'applicazione contenitore.

Questa operazione viene eseguita automaticamente se l'applicazione contenitore è un'applicazione di unificato, contiene le estensioni e la distribuzione di destinazione è iOS 8.0 o versioni successive.

App senza estensioni verranno comunque il collegamento con il runtime Mono in modo statico, poiché non esiste una riduzione delle dimensioni per l'utilizzo di un framework se è presente solo un'app farvi riferimento.

Questo comportamento può essere ignorato dallo sviluppatore di app, aggiungere il codice seguente come argomento mtouch aggiuntive in iOS del progetto le opzioni di compilazione:

- `--mono:static`: Collegata in modo statico con il runtime Mono.
- `--mono:framework`: I collegamenti con il runtime Mono come un framework.

Uno scenario per il collegamento con il runtime Mono come un framework anche per le app senza estensioni è quello di ridurre le dimensioni dei file eseguibili, per risolvere eventuali limiti di dimensioni che Apple applica sull'eseguibile. Per riferimento, il runtime Mono aggiunge circa 1.7MB per l'architettura (come di xamarin. IOS 8.12, tuttavia il suo varia tra le versioni e anche tra le App). Il framework Mono aggiunge circa 2,3 MB per architettura, che significa che per un'applicazione single-architettura senza alcuna estensione, effettua il collegamento all'app con il runtime Mono risultante come un framework di compattare il file eseguibile da ~1.7MB, ma aggiungere un framework ~2.3MB, in ~0.6MB più grande tutte app.

