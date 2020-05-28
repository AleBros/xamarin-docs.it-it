---
title:'' Xamarin.Essentials : Phone Dialer '' Description:' la classe PhoneDialer in Xamarin.Essentials consente a un'applicazione di aprire un numero di telefono nel dialer ' ms. AssetID: Author: ms. Custom: ms. Author: ms. Date: No-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---

# <a name="xamarinessentials-phone-dialer"></a>Xamarin.Essentials: Telefono telefonico

La classe **PhoneDialer** consente a un'applicazione di aprire un numero di telefono dal dialer.

## <a name="get-started"></a>Introduzione

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-phone-dialer"></a>Uso di Phone Dialer

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità Phone Dialer opera chiamando il metodo `Open` con un numero di telefono con cui aprire il dialer. Quando `Open` viene richiesto, l'API prova automaticamente a formattare il numero in base al prefisso internazionale, se specificato.

```csharp
public class PhoneDialerTest
{
    public void PlacePhoneCall(string number)
    {
        try
        {
            PhoneDialer.Open(number);
        }
        catch (ArgumentNullException anEx)
        {
            // Number was null or white space
        }
        catch (FeatureNotSupportedException ex)
        {
            // Phone Dialer is not supported on this device.
        }
        catch (Exception ex)
        {
            // Other error has occurred.
        }
    }
}
```

## <a name="api"></a>API

- [Codice sorgente di Phone Dialer](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/PhoneDialer)
- [Documentazione dell'API Phone Dialer](xref:Xamarin.Essentials.PhoneDialer)

## <a name="related-video"></a>Video correlato

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Phone-Dialer-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
