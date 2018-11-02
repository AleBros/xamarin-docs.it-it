---
title: 'Xamarin.Essentials: SMS'
description: La classe Sms in Xamarin.Essentials consente a un'applicazione di aprire l'applicazione predefinita per gli SMS con un messaggio specificato da inviare a un destinatario.
ms.assetid: 81A757F2-6F2A-458F-B9BE-770ADEBFAB58
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: fa55a17e99a11861b98c4d515df882ed3af58a0b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50102737"
---
# <a name="xamarinessentials-sms"></a>Xamarin.Essentials: SMS

![NuGet in versione non definitiva](~/media/shared/pre-release.png)

La classe **Sms** consente a un'applicazione di aprire l'applicazione predefinita per gli SMS con un messaggio specificato da inviare a un destinatario.

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
