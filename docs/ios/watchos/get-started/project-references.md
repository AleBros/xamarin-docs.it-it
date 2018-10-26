---
title: riferimenti al progetto in Xamarin watchOS
description: Questo documento descrive la relazione tra un'app per iOS, un'app e un'estensione dell'app watch. Illustra i riferimenti al progetto e bundle di identificatori.
ms.prod: xamarin
ms.assetid: C366E062-C33D-406A-B3FF-CBE82E5D1E7E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 09/13/2016
ms.openlocfilehash: c900ab714fed2bb1e02367ba39ad3c5a0a76121e
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106663"
---
# <a name="watchos-project-references-in-xamarin"></a>riferimenti al progetto in Xamarin watchOS

_Spiegazione della relazione tra l'app iOS, app e l'estensione dell'espressioni di controllo._

Sono i tre progetti in una soluzione di watchOS *configurata automaticamente* per fare riferimento tra loro in modo specifico per le app watchOS 3 di essere compilati e in bundle in modo corretto. Questi riferimenti al progetto e impostazioni dell'identificatore di bundle sono descritte di seguito per riferimento.

## <a name="project-references"></a>Riferimenti al progetto

Visualizzare i riferimenti facendo doppio clic sui nodi riferimenti per ogni progetto:

- **app iPhone** riferimenti **App Watch**

![](project-references-images/catalog-reference1.png "app iPhone fa riferimento all'App Watch")

- **App di guardare** riferimenti **estensione dell'App Watch**

![](project-references-images/catalog-reference2.png "app iPhone fa riferimento all'App Watch")


 - Il **estensione dell'App Watch** non fa riferimento a uno degli altri progetti

![](project-references-images/catalog-reference3.png "Estensione dell'App Watch non fa riferimento ad altri progetti")



## <a name="bundle-identifiers"></a>Identificatori di bundle

È anche necessario assicurarsi che il **identificatori di Bundle** siano corretti.
Tutti e tre i progetti devono avere la *stessa* prefisso identificatore, con i progetti di due espressioni di controllo con predefinito delle estensioni di `watchkitextension` e `watchkitapp`, come indicato di seguito (per il **WatchKitCatalog** ad esempio):

 - Progetto unificata di xamarin. ios: `com.xamarin.WatchKitCatalog`

 - Progetto estensione WatchKit: `com.xamarin.WatchKitCatalog.watchkitextension`

 - Progetto di App Watch- `com.xamarin.WatchKitCatalog.watchkitapp`

Assicurarsi anche che questi **Info. plist** impostazioni siano corrette:

 - Il progetto di App Watch `WKCompanionAppBundleIdentifier` corrisponda all'ID Bundle dell'app/contenitore padre (ad esempio. è in esecuzione su iPhone);

 - Il progetto di estensione di Kit Watch **ID Bundle di WKApp** corrisponde l'ID Bundle del progetto App Watch

È possibile modificare gli identificatori facendo doppio clic sui **Info. plist** file in ogni progetto.

Questa schermata è il **espressioni di controllo dell'estensione** file Info. plist, che mostra le **dell'App Watch** anche identificatore:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)
    
![](project-references-images/infoplist-extension.png "Questa schermata è file Info. plist dell'estensione Watch")

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)
    
![](project-references-images/infoplist-extension-vs.png "Questa schermata è file Info. plist dell'estensione Watch")

-----

Questa schermata è il **dell'App Watch** file Info. plist.
L'oggetto corrente **Watch OS** versione è 8.2, in modo che il **destinazione di distribuzione** per l'App Watch deve essere **8.2**. Si noti che se si dispone di Xcode 6.3 installato, questo valore può essere impostato su 8.3, è consigliabile modificarlo 8.2.

![](project-references-images/infoplist-watchapp.png "Le espressioni di controllo file Info. plist")

La destinazione di distribuzione per l'App Watch possa essere diversa dalle espressioni di controllo estensione App iOS.

