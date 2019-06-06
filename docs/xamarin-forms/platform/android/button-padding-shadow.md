---
title: Pulsante spaziatura interna e ombreggiature in Android
description: Funzionalità specifiche della piattaforma consentono di utilizzare funzionalità che è disponibile solo in una piattaforma specifica, senza implementare renderer personalizzati o gli effetti. Questo articolo illustra come usare Android specifico della piattaforma che utilizza la spaziatura interna predefinita e i valori dell'ombreggiatura di pulsanti di Android.
ms.prod: xamarin
ms.assetid: BD2B60D1-DE6E-4691-A777-8EC5F560A4E9
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 07/10/2018
ms.openlocfilehash: bcdea14ba6a4a9a09a7b35db67794709d6d36c4c
ms.sourcegitcommit: d3f48bfe72bfe03aca247d47bc64bfbfad1d8071
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66741040"
---
# <a name="button-padding-and-shadows-on-android"></a>Pulsante spaziatura interna e ombreggiature in Android

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)

Questo specifico della piattaforma Android controlla se i pulsanti di xamarin. Forms usano la spaziatura interna predefinita e i valori di ombreggiatura di pulsanti di Android. Vengono utilizzati in XAML, impostando il [ `Button.UseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultPaddingProperty) e [ `Button.UseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultShadowProperty) proprietà connesse alla `boolean` valori:

```xaml
<ContentPage ...
            xmlns:android="clr-namespace:Xamarin.Forms.PlatformConfiguration.AndroidSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        ...
        <Button ...
                android:Button.UseDefaultPadding="true"
                android:Button.UseDefaultShadow="true" />         
    </StackLayout>
</ContentPage>
```

In alternativa, può essere usato dal codice C# che utilizza l'API fluent:

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.AndroidSpecific;
...

button.On<Android>().SetUseDefaultPadding(true).SetUseDefaultShadow(true);
```

Il `Button.On<Android>` metodo consente di specificare che questo specifico della piattaforma verrà eseguito solo in Android. Il [ `Button.SetUseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.SetUseDefaultPadding(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button},System.Boolean)) e [ `Button.SetUseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.SetUseDefaultShadow(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button},System.Boolean)) metodi, nel [ `Xamarin.Forms.PlatformConfiguration.AndroidSpecific` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) dello spazio dei nomi, usate per controllare se il valore predefinito di usare i pulsanti di xamarin. Forms spaziatura interna e i valori dell'ombreggiatura di pulsanti di Android. Inoltre, il [ `Button.UseDefaultPadding` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultPadding(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button})) e [ `Button.UseDefaultShadow` ](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.Button.UseDefaultShadow(Xamarin.Forms.IPlatformElementConfiguration{Xamarin.Forms.PlatformConfiguration.Android,Xamarin.Forms.Button})) metodi possono essere utilizzati da restituire se un pulsante utilizza il valore predefinito di riempimento valore e il valore predefinito dell'ombreggiatura, rispettivamente.

Il risultato è che i pulsanti di xamarin. Forms è possono usare la spaziatura interna predefinita e i valori di ombreggiatura di pulsanti di Android:

![](button-padding-shadow-images/button-padding-and-shadow.png "Spaziatura interna predefinita e i valori di Shadow sui pulsanti di Android")

Si noti che nella schermata sopra ogni [ `Button` ](xref:Xamarin.Forms.Button) ha definizioni identiche, tranne il fatto che a destra `Button` Usa la spaziatura interna predefinita e i valori di ombreggiatura di pulsanti di Android.

## <a name="related-links"></a>Collegamenti correlati

- [PlatformSpecifics (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/PlatformSpecifics/)
- [Creazione di funzionalità specifiche della piattaforma](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [AndroidSpecific API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific)
- [AndroidSpecific.AppCompat API](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat)
