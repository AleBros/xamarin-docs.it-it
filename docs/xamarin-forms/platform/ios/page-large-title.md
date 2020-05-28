---
title: ''
description: ''
ms.prod: ''
ms.assetid: ''
ms.technology: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0db20620870340386ccd0cedf7f98cb2975527ba
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84128043"
---
# <a name="large-page-titles-on-ios"></a>Titoli di pagine di grandi dimensioni in iOS

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questo specifico della piattaforma iOS viene usato per visualizzare il titolo della pagina come titolo grande nella barra di navigazione di un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) , per i dispositivi che usano iOS 11 o versione successiva. Un titolo di grandi dimensioni viene allineato a sinistra e utilizza un tipo di carattere più grande e passa a un titolo standard quando l'utente inizia a scorrere il contenuto, in modo che l'area dello schermo venga utilizzata in modo efficiente. Tuttavia, nell'orientamento orizzontale, il titolo tornerà al centro della barra di spostamento per ottimizzare il layout del contenuto. Viene utilizzato in XAML impostando la `NavigationPage.PrefersLargeTitles` proprietà associata su un `boolean` valore:

```xaml
<NavigationPage xmlns="http://xamarin.com/schemas/2014/forms"
                xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                ...
                ios:NavigationPage.PrefersLargeTitles="true">
  ...
</NavigationPage>
```

In alternativa, può essere utilizzato da C# tramite l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

var navigationPage = new Xamarin.Forms.NavigationPage(new iOSLargeTitlePageCS());
navigationPage.On<iOS>().SetPrefersLargeTitles(true);
```

Il `NavigationPage.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. Il `NavigationPage.SetPrefersLargeTitle` metodo, nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, controlla se i titoli di grandi dimensioni sono abilitati.

A condizione che i titoli di grandi dimensioni siano abilitati in [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) , tutte le pagine dello stack di navigazione visualizzeranno titoli di grandi dimensioni. Questo comportamento può essere sostituito sulle pagine impostando la `Page.LargeTitleDisplay` proprietà associata su un valore dell' `LargeTitleDisplayMode` enumerazione:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
             Title="Large Title"
             ios:Page.LargeTitleDisplay="Never">
  ...
</ContentPage>
```

In alternativa, è possibile eseguire l'override del comportamento della pagina da C# usando l'API Fluent:

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

Il `Page.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. Il `Page.SetLargeTitleDisplay` metodo, nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, controlla il comportamento del titolo di grandi dimensioni su [`Page`](xref:Xamarin.Forms.Page) , con l' `LargeTitleDisplayMode` enumerazione che fornisce tre valori possibili:

- `Always`: forza la barra di navigazione e la dimensione del carattere per usare il formato grande.
- `Automatic`: usare lo stesso stile (grande o piccolo) dell'elemento precedente nello stack di navigazione.
- `Never`: forza l'uso della barra di spostamento normale in formato piccolo.

Inoltre, il `SetLargeTitleDisplay` metodo può essere utilizzato per abilitare o disabilitare i valori di enumerazione chiamando il `LargeTitleDisplay` metodo, che restituisce l'oggetto corrente `LargeTitleDisplayMode` :

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

Il risultato è che un oggetto specificato `LargeTitleDisplayMode` viene applicato a [`Page`](xref:Xamarin.Forms.Page) , che controlla il comportamento del titolo di grandi dimensioni:

![](page-large-title-images/large-title.png "Blur Effect Platform-Specific")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
