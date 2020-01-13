---
title: Riferimenti del progetto watchos in Novell
description: Questo documento descrive la relazione tra un'app iOS, un'app Watch e un'estensione dell'app Watch. Vengono illustrati i riferimenti al progetto e gli identificatori del bundle.
ms.prod: xamarin
ms.assetid: C366E062-C33D-406A-B3FF-CBE82E5D1E7E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/13/2016
ms.openlocfilehash: 3dcd5f17b35b9829831adcf997d8bde97c0572e7
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030162"
---
# <a name="watchos-project-references-in-xamarin"></a>Riferimenti del progetto watchos in Novell

_Spiegazione della relazione tra l'app iOS, l'app Watch e l'estensione Watch._

I tre progetti in una soluzione watchos vengono *automaticamente configurati* per fare riferimento reciprocamente in modo specifico per la compilazione e il raggruppamento delle app watchos 3. Questi riferimenti al progetto e le impostazioni dell'identificatore del bundle sono descritti di seguito per riferimento.

## <a name="project-references"></a>Riferimenti al progetto

Per visualizzare i riferimenti, fare doppio clic sui nodi riferimenti per ogni progetto:

- App di **iPhone** riferimenti a **espressioni di controllo**

  ![](project-references-images/catalog-reference1.png "iPhone app references Watch App")

- Espressioni di **controllo riferimenti app** **Controlla estensione app**

  ![](project-references-images/catalog-reference2.png "iPhone app references Watch App")

- L' **estensione Watch app** non fa riferimento ad alcun altro progetto

  ![](project-references-images/catalog-reference3.png "Watch App Extension does not reference the other projects")

## <a name="bundle-identifiers"></a>Identificatori bundle

È anche necessario assicurarsi che gli **identificatori del bundle** siano corretti.
Tutti e tre i progetti devono avere lo *stesso* prefisso identificatore, con i due progetti Watch con estensioni predefinite di `watchkitextension` e `watchkitapp`, come indicato di seguito (per l'esempio **WatchKitCatalog** ):

- Progetto unificato Xamarin.iOS-`com.xamarin.WatchKitCatalog`

- Progetto di estensione WatchKit-`com.xamarin.WatchKitCatalog.watchkitextension`

- Guarda il progetto dell'app-`com.xamarin.WatchKitCatalog.watchkitapp`

Assicurarsi inoltre che le impostazioni **info. plist** siano corrette:

- Il `WKCompanionAppBundleIdentifier` del progetto di app Watch corrisponde all'ID bundle dell'app padre/contenitore (ad esempio, quello che viene eseguito nell'iPhone);

- L' **ID bundle WKApp** del progetto dell'estensione Watch Kit corrisponde all'ID bundle del progetto dell'app Watch.

È possibile modificare gli identificatori facendo doppio clic sul file **info. plist** in ogni progetto.

Questa schermata è il file INFO. plist dell' **estensione Watch** , che mostra anche l'identificatore dell' **app Watch** :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![](project-references-images/infoplist-extension.png "This screenshot is the Watch Extension's Info.plist file")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](project-references-images/infoplist-extension-vs.png "This screenshot is the Watch Extension's Info.plist file")

-----

Questa schermata è il file INFO. plist dell' **app Watch** .
La versione corrente del **sistema operativo Watch** è 8,2, quindi la **destinazione di distribuzione** per l'App Watch deve essere **8,2**. Si noti che se è installato Xcode 6,3, questo valore potrebbe essere impostato su 8,3. è necessario modificarlo 8,2.

![](project-references-images/infoplist-watchapp.png "The watch Info.plist file")

La destinazione di distribuzione per l'app Watch può essere diversa dall'estensione Watch e dall'app iOS.
