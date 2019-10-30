---
title: Azure Active Directory
description: Questo documento descrive i passaggi da seguire per consentire a un'app per dispositivi mobili di eseguire l'autenticazione con Azure Active Directory.
ms.prod: xamarin
ms.assetid: 70B3C2AB-CB4D-420C-9CFA-20CCFA0E3C78
author: davidortinau
ms.author: daortin
ms.date: 03/23/2017
ms.openlocfilehash: 18604ffa4980d47149d8feed257460e782f30bee
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73016638"
---
# <a name="azure-active-directory"></a>Azure Active Directory

_Registrare un'app da usare Azure Active Directory_

Azure Active Directory consente agli sviluppatori di proteggere risorse quali file, collegamenti e API Web usando lo stesso account aziendale usato dai dipendenti per accedere ai propri sistemi o controllare i loro messaggi di posta elettronica.

Lo sviluppo di applicazioni per dispositivi mobili in grado di eseguire l'autenticazione con Azure Active Directory prevede tre passaggi.
I primi due passaggi sono in genere gli stessi, indipendentemente dai servizi che si prevede di usare. Il terzo passaggio è diverso per ogni tipo di servizio:

  1. Per la [registrazione con Azure Active Directory](~/cross-platform/data-cloud/active-directory/get-started/register.md) nel portale di *WindowsAzure.com* ,
  2. [Configurare i servizi](~/cross-platform/data-cloud/active-directory/get-started/configure.md).
  3. Sviluppare app per dispositivi mobili usando i servizi.

Esempi di servizi diversi a cui è possibile accedere includono:

- [API Graph](~/cross-platform/data-cloud/active-directory/graph.md)
- API Web
- Office365

## <a name="conclusion"></a>Conclusione

Seguendo i passaggi precedenti è possibile autenticare le app per dispositivi mobili con Azure Active Directory. Il Active Directory Authentication Library (ADAL) rende molto più semplice con un minor numero di righe di codice, mantenendo la maggior parte del codice allo stesso modo, rendendolo condivisibile tra le diverse piattaforme.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio Microsoft NativeClient](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
