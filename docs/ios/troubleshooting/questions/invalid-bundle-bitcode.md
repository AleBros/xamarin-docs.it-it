---
title: 'Errore durante l''invio all''archivio di App: "Bundle non valido - opzioni non possibile essere incorporati in bitcode vengono rilevati nell''inoltro"'
ms.topic: article
ms.prod: xamarin
ms.assetid: 137313FB-3D29-428B-93C1-5A05DC8F7C03
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: b8ce643d392945e7e746c28b13063a2b0b7ebb48
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submission"></a>Errore durante l'invio all'archivio di App: "Bundle non valido - opzioni non possibile essere incorporati in bitcode vengono rilevati nell'inoltro"

watchOS App e tvOS _richiedono_ bitcode invio all'archivio di App. Durante la compilazione e invio App watchOS e tvOS tramite Xcode 8.3 o versioni precedenti, potrebbe verificarsi l'errore seguente (tramite notifica di posta elettronica) durante il tentativo di caricare nell'App Store:

>Il pacchetto non valido - Impossibile elaborare l'app perché le opzioni non possibile essere incorporati in bitcode vengono rilevate nell'inoltro. È probabile che non si sta creando l'app con la toolchain fornita in Xcode.

Per risolvere questo problema consiste nel compilare le applicazioni con Xcode 9 e l'ultima versione di xamarin. IOS.
