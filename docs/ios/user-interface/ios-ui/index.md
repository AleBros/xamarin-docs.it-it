---
title: Interfaccia utente in iOS
description: Viene illustrato l'utilizzo con l'interfaccia utente di iOS in un'app xamarin. IOS.
ms.prod: xamarin
ms.assetid: 1BB46561-F503-491E-A27C-7878E7EBE00B
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/14/2017
ms.openlocfilehash: dd07ba6853901c93ad26b371ea88b7d39c37d3f7
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="user-interface-in-ios"></a>Interfaccia utente in iOS

## <a name="appearance-apiintroduction-to-the-appearance-apimd"></a>[API Appearance](introduction-to-the-appearance-api.md)

iOS consente molti attributi visivi di controlli dell'interfaccia utente per applicare un tema utilizzando le APIs UIAppearance.

## <a name="creating-user-interface-objectsiosuser-interfaceios-uicreating-ui-objectsmd"></a>[Creazione di oggetti dell'interfaccia utente](~/ios/user-interface/ios-ui/creating-ui-objects.md)

Gruppi di Apple relative funzionalità in "Framework" equivalgono a spazi dei nomi di xamarin. IOS. `UIKit` è lo spazio dei nomi che contiene tutti i controlli dell'interfaccia utente per iOS.

## <a name="layout-optionsiosuser-interfaceios-uilayout-optionsmd"></a>[Opzioni di layout](~/ios/user-interface/ios-ui/layout-options.md)

Sono disponibili due meccanismi diversi per controllare il layout quando una vista è stata ridimensionata o ruotata: ridimensionamento automatico e il layout automatico.

## <a name="providing-haptic-feedbackiosuser-interfaceios-uihaptic-feedbackmd"></a>[Implementazione del feedback aptico](~/ios/user-interface/ios-ui/haptic-feedback.md)

In questo articolo vengono illustrati i nuovi tipi di commenti e suggerimenti tattili disponibile in iOS 10 e come implementarli in xamarin. IOS.

## <a name="working-with-the-ui-threadiosuser-interfaceios-uiui-threadmd"></a>[Utilizzo del thread dell'interfaccia utente](~/ios/user-interface/ios-ui/ui-thread.md)

Il codice deve verificare solo thread di modifiche ai controlli dell'interfaccia da principale utente (o dell'interfaccia utente). Eventuali aggiornamenti dell'interfaccia utente che si verificano in un thread diverso (ad esempio, un thread di callback o in background) potrebbero non sottoposti a rendering sullo schermo oppure potrebbe provocare un arresto anomalo.




