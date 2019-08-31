---
title: Passaggio 1. Registrare un'app da usare Azure Active Directory
description: Questo documento descrive come registrare un'applicazione Azure con Azure Active Directory in modo che sia possibile accedervi in modo sicuro dai client mobili.
ms.prod: xamarin
ms.assetid: 0B17991A-4573-4F6C-9E86-D4B9D1A47E4D
author: asb3993
ms.author: amburns
ms.date: 03/23/2017
ms.openlocfilehash: 732c1ee241b39a4bb1422b8c27820631bf8c6b0c
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/31/2019
ms.locfileid: "70199054"
---
# <a name="step-1-register-an-app-to-use-azure-active-directory"></a>Passaggio 1. Registrare un'app da usare Azure Active Directory

1. Passare a [WindowsAzure.com](https://manage.windowsazure.com) e accedere con l'account Microsoft o l'account dell'organizzazione nel portale di Azure. Se non si ha una sottoscrizione di Azure, è possibile ottenere una versione di valutazione di [Azure.com](https://www.azure.com)

2. Dopo aver eseguito l'accesso, passare alla sezione **Active Directory** (1) e scegliere la directory in cui si vuole registrare l'applicazione (2)

   [![](register-images/01.-active-directory-in-azure-portal-sml.jpg "e scegliere la directory in cui si vuole registrare l'applicazione")](register-images/01.-active-directory-in-azure-portal.jpg#lightbox)

3. Fare clic su **Aggiungi** per creare una nuova applicazione, quindi selezionare **Aggiungi un'applicazione che l'organizzazione sta sviluppando**

   [![](register-images/02.-add-new-application-sml.jpg "Aggiungi un'applicazione che l'organizzazione sta sviluppando")](register-images/02.-add-new-application.jpg#lightbox)

4. Nella schermata successiva assegnare un nome all'app, ad esempio XAM-DEMO).
   Assicurarsi di selezionare **applicazione client nativa** come tipo di applicazione.

   ![](register-images/03.-app-name.jpg "Assicurarsi di selezionare applicazione client nativa come tipo di applicazione")

5. Nella schermata finale specificare un * URI di*Reindirizzamento* univoco per l'applicazione, in quanto tornerà a questo URI al termine dell'autenticazione.

   ![](register-images/04.-app-redirect.jpg "Nella schermata finale specificare un URI di reindirizzamento univoco per l'applicazione, in quanto tornerà a questo URI al termine dell'autenticazione")

6. Una volta creata l'app, passare alla scheda **Configura** . Prendere nota dell' **ID client** che verrà usato nell'applicazione in un secondo momento. Inoltre, in questa schermata è possibile concedere l'accesso dell'applicazione per dispositivi mobili a Active Directory o aggiungere un'altra applicazione, ad esempio API Web o Office365, che può essere usata dall'applicazione mobile una volta completata l'autenticazione.

   ![](register-images/05.-configure.jpg "Inoltre, in questa schermata è possibile concedere l'accesso dell'applicazione per dispositivi mobili a Active Directory o aggiungere un'altra applicazione come API Web o Office365")



## <a name="related-links"></a>Collegamenti correlati

- [Esempio Microsoft NativeClient](https://github.com/AzureADSamples/NativeClient-MultiTarget-DotNet)
