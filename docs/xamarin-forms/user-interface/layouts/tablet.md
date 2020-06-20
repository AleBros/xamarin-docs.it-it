---
title: Layout per le app tablet e desktop
description: Questo articolo illustra come ottimizzare Xamarin.Forms i layout delle applicazioni per tablet, anziché telefoni.
ms.prod: xamarin
ms.assetid: D62F472B-4345-4983-8403-659A538B591F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/01/2016
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 0ecbc850960465296dc4047277bdafe78ac800a4
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84573248"
---
# <a name="layout-for-tablet-and-desktop-apps"></a>Layout per le app tablet e desktop

Xamarin.Formssupporta tutti i tipi di dispositivi disponibili sulle piattaforme supportate, quindi, oltre ai telefoni, le app possono essere eseguite anche in:

- iPad
- Tablet Android,
- Tablet Windows e computer desktop (in esecuzione Windows 10).

In questa pagina viene brevemente illustrato quanto segue:

- i [tipi di dispositivo](#device-types)supportati e
- come [ottimizzare](#optimize-for-tablet-and-desktop) i layout per tablet e telefoni.

## <a name="device-types"></a>Tipi di dispositivi

I dispositivi schermo di dimensioni maggiori sono disponibili per tutte le piattaforme supportate da Xamarin.Forms .

### <a name="ipads-ios"></a>iPad (iOS)

Il Xamarin.Forms modello include automaticamente il supporto per iPad configurando il file **info. plist > i dispositivi** impostando **universalmente** , ovvero iPhone e iPad sono supportati.

Per offrire un'esperienza di avvio piacevole e garantire che la risoluzione a schermo intero venga usata in tutti i dispositivi, è necessario assicurarsi che venga fornita una [schermata di avvio specifica dell'iPad](~/ios/app-fundamentals/images-icons/launch-screens.md) (usando uno storyboard). In questo modo l'app viene sottoposta a rendering corretta nei dispositivi iPad mini, iPad e iPad Pro.

Prima di iOS 9 tutte le app occupavano l'intero schermo del dispositivo, ma alcuni iPad ora possono eseguire il [multitasking a schermo diviso](~/ios/platform/multitasking.md).
Ciò significa che l'app potrebbe occupare solo una colonna sottile sul lato dello schermo, il 50% della larghezza dello schermo o l'intero schermo.

[![](tablet-images/ipad-sml.png "iPad Split Screen Example")](tablet-images/ipad.png#lightbox "iPad Split Screen Example")

La funzionalità Split Screen significa che è consigliabile progettare l'applicazione in modo che funzioni bene con un minimo di 320 pixel di larghezza o fino a 1366 pixel di larghezza.

### <a name="android-tablets"></a>Tablet Android

L'ecosistema Android presenta una miriade di dimensioni dello schermo supportate, da piccoli telefoni fino a tablet di grandi dimensioni. Xamarin.Formspuò supportare tutte le dimensioni dello schermo, ma come per le altre piattaforme potrebbe essere necessario modificare l'interfaccia utente per i dispositivi più grandi.

Quando si supportano molte risoluzioni dello schermo diverse, è possibile fornire le risorse dell'immagine nativa in dimensioni diverse per ottimizzare l'esperienza utente.
Esaminare la documentazione sulle [risorse Android](~/android/app-fundamentals/resources-in-android/index.md) (e, in particolare, [creare risorse per le diverse dimensioni dello schermo](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)) per altre informazioni su come strutturare le cartelle e i nomi di file nel progetto di app Android per includere le risorse immagine ottimizzate nell'app.

### <a name="windows-tablets-and-desktops"></a>Tablet e desktop di Windows

Per supportare tablet e computer desktop che eseguono Windows, è necessario usare il [supporto di Windows UWP](~/xamarin-forms/platform/windows/installation/index.md), che consente di compilare app universali eseguite in Windows 10.

Le app in esecuzione su tablet e desktop di Windows possono essere ridimensionate in dimensioni arbitrarie, oltre all'esecuzione a schermo intero.

[![](tablet-images/splitscreen-sml.png "Windows Split Screen Example")](tablet-images/splitscreen.png#lightbox "Windows Split Screen Example")

## <a name="optimize-for-tablet-and-desktop"></a>Ottimizza per tablet e desktop

È possibile modificare l' Xamarin.Forms interfaccia utente a seconda che sia in uso un telefono o un tablet/dispositivo desktop. Ciò significa che è possibile ottimizzare l'esperienza utente per i dispositivi a schermo intero, ad esempio tablet e computer desktop.

### <a name="deviceidiom"></a>Device. Idiom

È possibile usare la [`Device`](~/xamarin-forms/platform/device.md) classe per modificare il comportamento dell'app o dell'interfaccia utente. Utilizzando l' `Device.Idiom` enumerazione è possibile

```csharp
if (Device.Idiom == TargetIdiom.Phone)
{
    HeroImage.Source = ImageSource.FromFile("hero.jpg");
} else {
    HeroImage.Source = ImageSource.FromFile("herotablet.jpg");
}
```

Questo approccio può essere espanso per apportare modifiche significative ai singoli layout di pagina o persino per eseguire il rendering di pagine completamente diverse su schermi più grandi.

### <a name="leverage-masterdetailpage"></a>Utilizzare MasterDetailPage

[`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)È ideale per schermi di dimensioni maggiori, soprattutto sull'iPad in cui USA [`UISplitViewController`](xref:UIKit.UISplitViewController) per offrire un'esperienza iOS nativa.

Vedere [questo post di Blog di Novell](https://devblogs.microsoft.com/xamarin/bringing-xamarin-forms-apps-to-tablets/) per informazioni su come adattare l'interfaccia utente in modo che i telefoni usino un layout e schermi più grandi possano usarlo con un altro (con `MasterDetailPage` ).

## <a name="related-links"></a>Collegamenti correlati

- [Blog di Novell](https://devblogs.microsoft.com/xamarin/bringing-xamarin-forms-apps-to-tablets/)
- [Esempio di reshoppe](https://github.com/jamesmontemagno/myshoppe)
