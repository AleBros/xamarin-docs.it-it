---
title: Azure Active Directory
description: Questo documento vengono descritti i passaggi che devono essere seguiti per consentire un'app per dispositivi mobili per l'autenticazione con Azure Active Directory.
ms.prod: xamarin
ms.assetid: 70B3C2AB-CB4D-420C-9CFA-20CCFA0E3C78
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: ca33422817f19dbb0a04e8870800d3f5efa8af2a
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780067"
---
# <a name="azure-active-directory"></a>Azure Active Directory

_Registrare un'app per l'utilizzo di Azure Active Directory_

Azure Active Directory consente agli sviluppatori per le risorse protette quali file, collegamenti e le API Web con lo stesso account aziendale che i dipendenti usano per accedere ai propri sistemi o controllare i messaggi di posta elettronica.

Lo sviluppo di applicazioni per dispositivi mobili che è possano eseguire l'autenticazione con Azure Active Directory comporta tre passaggi.
I primi due passaggi sono in genere lo stesso, indipendentemente dalle quali servizi si intende utilizzare. Il terzo passaggio è diverso per ogni tipo di servizio:

  1. [Registrazione con Azure Active Directory](~/cross-platform/data-cloud/active-directory/get-started/register.md) sul *windowsazure.com* portale, quindi
  2. [Configurare i servizi](~/cross-platform/data-cloud/active-directory/get-started/configure.md).
  3. Sviluppare App per dispositivi mobili utilizzando i servizi.

Esempi di diversi servizi a che è possibile accedere:

- [API Graph](~/cross-platform/data-cloud/active-directory/graph.md)
- API Web
- Office365


## <a name="conclusion"></a>Conclusione

Utilizzando i passaggi precedenti è possibile autenticare l'App per dispositivi mobili con Azure Active Directory. Active Directory Authentication Library (ADAL) rende molto più semplice con meno righe di codice, mantenendo la maggior parte del codice uguale e rendendo così essere condivise tra le piattaforme.



## <a name="related-links"></a>Collegamenti correlati

- [Esempio NativeClient Microsoft](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
