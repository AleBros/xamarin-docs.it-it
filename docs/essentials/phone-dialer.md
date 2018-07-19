---
title: 'Xamarin.Essentials: Dialer telefono'
description: La classe PhoneDialer in Xamarin.Essentials consente a un'applicazione aprire un numero di telefono per la connessione
ms.assetid: E7457942-4D7B-4195-A2FF-417919B9537F
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 34a6c80836d8cb42b1f8fd95718fe248d4701c0f
ms.sourcegitcommit: 7f2e44e6f628753e06a5fe2a3076fc2ec5baa081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2018
ms.locfileid: "39130793"
---
# <a name="xamarinessentials-phone-dialer"></a>Xamarin.Essentials: Dialer telefono

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **PhoneDialer** classe consente a un'applicazione aprire un numero di telefono per la connessione.

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
