---
title: Messaggio di posta elettronica Xamarin.Essentials
description: La classe messaggio di posta elettronica consente a un'applicazione aprire l'applicazione di posta elettronica predefinito con un'inclusi soggetto, corpo e recepients (a, CC, Ccn) le informazioni specificate.
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: a23d849131372820a73057b752908886f64b4210
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
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

- [Codice sorgente di posta elettronica](https://github.com/xamarin/Essentials/tree/master/Essentials/Email)
- [Documentazione del messaggio di posta elettronica API](xref:Xamarin.Essentials.Email)
