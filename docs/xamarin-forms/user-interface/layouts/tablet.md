---
title: Layout per le app Desktop e Tablet
description: Questo articolo illustra come ottimizzare i layout dell'applicazione xamarin. Forms per Tablet, anziché i telefoni.
ms.prod: xamarin
ms.assetid: D62F472B-4345-4983-8403-659A538B591F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/01/2016
ms.openlocfilehash: 9d1f54fa4753ba2ef44ba9b8b48a84a3ca932c4b
ms.sourcegitcommit: a1a58afea68912c79d16a3f64de9a0c1feb2aeb4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55233848"
---
# <a name="layout-for-tablet-and-desktop-apps"></a>Layout per le app Desktop e Tablet

Xamarin. Forms supporta tutti i tipi di dispositivo disponibili sulle piattaforme supportate, in modo che oltre a telefoni, è anche possono eseguire le App in:

* iPad,
* Tablet Android,
* Windows Tablet e computer desktop (che eseguono Windows 10).

Questa pagina vengono trattati brevemente:

* supportato [tipi di dispositivo](#Device_Types), e
* saper [ottimizzare](#optimize) layout per Tablet e telefoni.

<a name="Device_Types" />

## <a name="device-types"></a>Tipi di dispositivo

Dispositivi con schermo di dimensioni maggiori sono disponibili per tutte le piattaforme supportate da xamarin. Forms.

### <a name="ipads-ios"></a>iPad (iOS)

Il modello di xamarin. Forms include automaticamente il supporto di iPad configurando il **Info. plist > dispositivi** se si imposta su **universale** (ovvero iPhone e iPad sono supportati).

Per offrire un'esperienza di avvio piacevole e verificare che la risoluzione a schermo intero viene usata in tutti i dispositivi, è necessario assicurarsi che un [schermata di avvio specifiche per iPad](~/ios/app-fundamentals/images-icons/launch-screens.md) (utilizzando uno storyboard) viene fornito. In questo modo che viene eseguito il rendering correttamente l'app in dispositivi iPad mini, iPad e iPad Pro.

Prima di iOS 9 tutte le app a occuparmi dell'intero schermo del dispositivo, ma ora è possibile eseguire alcune iPad [dividere il multitasking schermata](~/ios/platform/multitasking.md).
Ciò significa che l'app può richiedere fino a una semplice colonna slim lato dello schermo, 50% della larghezza della schermata o l'intera schermata.

[![](tablet-images/ipad-sml.png "Dividi schermo esempio iPad")](tablet-images/ipad.png#lightbox "iPad Split schermata di esempio")

Funzionalità con schermo diviso significa che è necessario progettare l'app per l'uso con un po' di 320 pixel wide o quanto più 1366 pixel di larghezza.

### <a name="android-tablets"></a>Tablet Android

L'ecosistema di Android include una miriade di dimensioni dello schermo supportate, dai piccoli telefoni fino a Tablet di grandi dimensioni. Xamarin. Forms può supportare tutte le dimensioni dello schermo, ma come con altre piattaforme è possibile regolare l'interfaccia utente per i dispositivi più grandi.

Supporto molte diverse risoluzioni dello schermo, è possibile fornire le risorse di immagini native in dimensioni diverse per ottimizzare l'esperienza dell'utente.
Rivedere le [risorse Android](~/android/app-fundamentals/resources-in-android/index.md) documentazione (e in particolare [la creazione di risorse per variare le dimensioni dello schermo](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)) per altre informazioni su come strutturare le cartelle e i nomi di file nell'app per Android progetto per includere le risorse immagine ottimizzata nell'app.

### <a name="windows-tablets-and-desktops"></a>Desktop e Tablet Windows

Per supportare Tablet e computer desktop che eseguono Windows, è necessario usare [supporto per Windows UWP](~/xamarin-forms/platform/windows/installation/index.md), quali compilazioni App universali eseguibili in Windows 10.

Le App in esecuzione sul desktop e Tablet Windows possono essere ridimensionate alle dimensioni arbitrari inoltre a schermo intero in esecuzione.

[![](tablet-images/splitscreen-sml.png "Windows suddividere esempio schermata")](tablet-images/splitscreen.png#lightbox "Windows suddividere l'esempio di schermata")


<a name="optimize" />

## <a name="optimizing-for-tablet-and-desktop"></a>Ottimizzazione per Tablet e Desktop

È possibile regolare l'interfaccia utente di xamarin. Forms a seconda se un telefono o tablet/desktop dispositivo è in uso. Ciò significa che è possibile ottimizzare l'esperienza utente per i dispositivi di schermi di grandi dimensioni, come Tablet e computer desktop.


### <a name="deviceidiom"></a>Device.Idiom

È possibile usare la [ `Device` ](~/xamarin-forms/platform/device.md) classe per modificare il comportamento dell'interfaccia utente o app. Uso di `Device.Idiom` enumerazione è possibile

```csharp
if (Device.Idiom == TargetIdiom.Phone)
{
    HeroImage.Source = ImageSource.FromFile("hero.jpg");
} else {
    HeroImage.Source = ImageSource.FromFile("herotablet.jpg");
}
```

Questo approccio può essere espanso per apportare modifiche significative al layout di pagine singole o anche per il rendering delle pagine completamente diverse su schermi di dimensioni maggiori.

### <a name="leveraging-masterdetailpage"></a>Sfruttando MasterDetailPage

Il [ `MasterDetailPage` ](xref:Xamarin.Forms.MasterDetailPage) ideale per schermi di dimensioni maggiori, in particolare su iPad in cui viene utilizzato il [ `UISplitViewController` ](xref:UIKit.UISplitViewController) per offrire un'esperienza nativa per iOS.

Revisione [questo post di blog di Xamarin](https://blog.xamarin.com/bringing-xamarin-forms-apps-to-tablets/) per vedere come è possibile adattare l'interfaccia utente in modo che i telefoni un layout di schermi di dimensioni maggiori possono usare un altro (con il `MasterDetailPage`).



## <a name="related-links"></a>Collegamenti correlati

- [Blog di Xamarin](https://blog.xamarin.com/bringing-xamarin-forms-apps-to-tablets/)
- [Esempio MyShoppe](https://github.com/jamesmontemagno/myshoppe)
