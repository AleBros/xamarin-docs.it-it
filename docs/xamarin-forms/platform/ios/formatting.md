---
title: Aggiunta di formattazione specifici iOS
description: Questo articolo illustra come impostare l'aspetto specifico di iOS senza usare un renderer personalizzato di xamarin. Forms.
ms.prod: xamarin
ms.assetid: CE50E207-D092-4D88-8439-1B51F178E7ED
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/29/2016
ms.openlocfilehash: 3b8a440617dedfbe23f869e865b3cedae21d6c5b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946389"
---
# <a name="adding-ios-specific-formatting"></a>Aggiunta di formattazione specifici iOS

Un modo per impostare iOS specifici di formattazione consiste nel creare un [renderer personalizzato](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) per un controllo e impostare gli stili specifici della piattaforma e i colori per ogni piattaforma.

Altre opzioni per controllare la modalità di aspetto dell'app iOS xamarin. Forms includono:

* Configurazione di visualizzare le opzioni nella [ **Info. plist**](#info-plist)
* Impostazione degli stili del controllo tramite il [ `UIAppearance` API](#uiappearance)

Queste soluzioni alternative verranno illustrate di seguito.

<a name="info-plist"/>

## <a name="customizing-infoplist"></a>Personalizzazione di Info. plist

Il **Info. plist** file consente di configurare alcuni aspetti di per il rendering di un'applicazione iOS, ad esempio come (e se) sulla barra di stato viene visualizzata.

Ad esempio, il [esempio Todo](https://developer.xamarin.com/samples/xamarin-forms/Todo/) utilizza il codice seguente per impostare il barra di spostamento colore e il colore del testo in tutte le piattaforme:

```csharp
var nav = new NavigationPage (new TodoListPage ());
nav.BarBackgroundColor = Color.FromHex("91CA47");
nav.BarTextColor = Color.White;
```

Il risultato è illustrato nel frammento di schermata seguente. Si noti che le barre di stato sono nere (non può essere impostato all'interno di xamarin. Forms perché è una funzionalità specifiche della piattaforma).

![](theme-images/status-default-sml.png "iOS Theming")

In teoria la barra di stato sarà ugualmente bianca - qualcosa permette di eseguire direttamente nel progetto iOS. Aggiungere le voci seguenti per il **Info. plist** per forzare la barra di stato di colore bianco:

![](theme-images/info-plist.png "iOS Info.plist Entries")

o si modifica il corrispondente **Info. plist** file direttamente da includere:

```xml
<key>UIStatusBarStyle</key>
<string>UIStatusBarStyleLightContent</string>
<key>UIViewControllerBasedStatusBarAppearance</key>
<false/>
```

A questo punto quando si esegue l'app, la barra di spostamento è di colore verde e il testo è il bianco (a causa della formattazione di xamarin. Forms) *e* il testo della barra di stato viene inoltre bianchi grazie alla configurazione di iOS specifici:

![](theme-images/status-white-sml.png "iOS Theming")

<a name="uiappearance"/>

## <a name="uiappearance-api"></a>UIAppearance API

Il [ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md) può essere utilizzato per impostare proprietà visive per numerosi controlli iOS *senza* doverne creare di un [renderer personalizzati](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

Aggiunta di una singola riga di codice per il **AppDelegate.cs** `FinishedLaunching` metodo puoi applicare uno stile di tutti i controlli di un tipo specificato utilizzando i `Appearance` proprietà. Il codice seguente contiene due esempi - applicazione di stili a livello globale la scheda a barre e passare controllo:

**AppDelegate.cs** nel progetto iOS

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

Per impostazione predefinita, l'icona della barra selezionata della scheda in un [`TabbedPage`](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
sarebbe blu:

![](theme-images/tabbar-default.png "Icona della barra della scheda in TabbedPage iOS predefinita")

Per modificare questo comportamento, impostare il `UITabBar.Appearance` proprietà:

```csharp
UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

In questo modo la scheda selezionata sia verde:

![](theme-images/tabbar-custom.png "IOS verde sull'icona sulla barra della scheda in TabbedPage")

Usare questa API consente di personalizzare l'aspetto di xamarin. Forms `TabbedPage` in iOS con un minimo di codice. Vedere la [recipe di personalizzare le schede](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs) per altri dettagli sull'uso di un renderer personalizzato per impostare un tipo di carattere specifico per la scheda.

### <a name="uiswitch"></a>UISwitch

Il `Switch` controllo è un altro esempio che può applicare facilmente stili:

```csharp
UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

Queste due schermate mostrano il valore predefinito `UISwitch` controllo sulla sinistra e la versione personalizzata (impostazione `Appearance`) a destra di [esempio Todo](https://developer.xamarin.com/samples/xamarin-forms/Todo/):

![](theme-images/switch-default.png "Colore UISwitch predefinito") ![](theme-images/switch-custom.png "UISwitch colore personalizzato")

### <a name="other-controls"></a>Altri controlli

Molti controlli dell'interfaccia utente iOS possono avere i colori predefiniti e altri attributi impostati utilizzando il [ `UIAppearance` API](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md).



## <a name="related-links"></a>Collegamenti correlati

- [UIAppearance](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)
- [Personalizza schede](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs)
