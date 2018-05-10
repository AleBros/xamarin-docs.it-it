---
title: Xamarin.Essentials SMS
description: La classe Sms consente a un'applicazione aprire l'applicazione di SMS predefinito con un messaggio da inviare a un destinatario specificato.
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 5baeee03626ba659ac7e5c06be40039476a67e08
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-sms"></a>Xamarin.Essentials SMS

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **Sms** classe consente a un'applicazione aprire l'applicazione di SMS predefinito con un messaggio da inviare a un destinatario specificato.

## <a name="using-sms"></a>Utilizzo di Sms

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità SMS funziona chiamando il `ComposeAsync` metodo un `SmsMessage` che contiene il destinatario del messaggio e il corpo del messaggio, entrambi i quali sono facoltativi.

```csharp
public class SmsTest
{
    public async Task SendSms(string messageText, string recipient)
    {
        try
        {
            var message = new SmsMessage(messageText, recipient);
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

- [Codice sorgente di SMS](https://github.com/xamarin/Essentials/tree/master/Essentials/Sms)
- [Documentazione dell'API di SMS](xref:Xamarin.Essentials.Sms)
