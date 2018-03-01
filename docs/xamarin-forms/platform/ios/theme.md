---
title: Aggiunta di formattazione specifiche di iOS
ms.topic: article
ms.prod: xamarin
ms.assetid: CE50E207-D092-4D88-8439-1B51F178E7ED
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/29/2016
ms.openlocfilehash: c84bc8286ad5f73acb2d7e9eb13467e2c3bfb9b9
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="adding-ios-specific-formatting"></a>Aggiunta di formattazione specifiche di iOS

Un modo per impostare iOS specifico formattazione consiste nel creare un [renderer personalizzato](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) per un controllo e specifico della piattaforma di impostare gli stili e colori per ogni piattaforma.

Altre opzioni per controllare la modalità di aspetto dell'app iOS xamarin. Forms includono:

* Configurazione di visualizzare le opzioni in [ **Info. plist**](#info-plist)
* Impostazione degli stili del controllo tramite la [ `UIAppearance` API](#uiappearance)

Queste soluzioni alternative verranno illustrate di seguito.

<a name="info-plist"/>

## <a name="customizing-infoplist"></a>Personalizzazione Info. plist

Il **Info. plist** file consente di configurare alcuni aspetti di per il rendering di un'applicazione iOS, ad esempio come (e se) sulla barra di stato viene visualizzata.

Ad esempio, il [esempio Todo](https://developer.xamarin.com/samples/xamarin-forms/Todo/) utilizza il seguente codice per impostare il barra di spostamento colori e colore del testo in tutte le piattaforme:

```csharp
var nav = new NavigationPage (new TodoListPage ());
nav.BarBackgroundColor = Color.FromHex("91CA47");
nav.BarTextColor = Color.White;
```

Il risultato è illustrato nel frammento di schermata seguente. Si noti che le barre di stato sono nere (non può essere impostato all'interno di xamarin. Forms perché è una funzionalità specifiche della piattaforma).

![](theme-images/status-default-sml.png "i temi di iOS")

In teoria la barra di stato anche sarebbe bianca - un elemento è possibile eseguire direttamente nel progetto iOS. Aggiungere le seguenti voci di **Info. plist** per forzare la barra di stato di colore bianco:

![](theme-images/info-plist.png "iOS voci Info. plist")

o modificare i corrispondenti **Info. plist** file direttamente da includere:

```xml
<key>UIStatusBarStyle</key>
<string>UIStatusBarStyleLightContent</string>
<key>UIViewControllerBasedStatusBarAppearance</key>
<false/>
```

Quando si esegue l'app, la barra di spostamento è di colore verde e il testo è bianco (a causa di formattazione di xamarin. Forms) *e* il testo della barra di stato viene inoltre bianchi grazie alla configurazione di iOS specifiche:

![](theme-images/status-white-sml.png "i temi di iOS")

<a name="uiappearance"/>

## <a name="uiappearance-api"></a>UIAppearance API

Il [ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md) può essere utilizzato per impostare le proprietà visual molti controlli iOS *senza* la necessità di creare un [renderer personalizzato](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

Aggiunta di una singola riga di codice per il **appdelegate. cs** `FinishedLaunching` metodo puoi applicare uno stile di tutti i controlli di un tipo specificato usando i relativi `Appearance` proprietà. Il codice seguente contiene due esempi - stile globalmente la scheda a barre e passare controllo:

**Appdelegate. cs** nel progetto iOS

```csharp
public override bool FinishedLaunching (UIApplication app, NSDictionary options)
{
  // tab bar
    UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
  // switch
    UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
    // required Xamarin.Forms code
    Forms.Init ();
    LoadApplication (new App ());
    return base.FinishedLaunching (app, options);
}
```

### <a name="uitabbar"></a>UITabBar

Per impostazione predefinita, l'icona della barra di scheda selezionata in un [ `TabbedPage` ](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md) sarebbe blu:

![](theme-images/tabbar-default.png "Icona della barra della scheda in TabbedPage iOS predefinito")

Per modificare questo comportamento, impostare il `UITabBar.Appearance` proprietà:

```csharp
UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

Di conseguenza, la scheda selezionata essere di colore verde:

![](theme-images/tabbar-custom.png "Icona della barra della scheda in TabbedPage di iOS verde")

Usare questa API consente di personalizzare l'aspetto di xamarin. Forms `TabbedPage` in iOS con codice molto ridotta. Fare riferimento al [recipe personalizzare schede](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/ios/customize-tabs/) per ulteriori informazioni sull'utilizzo di un renderer personalizzato per impostare un tipo di carattere specifico per la scheda.

### <a name="uiswitch"></a>UISwitch

Il `Switch` controllo è un altro esempio che è possibile applicare facilmente stili:

```csharp
UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

Queste schermate due mostrano il valore predefinito `UISwitch` controllo a sinistra e la versione personalizzata (impostazione `Appearance`) a destra il [esempio Todo](https://developer.xamarin.com/samples/xamarin-forms/Todo/):

![](theme-images/switch-default.png "Colore UISwitch predefinito") ![ ] (theme-images/switch-custom.png "UISwitch colore personalizzato")

### <a name="other-controls"></a>Altri controlli

Molti controlli dell'interfaccia utente iOS possono avere i colori predefiniti e gli altri attributi impostati utilizzando il [ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md).



## <a name="related-links"></a>Collegamenti correlati

- [UIAppearance](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)
- [Personalizzare le schede](https://developer.xamarin.com/recipes/cross-platform/xamarin-forms/ios/customize-tabs/)
