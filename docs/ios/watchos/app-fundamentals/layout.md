---
title: Utilizzo di Layout
ms.topic: article
ms.prod: xamarin
ms.assetid: BEDB62A1-2249-4459-986F-413A41E63DF0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: 71a192343800dc11f46c645c27ab8a74209c92e6
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="working-with-layout"></a>Utilizzo di Layout

Progettazione di layout per la Apple Watch [schermata dimensioni](~/ios/watchos/app-fundamentals/screen-sizes.md) presenta difficoltà specifiche.

## <a name="design-tips"></a>Suggerimenti sulla progettazione

Il punto chiave è: rendere l'interfaccia utente leggibile e utilizzabile in una schermata di espressioni di controllo piccole, con un dito di grandi dimensioni. Non rientrano nell'errore di progettazione per il **simulatore iOS** (visualizzata molto grande) e un **puntatore** (funziona con le destinazioni di piccoli tocco)!

- Utilizzare un sfondo nero, che crea l'illusione di uno schermo più grande con nero dell'orologio.

- Non riempire intorno il layout della schermata: la cornice costituisce un riempimento visual naturale.

- Concentrarsi sulla leggibilità. Usare cautela le dimensioni dei caratteri e colori per garantire il testo è leggibile. Utilizzare gli stili di testo incorporato per ottenere supporto automatico di tipo dinamico.

![](layout-images/type.png "Esempio di supporto di tipo dinamico")

- Concentrarsi sulle dimensioni di destinazione tocco. Pulsanti/tappable righe della tabella con le etichette di testo devono estendersi l'intero schermo. Apple è indicato "mai inserire più di tre elementi side-by-side", e se si utilizzano le icone e non le etichette di testo.

- Utilizzare il [ `Menu` controllo](~/ios/watchos/user-interface/menu.md) alle funzionalità di esporre utilizzati meno di frequente per mantenere la progettazione di app chiara e concisa.


## <a name="implementation"></a>Implementazione

Guardare che Kit include i seguenti controlli per la compilazione di espressioni di controllo interessante layout app:

### <a name="interface-controller"></a>Controller di interfaccia

Il `WKInterfaceController` è la classe di base tutte le scene.

Finestra di progettazione per il controller di interfaccia si comporta come una verticale **gruppo**: è possibile trascinare altri controlli nel controller di interfaccia e saranno automaticamente disposto uno sopra l'altro:

![](layout-images/controller-scene.png "I controlli sono automaticamente disposto uno sopra l'altro")

È possibile impostare il **posizione** e **dimensioni** alle proprietà di ogni controllo per controllare l'aspetto:

![](layout-images/positionsize-attributes.png "Impostare le proprietà posizione e le dimensioni su ogni controllo")

Quando la dimensione è impostata su **relativo al contenitore** è possibile fornire un valore proporzionale e una offset di regolazione. Questa schermata viene visualizzato un pulsante che è stato impostato su 80% della larghezza della schermata di espressioni di controllo utilizzare (**0,8**):

![](layout-images/button-attributes.png "Fornire un valore proporzionale e una offset di regolazione")


### <a name="group"></a>Gruppo

`WKInterfaceGroup` è un contenitore di layout semplice che può essere configurato per stack controlli orizzontalmente o verticalmente. Sono incluse la spaziatura tra ogni controllo, per impostazione predefinita, ma è possibile modificare la spaziatura elementi interni (e) nei **attributi** controllo.

![](layout-images/group-attributes.png "Modificare la spaziatura e margini del controllo di attributi")

Gruppi possono stessi essere ridimensionati e posizionati rispetto dei controlli attorno a esse e per creare layout complessi, è possibile nidificare i gruppi.

![](layout-images/group-scene.png "Per creare layout complessi, è possibile nidificare i gruppi")


### <a name="separator"></a>Separatore

Il controllo separatore deve fornire indicazioni visive nel layout. Utilizzare separatori (o i colori di sfondo o immagini) per consentire all'utente di comprendere il contenuto è correlato sullo schermo.

![](layout-images/separator-scene.png "Esempio di utilizzo separatore")

Tenere presente i separatori di verde e blu che non usano l'intera larghezza dello schermo sono stati configurati con **Fixed** o **relativo al contenitore** dimensioni.

### <a name="content-controls"></a>Controlli del contenuto

Nessun layout sarebbe incompleta senza il `Label`, `Image`, `Button`, `Switch`, `Slider`, `Map`, e [altri controlli](~/ios/watchos/user-interface/index.md).
Questi possono essere posizionati, il layout con **gruppi** o le impostazioni di posizione e le dimensioni di ogni controllo.



## <a name="related-links"></a>Collegamenti correlati

- [WatchKitCatalog (esempio)](https://developer.xamarin.com/samples/monotouch/WatchKit/WatchKitCatalog/)
- [Riferimento di Layout di Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Layout.html)
- [Colore & tipografia Apple di riferimento](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/ColorandTypography.html)
