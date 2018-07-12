---
title: 'Xamarin.Essentials: Dialer telefono'
description: La classe PhoneDialer in Xamarin.Essentials consente a un'applicazione aprire un collegamento web nel browser preferito di sistema ottimizzato o il browser esterno.
ms.assetid: E7457942-4D7B-4195-A2FF-417919B9537F
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 6733e43ed4174d1dd78b2e8f70268eb54adadb98
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38831397"
---
# <a name="xamarinessentials-phone-dialer"></a>Xamarin.Essentials: Dialer telefono

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **PhoneDialer** classe consente a un'applicazione aprire un collegamento web nel browser preferito di sistema ottimizzato o il browser esterno.

## <a name="using-phone-dialer"></a>Utilizzando telefonica

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità Dialer telefono funziona chiamando il `Open` metodo con un numero di telefono per aprire la connessione con. Quando si `Open` è richiesto l'API tenterà automaticamente di formattare il numero basato il codice paese, se specificato.

```csharp
public class PhoneDialerTest
{
    public async Task PlacePhoneCall(string number)
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

- [Connessione telefonica codice sorgente](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/PhoneDialer)
- [Documentazione dell'API Dialer telefono](xref:Xamarin.Essentials.PhoneDialer)
