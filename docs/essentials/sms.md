---
title: 'Xamarin.Essentials: SMS'
description: La classe di Sms in Xamarin.Essentials consente a un'applicazione aprire l'applicazione di SMS predefinito con un messaggio da inviare a un destinatario specificato.
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: a93a67b83ea8f435a5e3ad5d26e1d6cbbb7092f7
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38815597"
---
# <a name="xamarinessentials-sms"></a>Xamarin.Essentials: SMS

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **Sms** classe consente a un'applicazione aprire l'applicazione di SMS predefinito con un messaggio da inviare a un destinatario specificato.

## <a name="using-sms"></a>Utilizzo di Sms

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità SMS funziona chiamando il `ComposeAsync` metodo un `SmsMessage` che contiene il destinatario e il corpo del messaggio, che sono facoltativi.

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

- [Codice sorgente di SMS](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Sms)
- [Documentazione dell'API di SMS](xref:Xamarin.Essentials.Sms)
