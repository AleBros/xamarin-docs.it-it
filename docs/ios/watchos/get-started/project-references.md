---
title: Riferimenti del progetto watchos in Novell
description: Questo documento descrive la relazione tra un'app iOS, un'app Watch e un'estensione dell'app Watch. Vengono illustrati i riferimenti al progetto e gli identificatori del bundle.
ms.prod: xamarin
ms.assetid: C366E062-C33D-406A-B3FF-CBE82E5D1E7E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: 9108f83d76e07d12d70b400075142d9c73519716
ms.sourcegitcommit: 3d21bb1a6d9b78b65aa49917b545c39d44aa3e3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70065600"
---
# <a name="watchos-project-references-in-xamarin"></a>Riferimenti del progetto watchos in Novell

_Spiegazione della relazione tra l'app iOS, l'app Watch e l'estensione Watch._

I tre progetti in una soluzione watchos vengono *automaticamente* configurati per fare riferimento reciprocamente in modo specifico per la compilazione e il raggruppamento delle app watchos 3. Questi riferimenti al progetto e le impostazioni dell'identificatore del bundle sono descritti di seguito per riferimento.

## <a name="project-references"></a>Riferimenti al progetto

Per visualizzare i riferimenti, fare doppio clic sui nodi riferimenti per ogni progetto:

- App di **iPhone** riferimenti a **espressioni di controllo**

  ![](project-references-images/catalog-reference1.png "App di iPhone riferimenti a espressioni di controllo")

- Espressioni di **controllo riferimenti app** **Controlla estensione app**

  ![](project-references-images/catalog-reference2.png "App di iPhone riferimenti a espressioni di controllo")


- L' **estensione Watch app** non fa riferimento ad alcun altro progetto

  ![](project-references-images/catalog-reference3.png "L'estensione dell'app Watch non fa riferimento ad altri progetti")



## <a name="bundle-identifiers"></a>Identificatori bundle

È anche necessario assicurarsi che gli identificatori del **bundle** siano corretti.
Tutti e tre i progetti devono avere lo *stesso* prefisso identificatore, con i due progetti Watch con estensioni predefinite `watchkitextension` di `watchkitapp`e, come indicato di seguito (per l'esempio **WatchKitCatalog** ):

- Progetto unificato Novell. iOS-`com.xamarin.WatchKitCatalog`

- Progetto di estensione WatchKit-`com.xamarin.WatchKitCatalog.watchkitextension`

- Guarda il progetto dell'app-`com.xamarin.WatchKitCatalog.watchkitapp`

Assicurarsi inoltre che le impostazioni **info. plist** siano corrette:

- Il progetto `WKCompanionAppBundleIdentifier` dell'app Watch corrisponde all'ID bundle dell'app padre/contenitore (IE. quello che viene eseguito nell'iPhone);

- L' **ID bundle WKApp** del progetto dell'estensione Watch Kit corrisponde all'ID bundle del progetto dell'app Watch.

È possibile modificare gli identificatori facendo doppio clic sul file **info. plist** in ogni progetto.

Questa schermata è il file INFO. plist dell' **estensione Watch** , che mostra anche l'identificatore dell' **app Watch** :

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

![](project-references-images/infoplist-extension.png "Questa schermata è il file INFO. plist dell'estensione Watch")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

![](project-references-images/infoplist-extension-vs.png "Questa schermata è il file INFO. plist dell'estensione Watch")

-----

Questa schermata è il file INFO. plist dell' **app Watch** .
La versione corrente del **sistema operativo Watch** è 8,2, quindi la **destinazione di distribuzione** per l'App Watch deve essere **8,2**. Si noti che se è installato Xcode 6,3, questo valore potrebbe essere impostato su 8,3. è necessario modificarlo 8,2.

![](project-references-images/infoplist-watchapp.png "File Watch info. plist")

La destinazione di distribuzione per l'app Watch può essere diversa dall'estensione Watch e dall'app iOS.

