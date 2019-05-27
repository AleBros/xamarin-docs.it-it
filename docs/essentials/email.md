---
title: 'Xamarin.Essentials: Email'
description: La classe Email in Xamarin.Essentials consente a un'applicazione di aprire l'applicazione di posta elettronica predefinita con le informazioni specificate, inclusi oggetto, corpo e destinatari (A, Cc, Ccn).
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.author: jamont
ms.date: 04/02/2019
ms.openlocfilehash: 5ddbb6f3d1c1ebf16ca5534b7effd1993c3d8b26
ms.sourcegitcommit: 0c823f5439f4279a35af23dd466e7a0483e65d50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65804916"
---
# <a name="xamarinessentials-email"></a>Xamarin.Essentials: Email

La classe **Email** consente a un'applicazione di aprire l'applicazione di posta elettronica predefinita con le informazioni specificate, inclusi oggetto, corpo e destinatari (A, Cc, Ccn).

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

> [!TIP]
> Per usare l'API Email in iOS, è necessario eseguirla in un dispositivo fisico, altrimenti verrà generata un'eccezione.

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

## <a name="file-attachments"></a>File allegati

![Funzionalità di anteprima](~/media/shared/preview.png)

L'invio di file tramite posta elettronica è disponibile come anteprima sperimentale in Xamarin.Essentials versione 1.1.0. Questa funzionalità consente a un'app di inviare file in messaggi di posta elettronica nei client di posta elettronica del dispositivo. Per abilitare questa funzionalità impostare la proprietà seguente nel codice di avvio dell'app:

```csharp
ExperimentalFeatures.Enable(ExperimentalFeatures.EmailAttachments);
```

Dopo l'abilitazione della funzionalità è possibile inviare qualsiasi file tramite posta elettronica. Xamarin.Essentials rileva automaticamente il tipo di file (MIME) e richiede l'aggiunta del file come allegato. Ogni client di posta elettronica è diverso: un client potrebbe supportare solo estensioni di file specifica o nessuna estensione.

Il seguente esempio mostra la scrittura di testo su disco e l'aggiunta del testo come allegato di posta elettronica:

```csharp
var message = new EmailMessage
{
    Subject = "Hello",
    Body = "World",
};

var fn = "Attachment.txt";
var file = Path.Combine(FileSystem.CacheDirectory, fn);
File.WriteAllText(file, "Hello World");

message.Attachments.Add(new EmailAttachment(file));

await Email.ComposeAsync(message);
```

## <a name="api"></a>API

- [Codice sorgente di Email](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Email)
- [Documentazione dell'API Email](xref:Xamarin.Essentials.Email)
