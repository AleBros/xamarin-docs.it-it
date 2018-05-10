---
title: Xamarin.Essentials telefonica
description: La classe PhoneDialer consente a un'applicazione aprire un collegamento web nel browser preferito sistema ottimizzato o browser esterno.
ms.assetid: E7457942-4D7B-4195-A2FF-417919B9537F
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 6bea1b26e43459be12272f9dd2ecc6fafba2b8b4
ms.sourcegitcommit: 0a72c7dea020b965378b6314f558bf5360dbd066
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2018
---
# <a name="xamarinessentials-phone-dialer"></a>Xamarin.Essentials telefonica

![Versione non definitiva NuGet](~/media/shared/pre-release.png)

Il **PhoneDialer** classe consente a un'applicazione aprire un collegamento web nel browser preferito sistema ottimizzato o browser esterno.

## <a name="using-phone-dialer"></a>Utilizzando telefonica

Aggiungere un riferimento a Xamarin.Essentials nella classe:

```csharp
using Xamarin.Essentials;
```

La funzionalità di connessione telefonica funziona chiamando il `Open` metodo con un numero di telefono per aprire connessione telefonica con. Quando `Open` è richiesto l'API tenterà automaticamente di formattare il numero in base al paese codice se non specificato.

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

- [Connessione telefonica codice sorgente](https://github.com/xamarin/Essentials/tree/master/Essentials/PhoneDialer)
- [Documentazione dell'API di connessione telefonica telefono](xref:Xamarin.Essentials.PhoneDialer)
