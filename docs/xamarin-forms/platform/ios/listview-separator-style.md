---
title: Stile dei separatori di ListView in iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare iOS specifiche della piattaforma che controlla se il separatore tra le celle in un ListView Usa l'intera larghezza del ListView.
ms.prod: xamarin
ms.assetid: A4CB45CE-9FB7-47ED-8C3D-93E39BF282E4
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 0fc8cdfac22ad54b73af193ce76e8fd27b8fb0da
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209466"
---
# <a name="listview-separator-style-on-ios"></a>Stile dei separatori di ListView in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Questo specifico della piattaforma iOS consente di controllare se il separatore tra le celle una [ `ListView` ](xref:Xamarin.Forms.ListView) Usa l'intera larghezza del `ListView`. Vengono utilizzati in XAML, impostando il [ `ListView.SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SeparatorStyleProperty) proprietà associata a un valore del [ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) enumerazione:

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

In alternativa, può essere usato dal codice C# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

listView.On<iOS>().SetSeparatorStyle(SeparatorStyle.FullWidth);
```

Il `ListView.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il [ `ListView.SetSeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.ListView.SetSeparatorStyle(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.ListView},Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle)) metodo, nel [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, viene usata per controllare se il separatore tra le celle nel [ `ListView` ](xref:Xamarin.Forms.ListView) Usa la versione completa larghezza del `ListView`, con il [ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) enumerazione che fornisce due valori possibili:

- [`Default`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.Default) : indica il comportamento di separatore iOS predefinite. Questo è il comportamento predefinito in xamarin. Forms.
- [`FullWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle.FullWidth) : indica che verranno disegnati un margine di separatori di `ListView` a altro.

Il risultato è che un oggetto specificato [ `SeparatorStyle` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.SeparatorStyle) valore viene applicato per il [ `ListView` ](xref:Xamarin.Forms.ListView), che determina la larghezza del separatore tra le celle:

![](listview-separator-style-images/listview-separatorstyle.png "ListView SeparatorStyle specifiche della piattaforma")

> [!NOTE]
> Dopo aver impostato lo stile dei separatori `FullWidth`, non può essere modificato a `Default` in fase di esecuzione.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
