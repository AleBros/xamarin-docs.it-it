---
title: "Errore durante l'invio all'App Store: \"Bundle non valido: le opzioni non consentite per l'incorporamento in bitcode sono rilevate nell'invio\""
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 137313FB-3D29-428B-93C1-5A05DC8F7C03
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 04/03/2018
ms.openlocfilehash: 84244e0c4c24a8ca6ac71a79de963bedf5c1ee68
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292541"
---
# <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submission"></a>Errore durante l'invio all'App Store: "Bundle non valido: le opzioni non consentite per l'incorporamento in bitcode sono rilevate nell'invio"

le app watchos e le app tvOS _richiedono_ bitcode quando vengono inviate all'App Store. Quando si compilano e inviano app watchos e tvOS con Xcode 8,3 o versioni precedenti, è possibile che si verifichi l'errore seguente (tramite la notifica tramite posta elettronica) durante il tentativo di caricamento nell'App Store:

>Bundle non valido. l'app non può essere elaborata perché le opzioni non consentite per l'incorporamento in bitcode vengono rilevate nell'invio. È probabile che non si stia compilando l'app con la cassetta di stato fornita in Xcode.

La soluzione a questo problema consiste nel compilare le applicazioni con Xcode 9 e la versione più recente di Novell. iOS.
