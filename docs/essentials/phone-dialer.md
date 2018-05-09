---
title: Xamarin.Essentials telefonica
description: La classe PhoneDialer consente a un'applicazione aprire un collegamento web nel browser preferito sistema ottimizzato o browser esterno.
ms.assetid: E7457942-4D7B-4195-A2FF-417919B9537F
ms.technology: xamarin-crossplatform
author: jamesmontemagno
ms.author: jamont
ms.date: 05/04/2018
ms.openlocfilehash: 70e43d58eab562f032b59edf7095ca2614af8082
ms.sourcegitcommit: 46d3c9daa45350bdd536d9e105517f3c1c753c5b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
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
