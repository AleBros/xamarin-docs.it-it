---
title: Layout per le applicazioni Desktop e Tablet
ms.topic: article
ms.prod: xamarin
ms.assetid: D62F472B-4345-4983-8403-659A538B591F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/01/2016
ms.openlocfilehash: f870cda73625197fb15bf19be1cdabbd675124d6
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="layout-for-tablet-and-desktop-apps"></a>Layout per le applicazioni Desktop e Tablet

Xamarin. Forms supporta tutti i tipi di dispositivo disponibili sulle piattaforme supportate, in modo oltre a telefoni, è inoltre possono eseguire applicazioni su:

* iPad,
* Tablet Android,
* Tablet Windows e computer desktop (che eseguono Windows 8.1 o Windows 10).

Questa pagina vengono descritti brevemente:

* supportato [tipi di dispositivo](#Device_Types), e
* come [ottimizzare](#optimize) layout per i Tablet e telefoni.

<a name="Device_Types" />

## <a name="device-types"></a>Tipi di dispositivo

Dispositivi dello schermo più grandi sono disponibili per tutte le piattaforme supportate da xamarin. Forms.

### <a name="ipads-ios"></a>iPads (iOS)

Il modello di xamarin. Forms include automaticamente il supporto iPad configurando il **Info. plist > dispositivi** impostando su **Universal** (ovvero iPhone e iPad sono supportati).

Per fornire un'esperienza di avvio piacevole e verificare la risoluzione dello schermo completo viene utilizzata in tutti i dispositivi, verificare che un [schermata di avvio specifiche per iPad](~/ios/app-fundamentals/images-icons/launch-screens.md) (utilizzando uno storyboard) viene fornito. In questo modo che viene eseguito il rendering correttamente l'app in dispositivi di iPad mini, iPad e iPad Pro.

Prima di iOS 9 tutte le app richiedeva l'intero schermo nel dispositivo, ma ora è possibile eseguire alcune iPad [dividere il multitasking schermata](~/ios/platform/multitasking.md).
Ciò significa che l'app potrebbe richiedere una semplice colonna sottile lato dello schermo, 50% della larghezza della schermata o l'intero schermo.

[![](tablet-images/ipad-sml.png "iPad esempio schermata Split")](tablet-images/ipad.png#lightbox "iPad esempio schermata Split")

Funzionalità con schermo diviso significa che è necessario progettare l'app per funzionare bene con meno di 320 pixel wide o quanto 1366 pixel di larghezza.

### <a name="android-tablets"></a>Tablet Android

L'ecosistema Android dispone di numerose delle dimensioni supportata dello schermo, dai telefoni piccoli fino a Tablet di grandi dimensioni. Xamarin. Forms può supportare tutte le dimensioni dello schermo, ma come con altre piattaforme si potrebbe voler modificare l'interfaccia utente per i dispositivi più grandi.

Per supportare molti diverse risoluzioni dello schermo, è possibile fornire le risorse di immagini native in diverse dimensioni per ottimizzare l'esperienza utente.
Esaminare il [risorse Android](~/android/app-fundamentals/resources-in-android/index.md) documentazione (e in particolare [la creazione di risorse per diverse dimensioni dello schermo](~/android/app-fundamentals/resources-in-android/resources-for-varying-screens.md)) per ulteriori informazioni su come strutturare le cartelle e i nomi di file nell'app Android progetto per includere risorse immagine ottimizzata in app.

### <a name="windows-tablets-and-desktops"></a>Desktop e i Tablet Windows

Per supportare i Tablet e computer desktop che eseguono Windows, è necessario utilizzare uno dei due tipi di progetto supportati:

* [Windows 8.1](~/xamarin-forms/platform/windows/installation/tablet.md) -
  compila le App in modo specifico per i Tablet Windows 8.1 e desktop.
* [Supporto della piattaforma UWP Windows](~/xamarin-forms/platform/windows/installation/universal.md) -
  compila universal App eseguite in Windows 10 telefoni, Tablet e desktop.

App in esecuzione su computer desktop e i Tablet Windows può essere ridimensionata alle dimensioni arbitrari inoltre a schermo intero in esecuzione.

[![](tablet-images/splitscreen-sml.png "Windows dividere schermata esempio")](tablet-images/splitscreen.png#lightbox "Windows dividere l'esempio di schermata")


<a name="optimize" />

## <a name="optimizing-for-tablet-and-desktop"></a>Ottimizzazione di Tablet PC e Desktop

È possibile regolare l'interfaccia utente xamarin. Forms a seconda se un telefono o tablet/desktop dispositivo è in uso. Ciò significa che è possibile ottimizzare l'esperienza utente per i dispositivi di schermi di grandi dimensioni, ad esempio Tablet e PC.


### <a name="deviceidiom"></a>Device.Idiom

È possibile utilizzare il [ `Device` ](~/xamarin-forms/platform/device.md) classe per modificare il comportamento dell'interfaccia utente o app. Utilizzo di `Device.Idiom` enumerazione, è possibile

```csharp
if (Device.Idiom == TargetIdiom.Phone)
{
    HeroImage.Source = ImageSource.FromFile("hero.jpg");
} else {
    HeroImage.Source = ImageSource.FromFile("herotablet.jpg");
}
```

Questo approccio può essere espanso per apportare modifiche significative ai layout di pagina singolo, o anche per il rendering delle pagine di completamente diverse su schermi di dimensioni maggiori.

### <a name="leveraging-masterdetailpage"></a>Sfruttando MasterDetailPage

Il [ `MasterDetailPage` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MasterDetailPage/) è ideale per schermi di dimensioni maggiori, soprattutto in un iPad in cui viene utilizzato il [ `UISplitViewController` ](https://developer.xamarin.com/api/type/UIKit.UISplitViewController/) per fornire un'esperienza iOS nativo.

Revisione [questo post di blog di Xamarin](https://blog.xamarin.com/bringing-xamarin-forms-apps-to-tablets/) per vedere come è possibile adattare l'interfaccia utente in modo che telefoni un layout di schermi di dimensioni maggiori è possono utilizzare un altro (con il `MasterDetailPage`).



## <a name="related-links"></a>Collegamenti correlati

- [Blog di Xamarin](https://blog.xamarin.com/bringing-xamarin-forms-apps-to-tablets/)
- [Esempio MyShoppe](https://github.com/jamesmontemagno/myshoppe)
