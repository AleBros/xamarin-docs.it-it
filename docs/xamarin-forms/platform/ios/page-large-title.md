---
title: Titoli di pagine di grandi dimensioni in iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare la specifica della piattaforma iOS che Visualizza il titolo della pagina come titolo grande nella barra di spostamento di un NavigationPage.
ms.prod: xamarin
ms.assetid: 45FD9145-8319-452C-9AE6-624431A4D43C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: ab9becf2f7363674346abf004c1748cb06eb0d31
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68655417"
---
# <a name="large-page-titles-on-ios"></a>Titoli di pagine di grandi dimensioni in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questo specifico della piattaforma iOS viene usato per visualizzare il titolo della pagina come titolo grande nella barra di navigazione di un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), per i dispositivi che usano iOS 11 o versione successiva. Un grande titolo è allineato a sinistra Usa un carattere di dimensioni maggiori e passa a un titolo standard come l'utente inizia a scorrimento di contenuto, in modo che l'area dello schermo viene usato in modo efficiente. Tuttavia, con orientamento orizzontale, il titolo verrà restituito al centro della barra di spostamento per ottimizzare il layout del contenuto. Vengono utilizzati in XAML, impostando il `NavigationPage.PrefersLargeTitles` proprietà associata una `boolean` valore:

```xaml
<NavigationPage xmlns="http://xamarin.com/schemas/2014/forms"
                xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ...
                ios:NavigationPage.PrefersLargeTitles="true">
  ...
</NavigationPage>
```

In alternativa può essere usato dal codice C# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var navigationPage = new Xamarin.Forms.NavigationPage(new iOSLargeTitlePageCS());
navigationPage.On<iOS>().SetPrefersLargeTitles(true);
```

Il `NavigationPage.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il `NavigationPage.SetPrefersLargeTitle` metodo, nelle [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, controlla se sono abilitati i titoli di grandi dimensioni.

Condizione che i titoli di grandi dimensioni sono abilitati nel [`NavigationPage`](xref:Xamarin.Forms.NavigationPage), tutte le pagine nello stack di navigazione verranno visualizzate titoli di grandi dimensioni. Questo comportamento può essere sottoposto a override nelle pagine impostando il `Page.LargeTitleDisplay` proprietà associata a un valore di `LargeTitleDisplayMode` enumerazione:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Large Title"
             ios:Page.LargeTitleDisplay="Never">
  ...
</ContentPage>
```

In alternativa, il comportamento di pagina può essere sottoposto a override dal codice C# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

public class iOSLargeTitlePageCS : ContentPage
{
    public iOSLargeTitlePageCS(ICommand restore)
    {
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        ...
    }
    ...
}
```

Il `Page.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il `Page.SetLargeTitleDisplay` metodo, nel [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, controlla il comportamento di titolo di grandi dimensioni nel [`Page`](xref:Xamarin.Forms.Page), con il `LargeTitleDisplayMode` enumerazione fornendo tre possibili valori:

- `Always` : forza la barra di spostamento e il tipo di carattere per utilizzare il formato di grande dimensioni.
- `Automatic` -usare lo stesso stile (grande o piccolo) come l'elemento precedente nello stack di navigazione.
- `Never` : forzare l'utilizzo della barra di spostamento formato regolare di piccole dimensioni.

Inoltre, il `SetLargeTitleDisplay` metodo può essere utilizzato per attivare o disattivare i valori di enumerazione chiamando il `LargeTitleDisplay` metodo, che restituisce l'attuale `LargeTitleDisplayMode`:

```csharp
switch (On<iOS>().LargeTitleDisplay())
{
    case LargeTitleDisplayMode.Always:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Automatic);
        break;
    case LargeTitleDisplayMode.Automatic:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Never);
        break;
    case LargeTitleDisplayMode.Never:
        On<iOS>().SetLargeTitleDisplay(LargeTitleDisplayMode.Always);
        break;
}
```

Il risultato è che un oggetto specificato `LargeTitleDisplayMode` viene applicato per il [`Page`](xref:Xamarin.Forms.Page), che controlla il comportamento di titolo di grandi dimensioni:

![](page-large-title-images/large-title.png "Specifiche della piattaforma effetto di sfocatura")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
