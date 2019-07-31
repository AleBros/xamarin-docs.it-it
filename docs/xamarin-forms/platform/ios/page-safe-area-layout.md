---
title: Guida per il layout dell'area sicura in iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare la piattaforma iOS specifica che garantisce che il contenuto della pagina sia posizionato in un'area dello schermo sicura per tutti i dispositivi che usano iOS 11 e versioni successive.
ms.prod: xamarin
ms.assetid: 2B6789C1-39B4-4C16-ADE1-3ED3378EAC63
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: c6a2ec5a4d1466b7118e6cc7b03cc5518b27e2fb
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68644539"
---
# <a name="safe-area-layout-guide-on-ios"></a>Guida per il layout dell'area sicura in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questo specifico della piattaforma iOS viene usato per garantire che il contenuto della pagina sia posizionato in un'area dello schermo sicura per tutti i dispositivi che usano iOS 11 e versioni successive. In particolare, può risultare utile per verificare che il contenuto non viene ritagliato angoli arrotondati dispositivo, l'indicatore principale o alloggiamento del sensore in un iPhone X. Vengono utilizzati in XAML, impostando il `Page.UseSafeArea` proprietà associata una `boolean` valore:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Safe Area"
             ios:Page.UseSafeArea="true">
    <StackLayout>
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice C# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

On<iOS>().SetUseSafeArea(true);
```

Il `Page.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il `Page.SetUseSafeArea` metodo, nel [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, controlla se è abilitata la Guida per il layout area sicura.

Il risultato è che il contenuto della pagina può essere posizionato su un'area dello schermo sicuro per tutti i dispositivi iPhone:

[![](page-safe-area-images/safe-area-layout.png "Guida Layout Area sicura")](page-safe-area-images/safe-area-layout-large.png#lightbox "Guida Layout Area sicura")

> [!NOTE]
> Area di sicurezza definita da Apple usato in xamarin. Forms per impostare il [`Page.Padding`](xref:Xamarin.Forms.Page.Padding) proprietà che avrà la precedenza eventuali valori precedenti di questa proprietà che sono stati impostati.

Area sicura può essere personalizzato tramite il recupero dei relativi [`Thickness`](xref:Xamarin.Forms.Thickness) valore con il `Page.SafeAreaInsets` metodo dal [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi. Può quindi essere modificato come richiesto e riassegnato al `Padding` proprietà nel costruttore della pagina oppure [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) eseguire l'override:

```csharp
protected override void OnAppearing()
{
    base.OnAppearing();

    var safeInsets = On<iOS>().SafeAreaInsets();
    safeInsets.Left = 20;
    Padding = safeInsets;
}
```

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
