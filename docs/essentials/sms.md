---
title:'' Xamarin.Essentials : SMS '' Description:' la classe SMS in Xamarin.Essentials consente a un'applicazione di aprire l'applicazione SMS predefinita con un messaggio specificato da inviare a un destinatario .'
ms. AssetID: autore: ms. Custom: ms. Author: ms. Date: No-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

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

- [Codice sorgente di Sms](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Sms)
- [Documentazione dell'API Sms](xref:Xamarin.Essentials.Sms)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/SMS-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
