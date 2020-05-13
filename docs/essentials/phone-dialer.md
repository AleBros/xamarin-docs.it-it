---
title: 'Xamarin.Essentials: Phone Dialer'
description: La classe PhoneDialer in Xamarin.Essentials consente a un'applicazione di aprire un numero di telefono dal dialer
ms.assetid: E7457942-4D7B-4195-A2FF-417919B9537F
author: jamesmontemagno
ms.custom: video
ms.author: jamont
ms.date: 07/02/2019
ms.openlocfilehash: a955399b40f26d4a03a4047d56f7bebe3ad5b5c4
ms.sourcegitcommit: 83cf2a4d99546751c6394510a463a2b2a8bf75b8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/13/2020
ms.locfileid: "83150052"
---
# <a name="xamarinessentials-phone-dialer"></a>Xamarin.Essentials: Phone Dialer

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
