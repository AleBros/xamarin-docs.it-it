---
title: Passaggio 2. Configurare l'accesso al servizio per applicazioni per dispositivi mobili
description: Questo documento descrive come fornire un'applicazione Xamarin con l'accesso a un'applicazione di Azure protetta da Azure Active Directory.
ms.prod: xamarin
ms.assetid: 8A14A457-F72E-4B08-B4B6-801F7619F893
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 2a9baab9215ae2d30e4daf6800a116c95165da42
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61188267"
---
# <a name="step-2-configure-service-access-for-mobile-application"></a>Passaggio 2. Configurare l'accesso al servizio per applicazioni per dispositivi mobili

Ogni volta che qualsiasi risorsa, ad esempio l'applicazione web, servizio web, e così via deve essere protetto da Azure Active Directory, deve essere registrato. Tutte le applicazioni protette o servizi possono essere visualizzati in **applicazioni** scheda. Qui è possibile selezionare l'applicazione che deve essere accessibile dall'applicazione per dispositivi mobili e consentire l'accesso a esso.

1. Nel **Configure** scheda, individuare **autorizzazioni per altre applicazioni** sezione:

  ![](configure-images/2.1-configure.png "Nella scheda Configura, individuare sezione autorizzazioni per altre applicazioni")

2.  Fare clic su **aggiungere l'applicazione** pulsante. Nella schermata successiva popup verrà visualizzato l'elenco di tutte le applicazioni che sono protetti da Azure Active Directory. Selezionare le applicazioni che deve essere accessibile dall'applicazione per dispositivi mobili.

  ![](configure-images/2.2-add-application.png "Selezionare le applicazioni che deve essere accessibile dall'applicazione per dispositivi mobili")

3. Dopo aver selezionato l'applicazione, anche in questo caso selezionare l'applicazione appena aggiunto in **autorizzazioni per altre applicazioni** sezione e assegnare i diritti appropriati.

  ![](configure-images/2.3-permissions.png "Dopo aver selezionato l'applicazione, anche in questo caso selezionare l'applicazione appena aggiunto nella sezione autorizzazioni per altre applicazioni e concedere i diritti appropriati")

4. Infine **salvare** la configurazione. Questi servizi dovrebbero ora essere disponibili nelle applicazioni per dispositivi mobili.



## <a name="related-links"></a>Collegamenti correlati

- [Esempio NativeClient Microsoft](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
