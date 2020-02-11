---
title: Interfacce utente in iOS
description: Questo documento contiene collegamenti a guide che descrivono come creare interfacce utente nell'app Xamarin.iOS. Le guide collegate coprono l'aspetto dell'API, la creazione di oggetti dell'interfaccia utente, le opzioni di layout e altro ancora.
ms.prod: xamarin
ms.assetid: 1BB46561-F503-491E-A27C-7878E7EBE00B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/14/2017
ms.openlocfilehash: 325898b3c934e25ae1610a3437f787476dcd22cb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73003336"
---
# <a name="user-interfaces-in-ios"></a>Interfacce utente in iOS

## <a name="appearance-apiintroduction-to-the-appearance-apimd"></a>[API Appearance](introduction-to-the-appearance-api.md)

iOS consente di usare con le API UIAppearance molti attributi visivi dei controlli dell'interfaccia utente.

## <a name="creating-user-interface-objectsiosuser-interfaceios-uicreating-ui-objectsmd"></a>[Creazione di oggetti dell'interfaccia utente](~/ios/user-interface/ios-ui/creating-ui-objects.md)

Apple raggruppa i componenti di funzionalità correlati in "Framework", che corrispondono agli spazi dei nomi Xamarin.iOS. `UIKit` è lo spazio dei nomi che contiene tutti i controlli dell'interfaccia utente per iOS.

## <a name="layout-optionsiosuser-interfaceios-uilayout-optionsmd"></a>[Opzioni di layout](~/ios/user-interface/ios-ui/layout-options.md)

Esistono due meccanismi diversi per controllare il layout quando una visualizzazione viene ridimensionata o ruotata: ridimensionamento automatico e layout automatico.

## <a name="providing-haptic-feedbackiosuser-interfaceios-uihaptic-feedbackmd"></a>[Implementazione del feedback aptico](~/ios/user-interface/ios-ui/haptic-feedback.md)

Questo articolo illustra i nuovi tipi di feedback tattili disponibili in iOS 10 e come implementarli in Xamarin.iOS.

## <a name="working-with-the-ui-threadiosuser-interfaceios-uiui-threadmd"></a>[Utilizzo del thread dell'interfaccia utente](~/ios/user-interface/ios-ui/ui-thread.md)

Il codice deve apportare modifiche solo ai controlli dell'interfaccia utente dal thread principale (o interfaccia utente). Eventuali aggiornamenti dell'interfaccia utente che si verificano in un thread diverso (ad esempio un callback o un thread in background) potrebbero non essere visualizzati sullo schermo o addirittura causare un arresto anomalo del sistema.
