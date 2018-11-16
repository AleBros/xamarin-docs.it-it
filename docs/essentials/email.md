---
title: 'Xamarin.Essentials: Email'
description: La classe Email in Xamarin.Essentials consente a un'applicazione di aprire l'applicazione di posta elettronica predefinita con le informazioni specificate, inclusi oggetto, corpo e destinatari (A, Cc, Ccn).
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 3c2958cc4572c2f87c46c9edc5fc194284658f24
ms.sourcegitcommit: 704d4cfd418c17b0e85a20c33a16d2419db0be71
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2018
ms.locfileid: "51691750"
---
# <a name="xamarinessentials-email"></a>Xamarin.Essentials: Email

![NuGet in versione non definitiva](~/media/shared/pre-release.png)

La classe **Email** consente a un'applicazione di aprire l'applicazione di posta elettronica predefinita con le informazioni specificate, inclusi oggetto, corpo e destinatari (A, Cc, Ccn).

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-email"></a>Uso di Email

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità Email opera chiamando il metodo `ComposeAsync` che crea un elemento `EmailMessage` contenente le informazioni sul messaggio di posta elettronica:

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
            // Some other exception occurred
        }
    }
}
```


## <a name="platform-differences"></a>Differenze tra le piattaforme

# <a name="androidtabandroid"></a>[Android](#tab/android)

Non esistono differenze per questa piattaforma.

# <a name="iostabios"></a>[iOS](#tab/ios)

Non esistono differenze per questa piattaforma.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Supporta solo `PlainText` poiché il tentativo di `BodyFormat` di inviare `Html` genera un'eccezione `FeatureNotSupportedException`.

-----

## <a name="api"></a>API

- [Codice sorgente di Email](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Email)
- [Documentazione dell'API Email](xref:Xamarin.Essentials.Email)
