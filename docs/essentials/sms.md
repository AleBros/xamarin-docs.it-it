---
title: 'Xamarin.Essentials: SMS'
description: La classe SMS in Xamarin.Essentials consente a un'applicazione di aprire l'applicazione SMS predefinita con un messaggio specificato da inviare a un destinatario.
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 11/04/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c3518befc9f26895514bf582a763c1323f336277
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84801842"
---
# <a name="xamarinessentials-sms"></a>Xamarin.Essentials: SMS

La classe **Sms** consente a un'applicazione di aprire l'applicazione predefinita per gli SMS con un messaggio specificato da inviare a un destinatario.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-sms"></a>Uso di Sms

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità SMS funziona chiamando il metodo `ComposeAsync` per un `SmsMessage` che contiene il destinatario e il corpo del messaggio, entrambi facoltativi.

```csharp
public class SmsTest
{
    public async Task SendSms(string messageText, string recipient)
    {
        try
        {
            var message = new SmsMessage(messageText, new []{ recipient });
            await Sms.ComposeAsync(message);
        }
        catch (FeatureNotSupportedException ex)
        {
            // Sms is not supported on this device.
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

Inoltre, è possibile passare più destinatari a `SmsMessage`:

```csharp
public class SmsTest
{
    public async Task SendSms(string messageText, string[] recipients)
    {
        try
        {
            var message = new SmsMessage(messageText, recipients);
            await Sms.ComposeAsync(message);
        }
        catch (FeatureNotSupportedException ex)
        {
            // Sms is not supported on this device.
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

## <a name="api"></a>API

- [Codice sorgente di Sms](https://github.com/xamarin/Essentials/tree/main/Xamarin.Essentials/Sms)
- [Documentazione dell'API Sms](xref:Xamarin.Essentials.Sms)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/SMS-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
