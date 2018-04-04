---
title: Riferimenti al progetto
description: Spiegazione della relazione tra l'app iOS, espressioni di controllo app e l'estensione di espressioni di controllo.
ms.prod: xamarin
ms.assetid: C366E062-C33D-406A-B3FF-CBE82E5D1E7E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 09/13/2016
ms.openlocfilehash: f3573e8b578ca567ea9d7360eb132aead4c24f37
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="project-references"></a>Riferimenti al progetto

_Spiegazione della relazione tra l'app iOS, espressioni di controllo app e l'estensione di espressioni di controllo._

I tre progetti in una soluzione watchOS sono *configurata automaticamente* per fare riferimento a loro in modo specifico per le app watchOS 3 essere compilato e collegate correttamente. Questi riferimenti al progetto e impostazioni dell'identificatore bundle sono descritte di seguito per riferimento.

## <a name="project-references"></a>Riferimenti al progetto

Visualizzare i riferimenti facendo doppio clic sui nodi di riferimenti per ogni progetto:

- **app iPhone** riferimenti **App Watch**

![](project-references-images/catalog-reference1.png "app iPhone fa riferimento a espressioni di controllo App")

- **Guardare App** riferimenti **espressioni di controllo dell'estensione App**

![](project-references-images/catalog-reference2.png "app iPhone fa riferimento a espressioni di controllo App")


 - Il **espressioni di controllo dell'estensione App** non fa riferimento a uno degli altri progetti

![](project-references-images/catalog-reference3.png "Estensione dell'App espressioni di controllo non fa riferimento ad altri progetti")



## <a name="bundle-identifiers"></a>Identificatori di raggruppamento

È inoltre necessario assicurarsi che il **identificatori Bundle** siano corretti.
Tutti i tre progetti devono avere il *stesso* prefisso dell'identificatore, con i progetti di due espressioni di controllo con predefiniti estensioni di `watchkitextension` e `watchkitapp`, come indicato di seguito (per il **WatchKitCatalog** ad esempio):

 - Progetto unificato di xamarin. IOS- `com.xamarin.WatchKitCatalog`

 - Progetto di estensione WatchKit- `com.xamarin.WatchKitCatalog.watchkitextension`

 - Progetto di App - espressioni di controllo `com.xamarin.WatchKitCatalog.watchkitapp`

Assicurarsi anche che tali **Info. plist** impostazioni siano corrette:

 - Il progetto di App Watch `WKCompanionAppBundleIdentifier` corrisponda all'ID Bundle dell'applicazione contenitore/padre (ie. è in esecuzione su iPhone);

 - Il progetto di estensione Kit Watch **ID Bundle WKApp** corrispondente di ID di pacchetto. del progetto di applicazione di espressioni di controllo

È possibile modificare gli identificatori facendo doppio clic su di **Info. plist** file in ogni progetto.

Questa schermata è il **dell'estensione di espressioni di controllo** file Info. plist, che mostra il **espressioni di controllo dell'App** anche identificatore:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)
    
![](project-references-images/infoplist-extension.png "Questa schermata è file Info. plist dell'estensione di espressioni di controllo")

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)
    
![](project-references-images/infoplist-extension-vs.png "Questa schermata è file Info. plist dell'estensione di espressioni di controllo")

-----

Questa schermata è il **espressioni di controllo dell'App** file Info. plist.
Corrente **OS Watch** versione è 8.2, pertanto la **destinazione distribuzione** per l'applicazione di espressioni di controllo deve essere **8.2**. Si noti che se si dispone di Xcode 6.3 installato, questo valore potrebbe essere impostato su 8.3, è consigliabile modificarlo 8.2.

![](project-references-images/infoplist-watchapp.png "Le espressioni di controllo file Info. plist")

La destinazione di distribuzione per l'applicazione di espressioni di controllo può essere diversa dall'estensione di espressioni di controllo e un'App iOS.

