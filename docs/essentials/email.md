---
title: 'Xamarin.Essentials: Email'
description: La classe Email in Xamarin.Essentials consente a un'applicazione di aprire l'applicazione di posta elettronica predefinita con le informazioni specificate, inclusi oggetto, corpo e destinatari (A, Cc, Ccn).
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: d7d2536fca32fe3ae9f9692031645c42edb4ea61
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898663"
---
# <a name="xamarinessentials-email"></a>Xamarin.Essentials: Email

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

Non tutti i client di posta elettronica per Android supportano `Html`. Dato che non è possibile rilevare questa condizione, è consigliabile usare `PlainText` durante l'invio di messaggi di posta elettronica.

# <a name="iostabios"></a>[iOS](#tab/ios)

Non esistono differenze per questa piattaforma.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Supporta solo `PlainText` poiché il tentativo di `BodyFormat` di inviare `Html` genera un'eccezione `FeatureNotSupportedException`.

-----

## <a name="api"></a>API

- [Codice sorgente di Email](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Email)
- [Documentazione dell'API Email](xref:Xamarin.Essentials.Email)
