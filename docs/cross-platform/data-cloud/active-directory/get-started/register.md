---
title: Passaggio 1. Registrare un'app per l'utilizzo di Azure Active Directory
description: Questo documento viene descritto come registrare un'applicazione Azure con Azure Active Directory in modo che sia in modo sicuro accessibile dai client per dispositivi mobili.
ms.prod: xamarin
ms.assetid: 0B17991A-4573-4F6C-9E86-D4B9D1A47E4D
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 7f1e743eea81edc0aa45b49f6acb6a9fd461bc80
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34780669"
---
# <a name="step-1-register-an-app-to-use-azure-active-directory"></a>Passaggio 1. Registrare un'app per l'utilizzo di Azure Active Directory

1. Passare a [windowsazure.com](https://manage.windowsazure.com) e accedere con l'Account Microsoft o aziendale nel portale di Azure. Se non si dispone di una sottoscrizione di Azure, è possibile ottenere una versione di valutazione da [azure.com](http://www.azure.com)

2. Dopo aver effettuato l'accesso, passare al **Active Directory** sezione (1) e scegliere la directory in cui si desidera registrare l'applicazione (2)

  [ ![](register-images/01.-active-directory-in-azure-portal-sml.jpg "sezione e scegliere la directory in cui si desidera registrare l'applicazione")](register-images/01.-active-directory-in-azure-portal.jpg#lightbox)

3. Fare clic su **Aggiungi** per creare una nuova applicazione, quindi selezionare **Aggiungi un'applicazione che l'organizzazione sta sviluppando**

  [ ![](register-images/02.-add-new-application-sml.jpg "Aggiungere un'applicazione in cui che l'organizzazione sta sviluppando")](register-images/02.-add-new-application.jpg#lightbox)

4. Nella schermata successiva, assegnare un nome (ad es l'app. XAM-DEMO).
  Assicurarsi di selezionare **applicazione Client nativa** come tipo di applicazione.

  ![](register-images/03.-app-name.jpg "Assicurarsi di selezionare l'applicazione Client nativa come tipo di applicazione")

5. Nella schermata finale, fornire un **URI di reindirizzamento* che è univoco per l'applicazione, verrà restituito per questo URI quando l'autenticazione.

  ![](register-images/04.-app-redirect.jpg "Nella schermata finale, fornire un URI di reindirizzamento che è univoco per l'applicazione, verrà restituito per questo URI quando l'autenticazione")

6. Una volta creato l'app, passare al **configura** scheda. Annotare il **ID Client** che verrà usato nell'applicazione in un secondo momento. Inoltre, in questa schermata è possibile concedere all'applicazione per dispositivi mobili di accedere ad Active Directory o aggiungere un'altra applicazione, ad esempio API Web o di Office 365, che può essere utilizzato dall'applicazione per dispositivi mobili, una volta completata l'autenticazione.

    ![](register-images/05.-configure.jpg "Inoltre, in questa schermata è possibile concedere all'applicazione per dispositivi mobili di accedere ad Active Directory o aggiungere un'altra applicazione, ad esempio API Web o di Office 365")



## <a name="related-links"></a>Collegamenti correlati

- [Esempio NativeClient Microsoft](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
