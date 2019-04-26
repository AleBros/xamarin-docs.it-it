---
title: Azure Active Directory
description: Questo documento vengono descritti i passaggi che devono essere seguiti per consentire un'app per dispositivi mobili per l'autenticazione con Azure Active Directory.
ms.prod: xamarin
ms.assetid: 70B3C2AB-CB4D-420C-9CFA-20CCFA0E3C78
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: ca33422817f19dbb0a04e8870800d3f5efa8af2a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61318298"
---
# <a name="azure-active-directory"></a>Azure Active Directory

_Registrare un'app per usare Azure Active Directory_

Azure Active Directory consente agli sviluppatori di proteggere le risorse, ad esempio file, collegamenti e le API Web usando lo stesso account aziendale che i dipendenti usano per accedere ai loro sistemi o controllare i messaggi di posta elettronica.

Lo sviluppo di applicazioni per dispositivi mobili che possono eseguire l'autenticazione con Azure Active Directory prevede tre passaggi.
I primi due passaggi sono in genere lo stesso, indipendentemente dal fatto che si prevede di usare i servizi. Il terzo passaggio è diverso per ogni tipo di servizio:

  1. [La registrazione con Azure Active Directory](~/cross-platform/data-cloud/active-directory/get-started/register.md) nella *windowsazure.com* portale, quindi
  2. [Configurare i servizi](~/cross-platform/data-cloud/active-directory/get-started/configure.md).
  3. Sviluppa App per dispositivi mobili usando i servizi.

Esempi di diversi servizi a che è possibile accedere:

- [API Graph](~/cross-platform/data-cloud/active-directory/graph.md)
- API Web
- Office365


## <a name="conclusion"></a>Conclusione

Usando i passaggi precedenti è possibile autenticare l'App per dispositivi mobili con Azure Active Directory. Active Directory Authentication Library (ADAL) rende molto più semplice con meno righe di codice, mantenendo la maggior parte del codice stesso e semplificando pertanto essere condivise tra le piattaforme.



## <a name="related-links"></a>Collegamenti correlati

- [Esempio NativeClient Microsoft](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
