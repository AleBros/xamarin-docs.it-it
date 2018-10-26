---
title: "Errore durante l'invio all'App Store: \"Bundle non valido - opzioni non possa essere incorporate in bitcode vengono rilevate nell'invio\""
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 137313FB-3D29-428B-93C1-5A05DC8F7C03
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: 867ad29abfa6a38971b60ac9ebf181905949dafd
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50106988"
---
# <a name="error-when-submitting-to-app-store-invalid-bundle---options-not-allowed-to-be-embedded-in-bitcode-are-detected-in-the-submission"></a>Errore durante l'invio all'App Store: "Bundle non valido - opzioni non possa essere incorporate in bitcode vengono rilevate nell'invio"

watchOS App e App tvOS _richiedono_ bitcode quando vengono inviate per l'App Store. Durante la compilazione e invio di App watchOS e tvOS con Xcode 8.3 o versione precedente, potrebbe verificarsi l'errore seguente (tramite notifica di posta elettronica) quando si prova a caricare l'App Store:

>Bundle non valido: l'app non può essere elaborato perché le opzioni non possa essere incorporate in bitcode vengono rilevate nell'invio. È probabile che non crei l'app con la toolchain disponibile in Xcode.

Per risolvere questo problema consiste nella compilazione di applicazioni con Xcode 9 e la versione più recente di xamarin. IOS.
