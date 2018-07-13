---
title: Impostazione dei valori di accessibilità in elementi dell'interfaccia utente
description: Questo articolo illustra come usare la classe AutomationProperties, consentirne la lettura dello schermo può presentare sugli elementi nella pagina.
ms.prod: xamarin
ms.assetid: c0bb6893-fd26-47e7-88e5-3c333c9f786c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2017
ms.openlocfilehash: 576fe34b0536c83825aa39bdd7111143d9474225
ms.sourcegitcommit: 6e955f6851794d58334d41f7a550d93a47e834d2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/12/2018
ms.locfileid: "38998914"
---
# <a name="setting-accessibility-values-on-user-interface-elements"></a>Impostazione dei valori di accessibilità in elementi dell'interfaccia utente

_Xamarin. Forms consente i valori di accessibilità deve essere impostato su elementi dell'interfaccia utente usando le proprietà associate dalla classe AutomationProperties, che a sua volta impostare i valori di accessibilità nativo. Questo articolo illustra come usare la classe AutomationProperties, consentirne la lettura dello schermo può presentare sugli elementi nella pagina._

## <a name="overview"></a>Panoramica

Xamarin. Forms consente valori di accessibilità deve essere impostato su elementi dell'interfaccia utente tramite le proprietà associate seguenti:

- `AutomationProperties.IsInAccessibleTree` : indica se l'elemento è disponibile per un'applicazione accessibile. Per altre informazioni, vedere [AutomationProperties.IsInAccessibleTree](#isinaccessibletree).
- `AutomationProperties.Name` : una breve descrizione dell'elemento che funge da identificatore speakable per l'elemento. Per altre informazioni, vedere [AutomationProperties.Name](#name).
- `AutomationProperties.HelpText` -una descrizione più lunga dell'elemento, che possa essere considerato come testo della descrizione comando associato all'elemento. Per altre informazioni, vedere [AutomationProperties.HelpText](#helptext).
- `AutomationProperties.LabeledBy` – consente a un altro elemento di definire le informazioni di accessibilità per l'elemento corrente. Per altre informazioni, vedere [AutomationProperties.LabeledBy](#labeledby).

Questi collegato proprietà impostare i valori di accessibilità native in modo che sia possibile parlare di lettura dello schermo sull'elemento. Per altre informazioni sulle proprietà associate, vedere [collegati proprietà](~/xamarin-forms/xaml/attached-properties.md).

> [!IMPORTANT]
> Uso di `AutomationProperties` le proprietà associate possono rallentare l'esecuzione di Test dell'interfaccia utente in Android. Il [ `AutomationId` ](xref:Xamarin.Forms.Element.AutomationId), `AutomationProperties.Name` e `AutomationProperties.HelpText` proprietà verranno impostate entrambe nativo `ContentDescription` proprietà, con la `AutomationProperties.Name` e `AutomationProperties.HelpText` i valori delle proprietà hanno la precedenza sul `AutomationId`valore (se entrambi `AutomationProperties.Name` e `AutomationProperties.HelpText` vengono impostate, sarà possibile concatenare i valori). Ciò significa che i test cercando `AutomationId` avrà esito negativo se `AutomationProperties.Name` o `AutomationProperties.HelpText` impostati anche nell'elemento. In questo scenario, i test dell'interfaccia utente devono essere modificati per cercare il valore di `AutomationProperties.Name` o `AutomationProperties.HelpText`, o una concatenazione di entrambi.

Ogni piattaforma ha di lettura dello schermo diversa per raccontare una storia i valori di accesso facilitato:

- iOS ha Voice over. Per altre informazioni, vedere [accessibilità Test nel dispositivo dell'utente di VoiceOver](https://developer.apple.com/library/content/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html) su developer.apple.com.
- Android ha TalkBack. Per altre informazioni, vedere [accessibilità test dell'applicazione](https://developer.android.com/training/accessibility/testing.html#talkback) su developer.android.com.
- Windows include l'Assistente vocale. Per altre informazioni, vedere [verificare gli scenari di app principale usando Assistente vocale](/windows/uwp/accessibility/accessibility-testing#verify-main-app-scenarios-by-using-narrator/).

Tuttavia, il comportamento esatto della lettura dello schermo dipende dal software e nella relativa configurazione dell'utente. La maggior parte dei lettori dello schermo, ad esempio, leggono il testo associato a un controllo quando riceve lo stato attivo, consentendo agli utenti di orientarsi durante lo spostamento tra i controlli nella pagina. Alcuni lettori dello schermo leggono anche l'interfaccia utente dell'intera applicazione quando viene visualizzata una pagina, che consente all'utente di ricevere tutte il contenuto della pagina disponibili informativo prima di tentare di spostarsi al suo interno.

Inoltre, gli screen reader leggere i valori di accessibilità diversa. Nell'applicazione di esempio:

- VoiceOver leggerà il `Placeholder` pari al `Entry`, seguita da istruzioni per l'uso del controllo.
- TalkBack leggerà il `Placeholder` pari al `Entry`, seguito dal `AutomationProperties.HelpText` valore, seguita dalle istruzioni per l'uso del controllo.
- Assistente vocale leggerà il `AutomationProperties.LabeledBy` pari al `Entry`, seguita dalle istruzioni sull'utilizzo del controllo.

Inoltre, avrà la priorità dell'Assistente vocale `AutomationProperties.Name`, `AutomationProperties.LabeledBy`e quindi `AutomationProperties.HelpText`. In Android, è possibile combinare TalkBack il `AutomationProperties.Name` e `AutomationProperties.HelpText` valori. Pertanto, è consigliabile che test accessibilità completa viene eseguito su ogni piattaforma per garantire un'esperienza ottimale.

<a name="isinaccessibletree" />

## <a name="automationpropertiesisinaccessibletree"></a>AutomationProperties.IsInAccessibleTree

Il `AutomationProperties.IsInAccessibleTree` proprietà associata è un `boolean` che determina se l'elemento è accessibile e pertanto sono visibili, ai lettori dello schermo. Deve essere impostata su `true` l'accessibilità di usare le proprietà associate. Ciò può essere eseguito in XAML come indicato di seguito:

```xaml
<Entry AutomationProperties.IsInAccessibleTree="true" />
```

In alternativa, sarà possibile impostarla c# come indicato di seguito:

```csharp
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
```

> [!NOTE]
> Si noti che il [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) metodo può essere utilizzato anche per impostare il `AutomationProperties.IsInAccessibleTree` – proprietà associata `entry.SetValue(AutomationProperties.IsInAccessibleTreeProperty, true);`

<a name="name" />

## <a name="automationpropertiesname"></a>AutomationProperties.Name

Il `AutomationProperties.Name` valore della proprietà associata deve essere una stringa di testo breve e descrittivo che utilizza una lettura dello schermo di annunciare un elemento. Questa proprietà deve essere impostata per gli elementi che hanno un significato che è importante per comprendere il contenuto o l'interazione con l'interfaccia utente. Ciò può essere eseguito in XAML come indicato di seguito:

```xaml
<ActivityIndicator AutomationProperties.IsInAccessibleTree="true"
                   AutomationProperties.Name="Progress indicator" />
```

In alternativa, sarà possibile impostarla c# come indicato di seguito:

```csharp
var activityIndicator = new ActivityIndicator();
AutomationProperties.SetIsInAccessibleTree(activityIndicator, true);
AutomationProperties.SetName(activityIndicator, "Progress indicator");
```

> [!NOTE]
> Si noti che il [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) metodo può essere utilizzato anche per impostare il `AutomationProperties.Name` – proprietà associata `activityIndicator.SetValue(AutomationProperties.NameProperty, "Progress indicator");`

<a name="helptext" />

## <a name="automationpropertieshelptext"></a>AutomationProperties.HelpText

Il `AutomationProperties.HelpText` collegato deve essere impostata su testo che descrive l'elemento dell'interfaccia utente e può essere considerato testo della descrizione comando associato all'elemento. Ciò può essere eseguito in XAML come indicato di seguito:

```xaml
<Button Text="Toggle ActivityIndicator"
        AutomationProperties.IsInAccessibleTree="true"
        AutomationProperties.HelpText="Tap to toggle the activity indicator" />
```

In alternativa, sarà possibile impostarla c# come indicato di seguito:

```csharp
var button = new Button { Text = "Toggle ActivityIndicator" };
AutomationProperties.SetIsInAccessibleTree(button, true);
AutomationProperties.SetHelpText(button, "Tap to toggle the activity indicator");
```

> [!NOTE]
> Si noti che il [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) metodo può essere utilizzato anche per impostare il `AutomationProperties.HelpText` – proprietà associata `button.SetValue(AutomationProperties.HelpTextProperty, "Tap to toggle the activity indicator");`

In alcune piattaforme, per la modifica controlli, ad esempio un [ `Entry` ](xref:Xamarin.Forms.Entry), il `HelpText` proprietà in alcuni casi può essere omesso e sostituita con testo segnaposto. Ad esempio, "Immettere qui il nome" è un buon candidato per la [ `Entry.Placeholder` ](xref:Xamarin.Forms.Entry.Placeholder) proprietà che inserisce il testo nel controllo prima l'input dell'utente effettivo.

<a name="labeledby" />

## <a name="automationpropertieslabeledby"></a>AutomationProperties.LabeledBy

Il `AutomationProperties.LabeledBy` proprietà associata consente a un altro elemento di definire le informazioni di accessibilità per l'elemento corrente. Ad esempio, un [ `Label` ](xref:Xamarin.Forms.Label) accanto a un [ `Entry` ](xref:Xamarin.Forms.Entry) può essere utilizzato per descrivere cosa la `Entry` rappresenta. Ciò può essere eseguito in XAML come indicato di seguito:

```xaml
<Label x:Name="label" Text="Enter your name: " />
<Entry AutomationProperties.IsInAccessibleTree="true"
       AutomationProperties.LabeledBy="{x:Reference label}" />
```

In alternativa, sarà possibile impostarla c# come indicato di seguito:

```csharp
var nameLabel = new Label { Text = "Enter your name: " };
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
AutomationProperties.SetLabeledBy(entry, nameLabel);
```

> [!NOTE]
> Si noti che il [ `SetValue` ](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) metodo può essere utilizzato anche per impostare il `AutomationProperties.IsInAccessibleTree` – proprietà associata `entry.SetValue(AutomationProperties.LabeledByProperty, nameLabel);`

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come impostare i valori di accessibilità utente elementi dell'interfaccia in un'applicazione xamarin. Forms usando le proprietà associate dal `AutomationProperties` classe. I valori di proprietà set accessibilità nativo questi collegata in modo che di lettura dello schermo può presentare sugli elementi nella pagina.


## <a name="related-links"></a>Collegamenti correlati

- [Proprietà associate](~/xamarin-forms/xaml/attached-properties.md)
- [Accessibilità (esempio)](https://developer.xamarin.com/samples/xamarin-forms/UserInterface/Accessibility/)
