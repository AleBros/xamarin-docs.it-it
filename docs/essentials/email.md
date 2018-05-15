---
title: Messaggio di posta elettronica Xamarin.Essentials
description: La classe messaggio di posta elettronica consente a un'applicazione aprire l'applicazione di posta elettronica predefinito con un'inclusi soggetto, corpo e recepients (a, CC, Ccn) le informazioni specificate.
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: dde3f7f160d796afe3184ef1d61d8b437ec09ea8
ms.sourcegitcommit: 3e05b135b6ff0d607bc2378c1b6e66d2eebbcc3e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2018
---
# <a name="xamarinessentials-email"></a>Messaggio di posta elettronica Xamarin.Essentials

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
    public async Task SendEmail(string subject, string, body, List<string> recipients)
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
            }
            await Email.ComposeAsync(message);
        }
        catch (FeatureNotSupportedException fbsEx)
        {
            // Sms is not supported on this device
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
