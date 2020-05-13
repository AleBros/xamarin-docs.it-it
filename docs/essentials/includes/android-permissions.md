---
ms.topic: include
author: jamesmontemagno
ms.author: jamont
ms.date: 05/11/2020
ms.openlocfilehash: b5396061de657690fa3f4cdf68e8a94382918613
ms.sourcegitcommit: 83cf2a4d99546751c6394510a463a2b2a8bf75b8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/13/2020
ms.locfileid: "83149749"
---
Questa API usa le autorizzazioni di runtime in Android. Assicurarsi che Novell. Essentials sia completamente inizializzato e che la gestione delle autorizzazioni sia impostata nell'app.

In `MainLauncher` o per qualsiasi `Activity` avviata del progetto Android, Xamarin.Essentials deve essere inizializzato nel metodo `OnCreate`:

```csharp
protected override void OnCreate(Bundle savedInstanceState) 
{
    //...
    base.OnCreate(savedInstanceState);
    Xamarin.Essentials.Platform.Init(this, savedInstanceState); // add this line to your code, it may also be called: bundle
    //...
}    
```

Per gestire le autorizzazioni di runtime per Android, Xamarin.Essentials deve ricevere qualsiasi `OnRequestPermissionsResult`. Aggiungere il codice seguente a tutte le classi `Activity`:

```csharp
public override void OnRequestPermissionsResult(int requestCode, string[] permissions, Android.Content.PM.Permission[] grantResults)
{
    Xamarin.Essentials.Platform.OnRequestPermissionsResult(requestCode, permissions, grantResults);

    base.OnRequestPermissionsResult(requestCode, permissions, grantResults);
}
```
