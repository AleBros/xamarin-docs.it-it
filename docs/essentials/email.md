---
title: 'Xamarin.Essentials: messaggio di posta elettronica'
description: La classe messaggio di posta elettronica in Xamarin.Essentials consente a un'applicazione aprire l'applicazione di posta elettronica predefinito con un'inclusi soggetto, corpo e destinatari (a, CC, Ccn) le informazioni specificate.
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: aea2f429126180ae3d98bc665bed5574f416ea53
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34782437"
---
# <a name="xamarinessentials-email"></a>Xamarin.Essentials: messaggio di posta elettronica

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **messaggio di posta elettronica** classe consente a un'applicazione aprire l'applicazione di posta elettronica predefinito con un'inclusi soggetto, corpo e recepients (a, CC, Ccn) le informazioni specificate.

## <a name="using-email"></a>Tramite posta elettronica

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità di posta elettronica funziona chiamando il `ComposeAsync` metodo un `EmailMessage` che contiene informazioni sul messaggio di posta elettronica:

```csharp
public class EmailTest
{
    public async Task SendEmail(string subject, string body, List<string> recipients)
    {
        try
        {
            var message = new EmailMessage
            {
                Subject = subject,
                Body = body,
                To = recipients,
                //Cc = ccRecipients,
                //Bcc = bccRecipients
            };
            
            await Email.ComposeAsync(message);
        }
        catch (FeatureNotSupportedException fbsEx)
        {
            // Email is not supported on this device
        }
        catch (Exception ex)
        {
            // Some other exception occured
        }
    }
}
```

## <a name="api"></a>API

- [Codice sorgente di posta elettronica](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Email)
- [Documentazione del messaggio di posta elettronica API](xref:Xamarin.Essentials.Email)
