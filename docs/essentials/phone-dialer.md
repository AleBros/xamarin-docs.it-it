---
title: 'Xamarin.Essentials: Dialer telefono'
description: La classe PhoneDialer in Xamarin.Essentials consente a un'applicazione di aprire un numero di telefono dal dialer
ms.assetid: E7457942-4D7B-4195-A2FF-417919B9537F
author: jamesmontemagno
ms.author: jamont
ms.date: 07/02/2019
ms.openlocfilehash: 0b00307759d95b8e2efe27bd9a17d2c6efd3d5c0
ms.sourcegitcommit: e95296f9e516975f5f32d822c323a71fd84007b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538681"
---
# <a name="xamarinessentials-phone-dialer"></a>Xamarin.Essentials: Dialer telefono

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
