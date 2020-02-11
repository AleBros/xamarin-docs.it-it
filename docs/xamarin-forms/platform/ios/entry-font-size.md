---
title: Dimensioni del carattere voce in iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare la specifica della piattaforma iOS che ridimensiona le dimensioni del carattere di una voce.
ms.prod: xamarin
ms.assetid: E8881D4E-902B-4397-A43E-916B2885EC87
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: f0f5ca8aa7959d5c305c29c96f972dccc43a65ff
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68648145"
---
# <a name="entry-font-size-on-ios"></a>Dimensioni del carattere voce in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Questo specifico della piattaforma iOS viene usato per ridimensionare le dimensioni del carattere [`Entry`](xref:Xamarin.Forms.Entry) di un oggetto per garantire che il testo inserito si adatta al controllo. Vengono utilizzati in XAML, impostando il [`Entry.AdjustsFontSizeToFitWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidthProperty) proprietà associata una `boolean` valore:

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
    <StackLayout Margin="20">
        <Entry x:Name="entry"
               Placeholder="Enter text here to see the font size change"
               FontSize="22"
               ios:Entry.AdjustsFontSizeToFitWidth="true" />
        ...
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice C# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

entry.On<iOS>().EnableAdjustsFontSizeToFitWidth();
```

Il `Entry.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il [`Entry.EnableAdjustsFontSizeToFitWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.EnableAdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry})) metodo, nel [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, viene usato per aumentare le dimensioni del carattere del testo valori immessi per garantire che si adatta al [`Entry`](xref:Xamarin.Forms.Entry). Inoltre, il [`Entry`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry) classe nel `Xamarin.Forms.PlatformConfiguration.iOSSpecific` dello spazio dei nomi ha anche un [`DisableAdjustsFontSizeToFitWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.DisableAdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry})) metodo che disabilita questo specifico della piattaforma, e un [`SetAdjustsFontSizeToFitWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.SetAdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry},System.Boolean)) metodo che può essere utilizzato per attivare o disattivare la dimensione del carattere ridimensionamento chiamando il [`AdjustsFontSizeToFitWidth`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.Entry.AdjustsFontSizeToFitWidth(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.iOS,Xamarin.Forms.Entry})) metodo:

```csharp
entry.On<iOS>().SetAdjustsFontSizeToFitWidth(!entry.On<iOS>().AdjustsFontSizeToFitWidth());
```

Il risultato è che le dimensioni del carattere i [`Entry`](xref:Xamarin.Forms.Entry) viene ridimensionata per garantire che il testo immessi sia adeguato nel controllo:

![](entry-font-size-images/entry-font-size.png "Regolare specifici delle voci del tipo di carattere delle dimensioni della piattaforma")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
