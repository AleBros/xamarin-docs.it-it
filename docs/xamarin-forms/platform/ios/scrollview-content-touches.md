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
ms.openlocfilehash: 9b8f743b2c3d7f4b38feb4cfc5015b1113620562
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84137098"
---
# <a name="scrollview-content-touches-on-ios"></a>Tocchi di contenuto ScrollView in iOS

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Un timer implicito viene attivato quando un movimento tocco inizia in un [`ScrollView`](xref:Xamarin.Forms.ScrollView) in iOS e `ScrollView` decide, in base all'azione dell'utente all'interno dell'intervallo del timer, se deve gestire il movimento o passarlo al relativo contenuto. Per impostazione predefinita, iOS `ScrollView` ritarda i ritardi del contenuto, ma ciò può causare problemi in alcune circostanze con il `ScrollView` contenuto che non vince il gesto quando necessario. Di conseguenza, questa specifica della piattaforma controlla se un oggetto `ScrollView` gestisce un movimento tocco o lo passa al relativo contenuto. Viene utilizzato in XAML impostando la `ScrollView.ShouldDelayContentTouches` proprietà associata su un `boolean` valore:

```xaml
<MasterDetailPage ...
                  xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <MasterDetailPage.Master>
        <ContentPage Title="Menu" BackgroundColor="Blue" />
    </MasterDetailPage.Master>
    <MasterDetailPage.Detail>
        <ContentPage>
            <ScrollView x:Name="scrollView" ios:ScrollView.ShouldDelayContentTouches="false">
                <StackLayout Margin="0,20">
                    <Slider />
                    <Button Text="Toggle ScrollView DelayContentTouches" Clicked="OnButtonClicked" />
                </StackLayout>
            </ScrollView>
        </ContentPage>
    </MasterDetailPage.Detail>
</MasterDetailPage>
```

In alternativa, può essere utilizzato da C# utilizzando l'API Fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

scrollView.On<iOS>().SetShouldDelayContentTouches(false);
```

Il `ScrollView.On<iOS>` metodo specifica che questa specifica della piattaforma verrà eseguita solo in iOS. Il `ScrollView.SetShouldDelayContentTouches` metodo, nello [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) spazio dei nomi, viene utilizzato per controllare se un oggetto [`ScrollView`](xref:Xamarin.Forms.ScrollView) gestisce un movimento tocco o lo passa al contenuto. Inoltre, il `SetShouldDelayContentTouches` metodo può essere usato per abilitare o disabilitare i ritocchi di contenuto ritardando chiamando il `ShouldDelayContentTouches` metodo per restituire se i ritocchi del contenuto vengono posticipati:

```csharp
scrollView.On<iOS>().SetShouldDelayContentTouches(!scrollView.On<iOS>().ShouldDelayContentTouches());
```

Il risultato è che un [`ScrollView`](xref:Xamarin.Forms.ScrollView) può disabilitare la ricezione dei ritardi di contenuto, in modo che in questo scenario [`Slider`](xref:Xamarin.Forms.Slider) riceva il gesto anziché la [`Detail`](xref:Xamarin.Forms.MasterDetailPage.Detail) pagina di [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) :

[![](scrollview-content-touches-images/scrollview-delay-content-touches.png "ScrollView Delay Content Touches Platform-Specific")](scrollview-content-touches-images/scrollview-delay-content-touches-large.png#lightbox "ScrollView Delay Content Touches Platform-Specific")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
