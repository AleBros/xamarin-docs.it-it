---
title: 'Xamarin.Essentials: Email'
description: La classe Email in Xamarin.Essentials consente a un'applicazione di aprire l'applicazione di posta elettronica predefinita con le informazioni specificate, inclusi oggetto, corpo e destinatari (A, Cc, Ccn).
ms.assetid: 5FBB6FF0-0E7B-4C29-8F06-91642AF12629
author: jamesmontemagno
ms.author: jamont
ms.date: 04/02/2019
ms.openlocfilehash: 73c398a7dbc2b8b7b22b9b8e38177efe2ff48735
ms.sourcegitcommit: 8fe8d163cb9927917f6a83204b4c387fc50181c2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68388494"
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

## <a name="platform-differences"></a>Differenze tra le piattaforme

# <a name="androidtabandroid"></a>[Android](#tab/android)

Non tutti i client di posta elettronica per Android supportano `Html`. Dato che non è possibile rilevare questa condizione, è consigliabile usare `PlainText` durante l'invio di messaggi di posta elettronica.

# <a name="iostabios"></a>[iOS](#tab/ios)

Non esistono differenze per questa piattaforma.

# <a name="uwptabuwp"></a>[UWP](#tab/uwp)

Supporta solo `PlainText` poiché il tentativo di `BodyFormat` di inviare `Html` genera un'eccezione `FeatureNotSupportedException`.

Non tutti i client di posta elettronica supportano l'invio di allegati. Vedere la [documentazione](https://docs.microsoft.com/windows/uwp/contacts-and-calendar/sending-email) per altre informazioni.

-----

## <a name="api"></a>API

- [Codice sorgente di Email](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Email)
- [Documentazione dell'API Email](xref:Xamarin.Essentials.Email)
