---
title: "Errore durante l'invio all'App Store: \"Bundle non valido: le opzioni non consentite per l'incorporamento in bitcode sono rilevate nell'invio\""
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 137313FB-3D29-428B-93C1-5A05DC8F7C03
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: c6a03fa3327e81f577f85b05a033d9c97495eb2e
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031069"
---
# <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submission"></a>Errore durante l'invio all'App Store: "Bundle non valido: le opzioni non consentite per l'incorporamento in bitcode sono rilevate nell'invio"

le app watchos e le app tvOS _richiedono_ bitcode quando vengono inviate all'App Store. Quando si compilano e inviano app watchos e tvOS con Xcode 8,3 o versioni precedenti, è possibile che si verifichi l'errore seguente (tramite la notifica tramite posta elettronica) durante il tentativo di caricamento nell'App Store:

>Bundle non valido. l'app non può essere elaborata perché le opzioni non consentite per l'incorporamento in bitcode vengono rilevate nell'invio. È probabile che non si stia compilando l'app con la cassetta di stato fornita in Xcode.

La soluzione a questo problema consiste nel compilare le applicazioni con Xcode 9 e la versione più recente di Xamarin.iOS.
