---
title: 'Xamarin.Essentials: SMS'
description: La classe Sms in Xamarin.Essentials consente a un'applicazione di aprire l'applicazione predefinita per gli SMS con un messaggio specificato da inviare a un destinatario.
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
author: jamesmontemagno
ms.author: jamont
ms.date: 11/04/2018
ms.openlocfilehash: d0655f3e687750e0fca626fb017096a946c0abb3
ms.sourcegitcommit: 01f93a34b466f8d4043cef68fab9b35cd8decee6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2018
ms.locfileid: "52898771"
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

- [Codice sorgente di Sms](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Sms)
- [Documentazione dell'API Sms](xref:Xamarin.Essentials.Sms)
