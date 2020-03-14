---
title: Aggiunta della formattazione specifica di iOS
description: Questo articolo illustra come impostare un aspetto specifico di iOS senza usare un renderer personalizzato Novell. Forms.
ms.prod: xamarin
ms.assetid: CE50E207-D092-4D88-8439-1B51F178E7ED
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/29/2016
ms.openlocfilehash: 24d86c54ea4b346e1c165b28c6b62f5a98390d64
ms.sourcegitcommit: eca3b01098dba004d367292c8b0d74b58c4e1206
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79305311"
---
# <a name="adding-ios-specific-formatting"></a>Aggiunta della formattazione specifica di iOS

Un modo per impostare la formattazione specifica di iOS consiste nel creare un [renderer personalizzato](~/xamarin-forms/app-fundamentals/custom-renderer/index.md) per un controllo e impostare stili e colori specifici della piattaforma per ogni piattaforma.

Altre opzioni per controllare il modo in cui l'aspetto dell'app per iOS Novell. Forms includono:

- Configurazione delle opzioni di visualizzazione in [ **info. plist**](#info-plist)
- Impostazione degli stili dei controlli tramite l' [API`UIAppearance`](#uiappearance)

Queste alternative sono illustrate di seguito.

<a name="info-plist"/>

## <a name="customizing-infoplist"></a>Personalizzazione di info. plist

Il file **info. plist** consente di configurare alcuni aspetti del renderer di un'applicazione iOS, ad esempio come (e se) viene visualizzata la barra di stato.

Ad esempio, l' [esempio todo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo) usa il codice seguente per impostare il colore della barra di navigazione e il colore del testo su tutte le piattaforme:

```csharp
var nav = new NavigationPage (new TodoListPage ());
nav.BarBackgroundColor = Color.FromHex("91CA47");
nav.BarTextColor = Color.White;
```

Il risultato viene mostrato nel frammento di schermata riportato di seguito. Si noti che gli elementi della barra di stato sono neri (non può essere impostato all'interno di Novell. Forms perché si tratta di una funzionalità specifica della piattaforma).

![](theme-images/status-default-sml.png "iOS Theming")

Idealmente, la barra di stato sarebbe anche bianca, cosa che è possibile eseguire direttamente nel progetto iOS. Aggiungere le voci seguenti al file **info. plist** per forzare il bianco della barra di stato:

![](theme-images/info-plist.png "iOS Info.plist Entries")

in alternativa, modificare direttamente il file **info. plist** corrispondente in modo da includere:

```xml
<key>UIStatusBarStyle</key>
<string>UIStatusBarStyleLightContent</string>
<key>UIViewControllerBasedStatusBarAppearance</key>
<false/>
```

A questo punto, quando l'app viene eseguita, la barra di spostamento è verde e il testo è bianco (a causa della formattazione Novell. Forms) *e* anche il testo della barra di stato è bianco grazie alla configurazione specifica di iOS:

![](theme-images/status-white-sml.png "iOS Theming")

<a name="uiappearance"/>

## <a name="uiappearance-api"></a>API UIAppearance

L' [API`UIAppearance`](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md) può essere usata per impostare le proprietà visive in molti controlli iOS *senza* dover creare un [renderer personalizzato](~/xamarin-forms/app-fundamentals/custom-renderer/index.md).

L'aggiunta di una singola riga di codice al metodo `FinishedLaunching` **AppDelegate.cs** può applicare uno stile a tutti i controlli di un determinato tipo utilizzando la relativa proprietà `Appearance`. Il codice seguente contiene due esempi: lo stile globale della barra delle schede e il controllo switch:

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

Per impostazione predefinita, l'icona della barra scheda selezionata in una [`TabbedPage`](~/xamarin-forms/app-fundamentals/navigation/tabbed-page.md)
sarebbe blu:

![](theme-images/tabbar-default.png "Default iOS Tab Bar Icon in TabbedPage")

Per modificare questo comportamento, impostare la proprietà `UITabBar.Appearance`:

```csharp
UITabBar.Appearance.SelectedImageTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

In questo modo la scheda selezionata sarà verde:

![](theme-images/tabbar-custom.png "Green iOS Tab Bar Icon in TabbedPage")

L'uso di questa API consente di personalizzare l'aspetto del `TabbedPage` Novell. Forms in iOS con pochissimo codice. Per altre informazioni sull'uso di un renderer personalizzato per impostare un tipo di carattere specifico per la scheda, vedere la [ricetta personalizzare le schede](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs) .

### <a name="uiswitch"></a>UISwitch

Il controllo `Switch` è un altro esempio che può essere facilmente applicato allo stile:

```csharp
UISwitch.Appearance.OnTintColor = UIColor.FromRGB(0x91, 0xCA, 0x47); // green
```

Queste due acquisizioni di schermata mostrano il controllo `UISwitch` predefinito a sinistra e la versione personalizzata (impostazione `Appearance`) a destra nell' [esempio todo](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/todo):

![](theme-images/switch-default.png "Colore predefinito UISwitch") ![](theme-images/switch-custom.png "Colore UISwitch personalizzato")

### <a name="other-controls"></a>Altri controlli

Molti controlli dell'interfaccia utente iOS possono avere colori predefiniti e altri attributi impostati usando l' [API`UIAppearance`](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md).

## <a name="related-links"></a>Collegamenti correlati

- [UIAppearance](~/ios/user-interface/ios-ui/introduction-to-the-appearance-api.md)
- [Personalizzare le schede](https://github.com/xamarin/recipes/tree/master/Recipes/xamarin-forms/iOS/customize-tabs)
