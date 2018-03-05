---
title: "Impostazione dei valori di accessibilità in elementi dell'interfaccia utente"
description: "Xamarin. Forms consente valori di accessibilità da impostare per gli elementi dell'interfaccia utente utilizzando le proprietà associate dalla classe AutomationProperties, che a sua volta impostare i valori di accessibilità nativo. In questo articolo viene illustrato come utilizzare la classe AutomationProperties, in modo che uno screen reader possono supportare sugli elementi nella pagina."
ms.topic: article
ms.prod: xamarin
ms.assetid: c0bb6893-fd26-47e7-88e5-3c333c9f786c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: 4aeeea7f946a121b12741d2da217daf531935849
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="setting-accessibility-values-on-user-interface-elements"></a>Impostazione dei valori di accessibilità in elementi dell'interfaccia utente

_Xamarin. Forms consente valori di accessibilità da impostare per gli elementi dell'interfaccia utente utilizzando le proprietà associate dalla classe AutomationProperties, che a sua volta impostare i valori di accessibilità nativo. In questo articolo viene illustrato come utilizzare la classe AutomationProperties, in modo che uno screen reader possono supportare sugli elementi nella pagina._

## <a name="overview"></a>Panoramica

Xamarin. Forms consente di accessibilità di valori da impostare per gli elementi dell'interfaccia utente tramite le proprietà associate seguenti:

- `AutomationProperties.IsInAccessibleTree` : indica se l'elemento è disponibile per un'applicazione accessibile. Per ulteriori informazioni, vedere [AutomationProperties.IsInAccessibleTree](#isinaccessibletree).
- `AutomationProperties.Name` : una breve descrizione dell'elemento che funge da identificatore speakable per l'elemento. Per ulteriori informazioni, vedere [AutomationProperties.Name](#name).
- `AutomationProperties.HelpText` -una descrizione più lunga dell'elemento, che può essere considerato come testo della descrizione comando associato all'elemento. Per ulteriori informazioni, vedere [AutomationProperties.HelpText](#helptext).
- `AutomationProperties.LabeledBy` : consente di definire le informazioni sull'accessibilità per l'elemento corrente di un altro elemento. Per ulteriori informazioni, vedere [AutomationProperties.LabeledBy](#labeledby).

Questi collegato dei valori di accessibilità native set di proprietà in modo che l'elemento può parlare di lettura dello schermo. Per ulteriori informazioni sulle proprietà associate, vedere [collegato proprietà](~/xamarin-forms/xaml/attached-properties.md).

> [!IMPORTANT]
> Utilizzo di `AutomationProperties` le proprietà associate potrebbero compromettere l'esecuzione di Test dell'interfaccia utente in Android. Il [ `AutomationId` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Element.AutomationId/), `AutomationProperties.Name` e `AutomationProperties.HelpText` verranno entrambe impostate nativo `ContentDescription` proprietà, con la `AutomationProperties.Name` e `AutomationProperties.HelpText` i valori delle proprietà che avrà la precedenza sul `AutomationId`valore (se entrambi `AutomationProperties.Name` e `AutomationProperties.HelpText` sono impostati, i valori verranno concatenati). Ciò significa che qualsiasi test cercando `AutomationId` avrà esito negativo se `AutomationProperties.Name` o `AutomationProperties.HelpText` vengono inoltre impostati nell'elemento. In questo scenario, i test dell'interfaccia utente devono essere modificati per cercare il valore di `AutomationProperties.Name` o `AutomationProperties.HelpText`, o una concatenazione di entrambi.

Ogni piattaforma presenta un lettore dello schermo diversa per commentare i valori di accessibilità:

- iOS è Voice over. Per ulteriori informazioni, vedere [accesso facilitato di Test su del dispositivo con Voice over](https://developer.apple.com/library/content/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html) su developer.apple.com.
- Android dispone del TalkBack. Per ulteriori informazioni, vedere [accessibilità test dell'applicazione](https://developer.android.com/training/accessibility/testing.html#talkback) su developer.android.com.
- Windows ha Assistente vocale. Per ulteriori informazioni, vedere [verificare gli scenari di applicazione principale tramite Assistente vocale](/windows/uwp/accessibility/accessibility-testing#verify-main-app-scenarios-by-using-narrator/).

Tuttavia, il comportamento esatto di lettura dello schermo varia a seconda del software e la configurazione dell'utente di esso. Ad esempio, la maggior parte dei lettori di schermata leggono il testo associato a un controllo quando riceve lo stato attivo, consentendo agli utenti di orientarsi durante lo spostamento tra i controlli della pagina. Alcuni assistenti leggono anche l'interfaccia utente di tutta l'applicazione quando viene visualizzata una pagina, che consente all'utente di ricevere tutti i contenuti informativi disponibili della pagina prima di tentare di spostarsi al suo interno.

Inoltre, gli screen reader leggere i valori di accessibilità diversa. Nell'applicazione di esempio:

- Voice over leggerà il `Placeholder` valore il `Entry`, seguito da istruzioni per l'utilizzo del controllo.
- TalkBack leggerà il `Placeholder` valore il `Entry`, seguito dal `AutomationProperties.HelpText` valore, seguita da istruzioni per l'utilizzo del controllo.
- Assistente vocale leggerà il `AutomationProperties.LabeledBy` valore il `Entry`, seguito da istruzioni sull'utilizzo del controllo.

Inoltre, si definirà Assistente vocale `AutomationProperties.Name`, `AutomationProperties.LabeledBy`e quindi `AutomationProperties.HelpText`. In Android, è possibile combinare TalkBack il `AutomationProperties.Name` e `AutomationProperties.HelpText` valori. Pertanto, è consigliabile che test approfonditi accessibilità viene eseguito su ogni piattaforma per garantire un'esperienza ottimale.

<a name="isinaccessibletree" />

## <a name="automationpropertiesisinaccessibletree"></a>AutomationProperties.IsInAccessibleTree

Il `AutomationProperties.IsInAccessibleTree` proprietà associata è un `boolean` che determina se l'elemento è accessibile e pertanto visibile per gli screen reader. Deve essere impostato su `true` per utilizzare l'accessibilità di altre proprietà associate. Questo può essere eseguito in XAML come segue:

```xaml
<Entry AutomationProperties.IsInAccessibleTree="true" />
```

In alternativa, può essere in c# come indicato di seguito:

```csharp
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
```

> [!NOTE]
> Si noti che il [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/) metodo può essere utilizzato anche per impostare il `AutomationProperties.IsInAccessibleTree` associata: `entry.SetValue(AutomationProperties.IsInAccessibleTreeProperty, true);`

<a name="name" />

## <a name="automationpropertiesname"></a>AutomationProperties.Name

Il `AutomationProperties.Name` valore della proprietà associata deve essere una stringa di testo breve e descrittivo che utilizza uno screen reader per annunciare un elemento. Questa proprietà deve essere impostata per gli elementi che hanno un significato che è importante per capire il contenuto o l'interazione con l'interfaccia utente. Questo può essere eseguito in XAML come segue:

```xaml
<ActivityIndicator AutomationProperties.IsInAccessibleTree="true"
                   AutomationProperties.Name="Progress indicator" />
```

In alternativa, può essere in c# come indicato di seguito:

```csharp
var activityIndicator = new ActivityIndicator();
AutomationProperties.SetIsInAccessibleTree(activityIndicator, true);
AutomationProperties.SetName(activityIndicator, "Progress indicator");
```

> [!NOTE]
> Si noti che il [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/) metodo può essere utilizzato anche per impostare il `AutomationProperties.Name` associata: `activityIndicator.SetValue(AutomationProperties.NameProperty, "Progress indicator");`

<a name="helptext" />

## <a name="automationpropertieshelptext"></a>AutomationProperties.HelpText

Il `AutomationProperties.HelpText` collegato deve essere impostata su testo che descrive l'elemento dell'interfaccia utente e può essere considerato come testo della descrizione comando associato all'elemento. Questo può essere eseguito in XAML come segue:

```xaml
<Button Text="Toggle ActivityIndicator"
        AutomationProperties.IsInAccessibleTree="true"
        AutomationProperties.HelpText="Tap to toggle the activity indicator" />
```

In alternativa, può essere in c# come indicato di seguito:

```csharp
var button = new Button { Text = "Toggle ActivityIndicator" };
AutomationProperties.SetIsInAccessibleTree(button, true);
AutomationProperties.SetHelpText(button, "Tap to toggle the activity indicator");
```

> [!NOTE]
> Si noti che il [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/) metodo può essere utilizzato anche per impostare il `AutomationProperties.HelpText` associata: `button.SetValue(AutomationProperties.HelpTextProperty, "Tap to toggle the activity indicator");`

In alcune piattaforme, per modificare i controlli, ad esempio un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/), `HelpText` proprietà talvolta può essere omesso e sostituita con testo segnaposto. Ad esempio, "Immettere qui il nome" è un buon candidato per la [ `Entry.Placeholder` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Entry.Placeholder/) proprietà che inserisce il testo nel controllo prima l'input dell'utente effettivo.

<a name="labeledby" />

## <a name="automationpropertieslabeledby"></a>AutomationProperties.LabeledBy

Il `AutomationProperties.LabeledBy` proprietà associata consente di definire le informazioni sull'accessibilità per l'elemento corrente di un altro elemento. Ad esempio, un [ `Label` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Label/) accanto a un [ `Entry` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Entry/) può essere utilizzato per descrivere le operazioni di `Entry` rappresenta. Questo può essere eseguito in XAML come segue:

```xaml
<Label x:Name="label" Text="Enter your name: " />
<Entry AutomationProperties.IsInAccessibleTree="true"
       AutomationProperties.LabeledBy="{x:Reference label}" />
```

In alternativa, può essere in c# come indicato di seguito:

```csharp
var nameLabel = new Label { Text = "Enter your name: " };
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
AutomationProperties.SetLabeledBy(entry, nameLabel);
```

> [!NOTE]
> Si noti che il [ `SetValue` ](https://developer.xamarin.com/api/member/Xamarin.Forms.BindableObject.SetValue/p/Xamarin.Forms.BindableProperty/System.Object/) metodo può essere utilizzato anche per impostare il `AutomationProperties.IsInAccessibleTree` associata: `entry.SetValue(AutomationProperties.LabeledByProperty, nameLabel);`

## <a name="summary"></a>Riepilogo

Questo articolo ha descritto come impostare i valori di accessibilità sull'utente che ha gli elementi dell'interfaccia in un'applicazione di xamarin. Forms usando le proprietà associate dalla `AutomationProperties` classe. Questi collegato dei valori di accessibilità native set di proprietà in modo che uno screen reader possono supportare sugli elementi nella pagina.


## <a name="related-links"></a>Collegamenti correlati

- [Proprietà associate](~/xamarin-forms/xaml/attached-properties.md)
- [Accessibilità (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Accessibility/)
