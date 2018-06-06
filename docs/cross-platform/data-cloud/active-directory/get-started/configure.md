---
title: Passaggio 2. Configurare l'accesso al servizio per applicazioni mobili
description: Questo documento viene descritto come fornire un'applicazione di Xamarin con accesso a un'applicazione Azure protetta da Azure Active Directory.
ms.prod: xamarin
ms.assetid: 8A14A457-F72E-4B08-B4B6-801F7619F893
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 2a9baab9215ae2d30e4daf6800a116c95165da42
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780100"
---
# <a name="step-2-configure-service-access-for-mobile-application"></a>Passaggio 2. Configurare l'accesso al servizio per applicazioni mobili

Ogni volta che qualsiasi risorsa, ad esempio, l'applicazione web, servizio web, e così via. deve essere protetto da Azure Active Directory, è necessario essere registrati. Tutte le applicazioni protette o servizi possono essere visualizzati in **applicazioni** scheda. Qui è possibile selezionare l'applicazione che deve accedere dall'applicazione per dispositivi mobili e consentire l'accesso a esso.

1. Nel **configura** , individuare **autorizzazioni per altre applicazioni** sezione:

  ![](configure-images/2.1-configure.png "Nella scheda Configura, individuare sezione autorizzazioni per altre applicazioni")

2.  Fare clic su **aggiungere applicazione** pulsante. Nella schermata successiva popup verrà visualizzato l'elenco di tutte le applicazioni che sono protetti da Azure Active Directory. Selezionare le applicazioni che devono essere accessibili dall'applicazione per dispositivi mobili.

  ![](configure-images/2.2-add-application.png "Selezionare le applicazioni che devono essere accessibili dall'applicazione per dispositivi mobili")

3. Dopo aver selezionato l'applicazione, selezionare nuovamente l'applicazione appena aggiunto in **autorizzazioni per altre applicazioni** sezione e assegnare i diritti appropriati.

  ![](configure-images/2.3-permissions.png "Dopo aver selezionato l'applicazione, ancora una volta selezionare l'applicazione appena aggiunto nella sezione autorizzazioni per altre applicazioni e assegnare i diritti appropriati")

4. Infine, **salvare** la configurazione. Questi servizi dovrebbero ora essere disponibili in applicazioni per dispositivi mobili.



## <a name="related-links"></a>Collegamenti correlati

- [Esempio NativeClient Microsoft](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
