---
title: Passaggio 2. Configurare l'accesso al servizio per l'applicazione per dispositivi mobili
description: Questo documento descrive come fornire un'applicazione Novell con accesso a un'applicazione Azure protetta da Azure Active Directory.
ms.prod: xamarin
ms.assetid: 8A14A457-F72E-4B08-B4B6-801F7619F893
author: conceptdev
ms.author: crdun
ms.date: 03/23/2017
ms.openlocfilehash: ec5dd15ffb838d7062c8c769375289e7b07b24d2
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70766378"
---
# <a name="step-2-configure-service-access-for-mobile-application"></a>Passaggio 2. Configurare l'accesso al servizio per l'applicazione per dispositivi mobili

Quando una risorsa, ad esempio un'applicazione Web, un servizio Web e così via, deve essere protetta da Azure Active Directory, deve essere registrata. Tutte le applicazioni o i servizi protetti possono essere visualizzati nella scheda **applicazioni** . Qui è possibile selezionare l'applicazione a cui deve essere eseguito l'accesso dall'applicazione per dispositivi mobili e concedergli l'accesso.

1. Nella scheda **Configura** individuare le **autorizzazioni per altre applicazioni** sezione:

   ![](configure-images/2.1-configure.png "Nella scheda Configura individuare le autorizzazioni per altre applicazioni sezione")

2. Fare clic sul pulsante **Aggiungi applicazione** . Nella finestra popup successiva verrà visualizzato un elenco di tutte le applicazioni protette da Azure Active Directory. Selezionare le applicazioni a cui è necessario accedere dall'applicazione per dispositivi mobili.

   ![](configure-images/2.2-add-application.png "Selezionare le applicazioni a cui è necessario accedere dall'applicazione per dispositivi mobili")

3. Dopo aver selezionato l'applicazione, selezionare di nuovo l'applicazione appena aggiunta nella sezione **autorizzazioni per altre applicazioni** e concedere i diritti appropriati.

   ![](configure-images/2.3-permissions.png "Dopo aver selezionato l'applicazione, selezionare di nuovo l'applicazione appena aggiunta nella sezione autorizzazioni per altre applicazioni e concedere i diritti appropriati")

4. Infine, **salvare** la configurazione. Questi servizi dovrebbero ora essere disponibili nelle applicazioni per dispositivi mobili.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio Microsoft NativeClient](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
