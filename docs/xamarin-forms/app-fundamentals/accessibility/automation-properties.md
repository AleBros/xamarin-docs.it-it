---
title: Proprietà di automazione
description: Questo articolo illustra come usare la classe AutomationProperties in un'applicazione Xamarin.Forms, in modo che un'utilità per la lettura dello schermo possa annunciare gli elementi della pagina.
ms.prod: xamarin
ms.assetid: c0bb6893-fd26-47e7-88e5-3c333c9f786c
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 12/18/2018
ms.openlocfilehash: 12c6229c1922f0bd4a4d25ca796bcb46141a326c
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "77131133"
---
# <a name="automation-properties-in-xamarinforms"></a>Proprietà di automazione in Xamarin.Forms

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-accessibility)

_Xamarin.Forms consente di impostare i valori di accessibilità sugli elementi dell'interfaccia utente utilizzando le proprietà associate dalla classe AutomationProperties, che a sua volta impostano valori di accessibilità nativi. In questo articolo viene illustrato come utilizzare il AutomationProperties classe, in modo che un'utilità per la lettura dello schermo può parlare degli elementi nella pagina._

Xamarin.Forms consente di impostare le proprietà di automazione su elementi dell'interfaccia utente tramite le proprietà associate seguenti:

- `AutomationProperties.IsInAccessibleTree`: indica se l'elemento è disponibile per un'applicazione accessibile. Per altre informazioni, vedere [AutomationProperties.IsInAccessibleTree](#isinaccessibletree).
- `AutomationProperties.Name`: una breve descrizione dell'elemento che funge da identificatore pronunciabile per l'elemento. Per altre informazioni, vedere [AutomationProperties.Name](#name).
- `AutomationProperties.HelpText`: una descrizione più lunga dell'elemento assimilabile al testo della descrizione comando associato all'elemento. Per altre informazioni, vedere [AutomationProperties.HelpText](#helptext).
- `AutomationProperties.LabeledBy`: consente a un altro elemento di definire le informazioni di accessibilità per l'elemento corrente. Per altre informazioni, vedere [AutomationProperties.LabeledBy](#labeledby).

Queste proprietà associate impostano valori di accessibilità nativi in modo che l'utilità per la lettura dello schermo possa presentare l'elemento. Per altre informazioni sulle proprietà associate, vedere [Proprietà associate](~/xamarin-forms/xaml/attached-properties.md).

> [!IMPORTANT]
> L'uso delle proprietà associate `AutomationProperties` può rallentare l'esecuzione del test dell'interfaccia utente in Android. Le [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) `AutomationProperties.Name` proprietà `AutomationProperties.HelpText` , e imposteranno entrambe la `ContentDescription` proprietà nativa, `AutomationId` con i `AutomationProperties.Name` valori `AutomationProperties.HelpText` delle `AutomationProperties.Name` proprietà e `AutomationProperties.HelpText` che hanno la precedenza sul valore (se entrambi e sono impostati, i valori verranno concatenati). Ciò significa che i test che cercano `AutomationId` avranno esito negativo se anche `AutomationProperties.Name` o `AutomationProperties.HelpText` è impostato per l'elemento. In questo scenario, i test dell'interfaccia utente dovrebbero essere modificati per cercare il valore `AutomationProperties.Name` o `AutomationProperties.HelpText`, o una concatenazione dei due.

In ogni piattaforma è presente un'utilità per la lettura dello schermo diversa tramite la quale enunciare i valori di accessibilità:

- iOS ha VoiceOver. Per altre informazioni, vedere [Test Accessibility on Your Device with VoiceOver](https://developer.apple.com/library/content/technotes/TestingAccessibilityOfiOSApps/TestAccessibilityonYourDevicewithVoiceOver/TestAccessibilityonYourDevicewithVoiceOver.html) (Testare l'accessibilità sul dispositivo con VoiceOver) su developer.apple.com.
- Android ha TalkBack. Per altre informazioni, vedere [Test Your App's Accessibility](https://developer.android.com/training/accessibility/testing.html#talkback) (Testare l'accessibilità dell'app) su developer.android.com.
- Windows ha Assistente vocale. Per altre informazioni, vedere [Verify main app scenarios by using Narrator](/windows/uwp/accessibility/accessibility-testing#verify-main-app-scenarios-by-using-narrator) (Verificare gli scenari app principali usando Assistente vocale).

Tuttavia, il comportamento esatto di un'utilità per la lettura dello schermo dipende dal software e dalla relativa configurazione scelta dall'utente. La maggior parte delle utilità per la lettura dello schermo, ad esempio, leggono il testo associato a un controllo quando questo riceve lo stato attivo, consentendo agli utenti di orientarsi mentre si spostano tra i controlli della pagina. Alcune utilità per la lettura dello schermo leggono anche l'intera interfaccia utente dell'applicazione quando viene visualizzata una pagina, così che l'utente conosca tutto il contenuto informativo della pagina prima di iniziare a spostarsi al suo interno.

Inoltre, le utilità per la lettura dello schermo leggono valori di accessibilità diversi. Nell'applicazione di esempio:

- VoiceOver leggerà il valore `Placeholder` del controllo `Entry`, seguito dalle istruzioni per l'uso del controllo.
- TalkBack leggerà il valore `Placeholder` del controllo `Entry`, seguito dal valore `AutomationProperties.HelpText`, seguito dalle istruzioni per l'uso del controllo.
- Assistente vocale leggerà il valore `AutomationProperties.LabeledBy` del controllo `Entry`, seguito dalle istruzioni per l'uso del controllo.

Inoltre, Assistente vocale classifica in ordine di priorità `AutomationProperties.Name`, `AutomationProperties.LabeledBy` e quindi `AutomationProperties.HelpText`. In Android può accadere che TalkBack combini i valori `AutomationProperties.Name` e `AutomationProperties.HelpText`. Pertanto, per garantire un'esperienza ottimale, si consiglia di eseguire test completi sull'accessibilità su ogni piattaforma.

<a name="isinaccessibletree" />

## <a name="automationpropertiesisinaccessibletree"></a>AutomationProperties.IsInAccessibleTree

La proprietà associata `AutomationProperties.IsInAccessibleTree` è un valore `boolean` che determina se l'elemento è accessibile, e pertanto visibile, alle utilità per la lettura dello schermo. Per poter usare le altre proprietà associate di accessibilità, deve essere impostata su `true`. Per ottenere questo risultato, è possibile procedere come segue in XAML:

```xaml
<Entry AutomationProperties.IsInAccessibleTree="true" />
```

In alternativa, può essere impostata in C# come indicato di seguito:

```csharp
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
```

> [!NOTE]
> Si noti che il [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) metodo `AutomationProperties.IsInAccessibleTree` può essere utilizzato anche per impostare la proprietà associata –`entry.SetValue(AutomationProperties.IsInAccessibleTreeProperty, true);`

<a name="name" />

## <a name="automationpropertiesname"></a>AutomationProperties.Name

Il valore della proprietà associata `AutomationProperties.Name` deve essere una stringa di testo breve e descrittiva che l'utilità per la lettura dello schermo possa usare per annunciare un elemento. Questa proprietà deve essere impostata per gli elementi indispensabili per comprendere il contenuto o l'interazione con l'interfaccia utente. Per ottenere questo risultato, è possibile procedere come segue in XAML:

```xaml
<ActivityIndicator AutomationProperties.IsInAccessibleTree="true"
                   AutomationProperties.Name="Progress indicator" />
```

In alternativa, può essere impostata in C# come indicato di seguito:

```csharp
var activityIndicator = new ActivityIndicator();
AutomationProperties.SetIsInAccessibleTree(activityIndicator, true);
AutomationProperties.SetName(activityIndicator, "Progress indicator");
```

> [!NOTE]
> Si noti che il [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) metodo `AutomationProperties.Name` può essere utilizzato anche per impostare la proprietà associata –`activityIndicator.SetValue(AutomationProperties.NameProperty, "Progress indicator");`

<a name="helptext" />

## <a name="automationpropertieshelptext"></a>AutomationProperties.HelpText

La proprietà associata `AutomationProperties.HelpText` deve essere impostata su testo che descriva l'elemento dell'interfaccia utente e assimilabile al testo della descrizione comando associata all'elemento. Per ottenere questo risultato, è possibile procedere come segue in XAML:

```xaml
<Button Text="Toggle ActivityIndicator"
        AutomationProperties.IsInAccessibleTree="true"
        AutomationProperties.HelpText="Tap to toggle the activity indicator" />
```

In alternativa, può essere impostata in C# come indicato di seguito:

```csharp
var button = new Button { Text = "Toggle ActivityIndicator" };
AutomationProperties.SetIsInAccessibleTree(button, true);
AutomationProperties.SetHelpText(button, "Tap to toggle the activity indicator");
```

> [!NOTE]
> Si noti che il [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) metodo `AutomationProperties.HelpText` può essere utilizzato anche per impostare la proprietà associata –`button.SetValue(AutomationProperties.HelpTextProperty, "Tap to toggle the activity indicator");`

In alcune piattaforme, per [`Entry`](xref:Xamarin.Forms.Entry)i `HelpText` controlli di modifica, ad esempio un oggetto , la proprietà può talvolta essere omessa e sostituita con testo segnaposto. Ad esempio, "Immettere il proprio nome [`Entry.Placeholder`](xref:Xamarin.Forms.InputView.Placeholder) qui" è un buon candidato per la proprietà che inserisce il testo nel controllo prima dell'input effettivo dell'utente.

<a name="labeledby" />

## <a name="automationpropertieslabeledby"></a>AutomationProperties.LabeledBy

La proprietà associata `AutomationProperties.LabeledBy` consente a un altro elemento di definire le informazioni di accessibilità per l'elemento corrente. Ad esempio, [`Label`](xref:Xamarin.Forms.Label) un [`Entry`](xref:Xamarin.Forms.Entry) accanto a un può `Entry` essere utilizzato per descrivere ciò che rappresenta il. Per ottenere questo risultato, è possibile procedere come segue in XAML:

```xaml
<Label x:Name="label" Text="Enter your name: " />
<Entry AutomationProperties.IsInAccessibleTree="true"
       AutomationProperties.LabeledBy="{x:Reference label}" />
```

In alternativa, può essere impostata in C# come indicato di seguito:

```csharp
var nameLabel = new Label { Text = "Enter your name: " };
var entry = new Entry();
AutomationProperties.SetIsInAccessibleTree(entry, true);
AutomationProperties.SetLabeledBy(entry, nameLabel);
```

> [!NOTE]
> Si noti che il [`SetValue`](xref:Xamarin.Forms.BindableObject.SetValue(Xamarin.Forms.BindableProperty,System.Object)) metodo `AutomationProperties.IsInAccessibleTree` può essere utilizzato anche per impostare la proprietà associata –`entry.SetValue(AutomationProperties.LabeledByProperty, nameLabel);`

## <a name="accessibility-intricacies"></a>Aspetti complessi dell'accessibilità

Le sezioni seguenti descrivono aspetti complessi dell'impostazione dei valori di accessibilità su determinati controlli.

### <a name="navigationpage"></a>NavigationPage

In Android, per impostare il testo che le utilità per [`NavigationPage`](xref:Xamarin.Forms.NavigationPage)la lettura `AutomationProperties.Name` `AutomationProperties.HelpText` dello schermo [`Page`](xref:Xamarin.Forms.Page)leggeranno per la freccia indietro nella barra delle azioni in un , impostare le proprietà e su un oggetto . Tenere tuttavia presente che questo non ha nessun effetto sui pulsanti Indietro del sistema operativo.

### <a name="masterdetailpage"></a>MasterDetailPage

In iOS e la piattaforma UWP (Universal Windows Platform), per impostare [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)il testo `AutomationProperties.Name`che `AutomationProperties.HelpText` le utilità `MasterDetailPage`per la `IconImageSource` lettura `Master` dello schermo leggeranno per il pulsante di attivazione/disattivazione in un' , impostare le proprietà e sulla proprietà della pagina .

In Android, per impostare il testo che le [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage)utilità per la lettura dello schermo leggeranno per il pulsante di attivazione/disattivazione in un , aggiungere risorse stringa al progetto Android:

```xml
<resources>
    <string name="app_name">Xamarin Forms Control Gallery</string>
    <string name="btnMDPAutomationID_open">Open Side Menu message</string>
    <string name="btnMDPAutomationID_close">Close Side Menu message</string>
</resources>
```

Impostare quindi la proprietà `AutomationId` della proprietà `IconImageSource` della pagina `Master` sulla stringa appropriata:

```csharp
var master = new ContentPage { ... };
master.IconImageSource.AutomationId = "btnMDPAutomationID";
```

### <a name="toolbaritem"></a>ToolbarItem

In iOS, Android e UWP, le `Text` utilità [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem) per la `AutomationProperties.Name` lettura `AutomationProperties.HelpText` dello schermo leggeranno il valore della proprietà delle istanze, a condizione che o i valori non siano definiti.

In iOS e nella piattaforma UWP il valore della proprietà `AutomationProperties.Name` sostituisce il valore della proprietà `Text` che viene letto dall'utilità per la lettura dello schermo.

In Android i valori delle proprietà `AutomationProperties.Name` e/o `AutomationProperties.HelpText` sostituiranno completamente il valore della proprietà `Text` che è visibile e viene letto dall'utilità per la lettura dello schermo. Si noti che questa è una limitazione per le API inferiori alla 26.

## <a name="related-links"></a>Collegamenti correlati

- [Proprietà associate](~/xamarin-forms/xaml/attached-properties.md)
- [Accessibility (sample)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-accessibility) (Esempio di accessibilità)
