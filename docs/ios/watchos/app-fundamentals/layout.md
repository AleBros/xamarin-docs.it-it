---
title: Utilizzo di watchOS Layout di Xamarin
description: Questo documento descrive come creare un layout di watchOS con Xamarin. Illustra i controller di interfaccia, gruppi, separatori e i controlli del contenuto.
ms.prod: xamarin
ms.assetid: BEDB62A1-2249-4459-986F-413A41E63DF0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 9a9bec364990f683a59e6ddce1a536950cdf3861
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61392690"
---
# <a name="working-with-watchos-layout-in-xamarin"></a>Utilizzo di watchOS Layout di Xamarin

Progettazione di layout per l'Apple Watch [dimensioni di schermi](~/ios/watchos/app-fundamentals/screen-sizes.md) presenta sfide univoche.

## <a name="design-tips"></a>Suggerimenti relativi alla progettazione

Il punto chiave è: apportare all'interfaccia utente facilmente leggibile e facile da usare in una schermata di piccole espressioni di controllo, con un dito di grandi dimensioni. Non rientrano nell'errore di progettazione per il **simulatore iOS** (visualizzata molto grande) e una **puntatore del mouse** (che funziona con le destinazioni piccolo tocco).

- Usare uno sfondo nero, che crea l'illusione di uno schermo più grande con nero dell'orologio.

- Non è riempire tutto il layout della schermata: il pannello costituisce un riempimento visual naturale.

- Concentrarsi sulla leggibilità. Usare le dimensioni dei caratteri e colori con cautela per assicurarsi che il testo è leggibile. Usare gli stili del testo incorporato per ottenere supporto automatico di tipo dinamico.

![](layout-images/type.png "Esempio di supporto di tipo dinamico")

- Concentrarsi sulle dimensioni di destinazione di tocco. Pulsanti/tappable righe della tabella con le etichette di testo devono estendersi l'intero schermo. Apple afferma "mai inserire più di tre elementi side-by-side", e se si usa le icone e non le etichette di testo.

- Usare la [ `Menu` controllo](~/ios/watchos/user-interface/menu.md) alla funzionalità espongono meno usato per mantenere la progettazione di app chiara e concisa.


## <a name="implementation"></a>Implementazione

Guarda il che Kit comprende i seguenti controlli per la compilazione di espressioni di controllo interessante layout dell'app:

### <a name="interface-controller"></a>Controller di interfaccia

Il `WKInterfaceController` è la classe di base tutte le quinte.

Area di progettazione per il controller di interfaccia si comporta come un parametro vertical **gruppo**: è possibile trascinare altri controlli nel controller di interfaccia e saranno automaticamente disposto uno sopra l'altro:

![](layout-images/controller-scene.png "I controlli vengono automaticamente disposto uno sopra l'altro")

È possibile impostare il **posizione** e **dimensioni** proprietà ogni controllo per controllare l'aspetto:

![](layout-images/positionsize-attributes.png "Impostare le proprietà di posizione e le dimensioni in ogni controllo")

Quando la dimensione è impostata su **relativo al contenitore** è possibile fornire una regolazione dell'offset e un valore proporzionale. Questo screenshot Mostra un pulsante che è stato impostato per usare l'80% della larghezza della schermata espressioni di controllo (**0.8**):

![](layout-images/button-attributes.png "Fornire un valore proporzionale e una regolazione dell'offset")


### <a name="group"></a>Raggruppa

`WKInterfaceGroup` è un contenitore di layout semplice che può essere configurato per stack controlla orizzontalmente o verticalmente. Include la spaziatura tra ogni controllo per impostazione predefinita, ma è possibile modificare la spaziatura (e Inset) nei **attributi** inspector.

![](layout-images/group-attributes.png "Modificare la spaziatura e margini in Attributes inspector")

I gruppi possono stessi dimensionati e posizionati in relazione i controlli attorno a esse e i gruppi possono essere nidificati per creare layout complessi.

![](layout-images/group-scene.png "I gruppi possono essere nidificati per creare layout complessi")


### <a name="separator"></a>Separatore

Il controllo separatore deve consentire di fornire indicazioni visive nel layout. Usare i separatori (o i colori di sfondo o le immagini) per consentire all'utente di comprendere quale contenuto viene correlato sullo schermo.

![](layout-images/separator-scene.png "Esempio di utilizzo di separatori")

Si noti il blu e verde il separatore che non usano l'intera larghezza dello schermo sono stato configurato con una **Fixed** oppure **relativo al contenitore** dimensioni.

### <a name="content-controls"></a>Controlli del contenuto

Nessun layout sarebbe incompleta senza il `Label`, `Image`, `Button`, `Switch`, `Slider`, `Map`, e [altri controlli](~/ios/watchos/user-interface/index.md).
Questi possono essere posizionati nei layout usando **gruppi** o le impostazioni di dimensioni e posizione su ogni controllo.



## <a name="related-links"></a>Collegamenti correlati

- [WatchKitCatalog (esempio)](https://developer.xamarin.com/samples/monotouch/watchOS/WatchKitCatalog/)
- [Riferimento di Layout di Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Layout.html)
- [Fare riferimento a colore & funzionalità tipografiche di Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/ColorandTypography.html)
