---
title: Stile separatore ListView in iOS
description: Le specifiche della piattaforma consentono di utilizzare funzionalità disponibili solo su una piattaforma specifica, senza implementare Renderer o effetti personalizzati. Questo articolo illustra come usare la specifica della piattaforma iOS che controlla se il separatore tra le celle in un controllo ListView usa l'intera larghezza del controllo ListView.
ms.prod: xamarin
ms.assetid: A4CB45CE-9FB7-47ED-8C3D-93E39BF282E4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: c8d660896684283ba9b40cde168adbfe30ca0c51
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84135993"
---
# <a name="listview-separator-style-on-ios"></a>Stile separatore ListView in iOS

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questa specifica della piattaforma iOS controlla se il separatore tra le celle in a [`ListView`](xref:Xamarin.Forms.ListView) Usa la larghezza completa di `ListView` . Viene utilizzato in XAML impostando la [`ListView.SeparatorStyle`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SeparatorStyleProperty) proprietà associata su un valore dell' [`SeparatorStyle`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) enumerazione:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <ListView ... ios:ListView.SeparatorStyle="FullWidth">
            ...
        </ListView>
    </StackLayout>
</ContentPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetSeparatorStyle(SeparatorStyle.FullWidth);
```

Il `ListView.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. [ `ListView.SetSeparatorStyle` ] (Xrif: Xamarin.Forms . PlatformConfiguration. iOSSpecific. ListView. SetSeparatorStyle ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . ListView}, Xamarin.Forms . Il metodo PlatformConfiguration. iOSSpecific. SeparatorStyle), nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, viene usato per controllare se il separatore tra celle in [`ListView`](xref:Xamarin.Forms.ListView) Usa la larghezza intera di `ListView` , con l' [`SeparatorStyle`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) enumerazione che fornisce due valori possibili:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.Default): indica il comportamento predefinito del separatore iOS. Si tratta del comportamento predefinito in Xamarin.Forms .
- [`FullWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.FullWidth): indica che i separatori verranno disegnati da un bordo dell'oggetto `ListView` all'altro.

Il risultato è che un [`SeparatorStyle`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) valore specificato viene applicato a [`ListView`](xref:Xamarin.Forms.ListView) , che controlla la larghezza del separatore tra celle:

![](listview-separator-style-images/listview-separatorstyle.png "ListView SeparatorStyle Platform-Specific")

> [!NOTE]
> Quando lo stile del separatore è stato impostato su `FullWidth` , non è possibile modificarlo di nuovo in in fase di `Default` esecuzione.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
