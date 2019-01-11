---
title: ScrollView contenuto tocco in iOS
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare iOS specifiche della piattaforma che controlla se un ScrollView gestisce un gesto tocco o passarlo al relativo contenuto.
ms.prod: xamarin
ms.assetid: 99F823DB-B379-40F0-A343-A9783C341120
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
ms.openlocfilehash: 7f6ec13b1b21a1526bb53f260c4d80e881e7feba
ms.sourcegitcommit: 395774577f7524b57035c5cca3c9034a4b636489
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54209776"
---
# <a name="scrollview-content-touches-on-ios"></a>ScrollView contenuto tocco in iOS

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)

Un timer implicito viene attivato quando un gesto tocco viene avviata in un [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) in iOS e `ScrollView` decide, in base l'azione dell'utente all'interno dell'intervallo del timer, se deve gestire il movimento o passarlo al relativo contenuto. Per impostazione predefinita, iOS `ScrollView` tocchi contenuto ritardi, ma questo può causare problemi in alcuni casi con il `ScrollView` contenuto non vincere il movimento in un momento opportuno. Di conseguenza, questo controlli specifici della piattaforma se un `ScrollView` gestisce un gesto tocco o passarlo al relativo contenuto. Vengono utilizzati in XAML, impostando il `ScrollView.ShouldDelayContentTouches` proprietà associata una `boolean` valore:

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

In alternativa, può essere usato dal codice c# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

scrollView.On<iOS>().SetShouldDelayContentTouches(false);
```

Il `ScrollView.On<iOS>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in iOS. Il `ScrollView.SetShouldDelayContentTouches` metodo, nel [ `Xamarin.Forms.PlatformConfiguration.iOSSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) dello spazio dei nomi, viene utilizzato per controllare se un [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) gestisce un gesto tocco o passarlo al relativo contenuto. Inoltre, il `SetShouldDelayContentTouches` metodo può essere utilizzato per attivare/disattivare ritardando tocchi contenuti chiamando il `ShouldDelayContentTouches` metodo restituisca se sono posticipati tocchi contenuti:

```csharp
scrollView.On<iOS>().SetShouldDelayContentTouches(!scrollView.On<iOS>().ShouldDelayContentTouches());
```

Il risultato è che un [ `ScrollView` ](xref:Xamarin.Forms.ScrollView) possibile disabilitare ritardare la ricezione di contenuto computerizzati, pertanto, che in questo scenario il [ `Slider` ](xref:Xamarin.Forms.Slider) riceve il movimento anziché il [ `Detail` ](xref:Xamarin.Forms.MasterDetailPage.Detail) della pagina la [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage):

[![](scrollview-content-touches-images/scrollview-delay-content-touches.png "Il contenuto di ritardo ScrollView tocca specifiche della piattaforma")](scrollview-content-touches-images/scrollview-delay-content-touches-large.png#lightbox "ritardo ScrollView contenuto tocca specifiche della piattaforma")

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/userinterface/platformspecifics/)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [iOSSpecific API](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
