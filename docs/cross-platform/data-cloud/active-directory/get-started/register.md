---
title: Passaggio 1. Registrare un'app per usare Azure Active Directory
description: Questo documento descrive come registrare un'applicazione Azure con Azure Active Directory in modo che sia in modo sicuro accessibile dai client per dispositivi mobili.
ms.prod: xamarin
ms.assetid: 0B17991A-4573-4F6C-9E86-D4B9D1A47E4D
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 3e852e3e7ab3ac5db28052a47af1ebec4dbd2416
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67864666"
---
# <a name="step-1-register-an-app-to-use-azure-active-directory"></a>Passaggio 1. Registrare un'app per usare Azure Active Directory

1. Passare a [windowsazure.com](https://manage.windowsazure.com) e accedere con l'Account Microsoft o aziendale nel portale di Azure. Se non hai una sottoscrizione di Azure, è possibile ottenere una versione di valutazione da [azure.com](https://www.azure.com)

2. Dopo l'accesso, vedere la **Active Directory** sezione (1) e scegliere la directory in cui si vuole registrare l'applicazione (2)

   [![](register-images/01.-active-directory-in-azure-portal-sml.jpg "sezione e scegliere la directory in cui si vuole registrare l'applicazione")](register-images/01.-active-directory-in-azure-portal.jpg#lightbox)

3. Fare clic su **Add** per creare una nuova applicazione, quindi selezionare **Aggiungi un'applicazione che l'organizzazione sta sviluppando**

   [![](register-images/02.-add-new-application-sml.jpg "Aggiungere un'applicazione che l'organizzazione sta sviluppando")](register-images/02.-add-new-application.jpg#lightbox)

4. Nella schermata successiva, assegnare all'app un nome (ad es. XAM-DEMO).
   Assicurarsi di selezionare **applicazione Client nativa** come tipo di applicazione.

   ![](register-images/03.-app-name.jpg "Assicurarsi di selezionare l'applicazione Client nativa come tipo di applicazione")

5. Nella schermata finale, fornire un **URI di reindirizzamento* che è univoco per l'applicazione come verrà restituito da questo URI di una volta completata l'autenticazione.

   ![](register-images/04.-app-redirect.jpg "Nella schermata finale, fornire un URI di reindirizzamento che è univoco per l'applicazione come verrà restituito da questo URI di una volta completata l'autenticazione")

6. Dopo aver creato l'app, passare al **configura** scheda. Prendere nota di **ID Client** che verrà usata nell'applicazione in un secondo momento. Inoltre, in questa schermata è possibile concedere l'accesso dell'applicazione per dispositivi mobili ad Active Directory o aggiungere un'altra applicazione, ad esempio API Web o di Office 365, che può essere utilizzato da applicazioni per dispositivi mobili una volta completata l'autenticazione.

     ![](register-images/05.-configure.jpg "In questa schermata è inoltre possibile assegnare all'applicazione per dispositivi mobili di accedere ad Active Directory o aggiungere un'altra applicazione, ad esempio Office 365 o API Web")



## <a name="related-links"></a>Collegamenti correlati

- [Esempio NativeClient Microsoft](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
