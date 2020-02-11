---
title: Uso del layout watchos in Xamarin
description: Questo documento descrive come creare un layout watchos usando Xamarin. Vengono descritti i controller di interfaccia, i gruppi, i separatori e i controlli contenuto.
ms.prod: xamarin
ms.assetid: BEDB62A1-2249-4459-986F-413A41E63DF0
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 568d1e354d0ee840aeed980d6e8cc6b83068a1c8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73001535"
---
# <a name="working-with-watchos-layout-in-xamarin"></a>Uso del layout watchos in Xamarin

La progettazione di layout per le [dimensioni dello schermo](~/ios/watchos/app-fundamentals/screen-sizes.md) Apple Watch presenta problemi univoci.

## <a name="design-tips"></a>Suggerimenti per la progettazione

Il punto chiave è: rendere l'interfaccia utente leggibile e utilizzabile in una piccola schermata di controllo, con un dito grande. Non rientrare nella trappola della progettazione per il **simulatore iOS** (che sembra molto grande) e un **puntatore del mouse** (che funziona con le piccole destinazioni tocco).

- Usa uno sfondo nero: crea l'illusione di uno schermo più grande con la lunetta nera dell'orologio.

- Non riempire il layout dello schermo: la lunetta forma una spaziatura visiva naturale.

- Concentrarsi sulla leggibilità. Usare le dimensioni e i colori dei tipi di carattere in modo sensato per garantire la leggibilità del testo. Usare gli stili di testo incorporati per ottenere il supporto automatico dei tipi dinamici.

![](layout-images/type.png "Example of Dynamic Type support")

- Concentrarsi sulle dimensioni di destinazione del tocco. I pulsanti e le righe della tabella da toccare con etichette di testo devono estendersi sull'intero schermo. Apple dice "non inserire mai più di tre elementi side-by-side" e se si usano icone e non etichette di testo.

- Usare il [controllo`Menu`](~/ios/watchos/user-interface/menu.md) per esporre la funzionalità usata meno di frequente per rendere chiara e concisa la progettazione dell'app.

## <a name="implementation"></a>Implementazione

Watch kit include i controlli seguenti che consentono di creare layout di app di controllo interessanti:

### <a name="interface-controller"></a>Controller di interfaccia

Il `WKInterfaceController` è la classe base di tutte le scene.

L'area di progettazione per il controller di interfaccia si comporta come un **gruppo**verticale: è possibile trascinare altri controlli sul controller di interfaccia, che verranno automaticamente disposti uno sopra l'altro:

![](layout-images/controller-scene.png "Controls are automatically laid-out one above the other")

È possibile impostare le proprietà relative alla **posizione** e alle **dimensioni** per ogni controllo per controllarne l'aspetto:

![](layout-images/positionsize-attributes.png "Set the Position and Size properties on each control")

Quando la dimensione è impostata su **relativo al contenitore** , è possibile fornire un valore proporzionale e una regolazione dell'offset. Questa schermata mostra un pulsante che è stato impostato per usare il 80% della larghezza dello schermo di controllo (**0,8**):

![](layout-images/button-attributes.png "Provide a proportional value and an offset adjustment")

### <a name="group"></a>Raggruppa

`WKInterfaceGroup` è un contenitore di layout semplice che può essere configurato per lo stack di controlli verticalmente o orizzontalmente. Include la spaziatura tra ogni controllo per impostazione predefinita, ma è possibile modificare la spaziatura (e le impostazioni) nel controllo **attributi** .

![](layout-images/group-attributes.png "Modify the spacing and insets in the Attributes inspector")

I gruppi possono essere ridimensionati e posizionati in relazione ai controlli che li circondano e i gruppi possono essere annidati per creare layout complessi.

![](layout-images/group-scene.png "Groups can be nested to create complex layouts")

### <a name="separator"></a>Separator

Il controllo separatore è progettato per fornire indicazioni visive nel layout. Usare i separatori (o immagini o colori di sfondo) per aiutare l'utente a capire quale contenuto è correlato sullo schermo.

![](layout-images/separator-scene.png "Example of Separator usage")

Si noti che i separatori blu e verde che non usano la larghezza intera dello schermo sono stati configurati con dimensioni **fisse** o **relative a contenitori** .

### <a name="content-controls"></a>Controlli del contenuto

Nessun layout verrà completato senza il `Label`, `Image`, `Button`, `Switch`, `Slider`, `Map`e [altri controlli](~/ios/watchos/user-interface/index.md).
Questi possono essere posizionati nei layout usando i **gruppi** o le impostazioni di posizione e dimensioni di ogni controllo.

## <a name="related-links"></a>Collegamenti correlati

- [WatchKitCatalog (esempio)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Riferimento al layout di Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/Layout.html)
- [Informazioni di riferimento sulla tipografia color & di Apple](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/WatchHumanInterfaceGuidelines/ColorandTypography.html)
