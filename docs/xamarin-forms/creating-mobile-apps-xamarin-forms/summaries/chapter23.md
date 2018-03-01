---
title: Riepilogo del capitolo 23. I trigger e i comportamenti
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: 72158cac683e46a37d2cbd537cdfd72bea78a336
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="summary-of-chapter-23-triggers-and-behaviors"></a>Riepilogo del capitolo 23. I trigger e i comportamenti

I trigger e i comportamenti sono simili, in quanto sono entrambi devono essere utilizzati nei file XAML per semplificare l'interazione di elemento oltre l'utilizzo delle associazioni dati e per estendere le funzionalità di elementi XAML. Sia i trigger e i comportamenti sono quasi sempre usati con gli oggetti dell'interfaccia utente di visual.

Per supportare i trigger e i comportamenti, entrambi `VisualElement` e `Style` supporta due proprietà di raccolta:

- [`VisualElement.Triggers`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Triggers/) e [ `Style.Triggers` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.Triggers/) di tipo `IList<TriggerBase>`
- [`VisualElement.Behaviors`](https://developer.xamarin.com/api/property/Xamarin.Forms.VisualElement.Behaviors/) e [ `Style.Behaviors` ](https://developer.xamarin.com/api/property/Xamarin.Forms.Style.Behaviors/) di tipo `IList<Behavior>`

## <a name="triggers"></a>Trigger

Un trigger è una condizione (modifica di una proprietà o la generazione di un evento) che restituisce una risposta (un'altra modifica della proprietà o esegua codice). Il `Triggers` proprietà di `VisualElement` e `Style` è di tipo `IList<TriggersBase>`. [`TriggerBase`](https://developer.xamarin.com/api/type/Xamarin.Forms.TriggerBase/) è una classe astratta dalla quale derivano le classi sealed quattro:

- [`Trigger`](https://developer.xamarin.com/api/type/Xamarin.Forms.Trigger/) per le risposte in base alle modifiche di proprietà
- [`EventTrigger`](https://developer.xamarin.com/api/type/Xamarin.Forms.EventTrigger/) per le risposte in base alle esecuzioni di evento
- [`DataTrigger`](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTrigger/) per le risposte in base alle associazioni dati
- [`MultiTrigger`](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiTrigger/) per le risposte in base a più trigger

Il trigger è sempre impostato sull'elemento con la proprietà viene modificata da parte del trigger.

### <a name="the-simplest-trigger"></a>Il trigger più semplice

Il [ `Trigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Trigger/) verifica la presenza di una modifica del valore della proprietà di classe e risponde impostando un'altra proprietà dello stesso elemento.

`Trigger` definisce tre proprietà:

- [`Property`](https://developer.xamarin.com/api/property/Xamarin.Forms.Trigger.Property/) di tipo `BindableProperty`
- [`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.Trigger.Value/) di tipo `Object`
- [`Setters`](https://developer.xamarin.com/api/property/Xamarin.Forms.Trigger.Setters/) di tipo `IList<SetterBase>`, la proprietà di contenuto `Trigger`

Inoltre, `Trigger` richiede che la seguente proprietà ereditata da `TriggerBase` impostare:

- [`TargetType`](https://developer.xamarin.com/api/property/Xamarin.Forms.TriggerBase.TargetType/) per indicare il tipo di elemento per cui il `Trigger` è collegato

Il `Property` e `Value` costituiscono la condizione e `Setters` insieme è riportata la risposta. Quando il functoid `Property` ha il valore indicato da `Value`, il `Setter` gli oggetti il `Setters` vengono applicati insieme. Quando il `Property` ha un valore diverso, vengono rimossi i metodi di impostazione. `Setter` definisce due proprietà che corrispondono alle prime due proprietà di `Trigger`:

- [`Property`](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Property/) di tipo `BindableProperty`
- [`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.Setter.Value/) di tipo `Object`

Il [ **EntryPop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPop) esempio viene illustrato come un `Trigger` applicato a un `Entry` può aumentare le dimensioni del `Entry` tramite il `Scale` proprietà quando il `IsFocused`proprietà del `Entry` è `true`.

Anche se non è comune, il `Trigger` può essere impostata nel codice, come il [ **EntryPopCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPopCode) viene illustrato l'esempio.

Il [ **StyledTriggers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/StyledTriggers) esempio viene illustrato come la `Trigger` può essere impostata in un `Style` da applicare a più `Entry` elementi.

### <a name="trigger-actions-and-animations"></a>Le azioni dei trigger e le animazioni

È anche possibile eseguire il codice leggermente in base a un trigger. Questo codice può essere un'animazione destinato a una proprietà. Un modo comune consiste nell'usare un [ `EventTrigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.EventTrigger/), che definisce due proprietà:

- [`Event`](https://developer.xamarin.com/api/property/Xamarin.Forms.EventTrigger.Event/) di tipo `string`, il nome di un evento
- [`Actions`](https://developer.xamarin.com/api/property/Xamarin.Forms.EventTrigger.Actions/) di tipo `IList<TriggerAction>`, un elenco di azioni da eseguire in risposta.

Per utilizzare questo, è necessario scrivere una classe che deriva da [ `TriggerAction<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.TriggerAction%3CT%3E/), in genere `TriggerAction<VisualElement>`. È possibile definire le proprietà di questa classe. Si tratta di semplici proprietà CLR anziché proprietà associabili perché `TriggerAction` non deriva da `BindableObject`. È necessario eseguire l'override di [ `Invoke` ](https://developer.xamarin.com/api/member/Xamarin.Forms.TriggerAction%3CT%3E.Invoke/p/T/) metodo che viene chiamato quando viene richiamata l'azione. L'argomento è l'elemento di destinazione.

Il [ `ScaleAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleAction.cs) classe il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria è riportato un esempio. Chiama il `ScaleTo` proprietà da animare la `Scale` proprietà di un elemento. Perché una delle relative proprietà è di tipo `Easing`, [ `EasingConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/EasingConverter.cs) classe consente di utilizzare lo standard `Easing` campi statici in XAML.

Il [ **EntrySwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntrySwell) esempio viene illustrato come richiamare il `ScaleAction` da `EventTrigger` gli oggetti che monitora il `Focused` e `Unfocused` eventi.

Il [ **CustomEasingSwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/CustomEasingSwell) esempio viene illustrato come definire una funzione di interpolazione personalizzata per `ScaleAction` in un file code-behind.

È inoltre possibile richiamare le azioni utilizzando un `Trigger` (distinguished da `EventTrigger`). Ciò è necessario essere a conoscenza che `TriggerBase` definisce due raccolte:

- [`EnterActions`](https://developer.xamarin.com/api/property/Xamarin.Forms.TriggerBase.EnterActions/) di tipo `IList<TriggerAction>`
- [`ExitActions`](https://developer.xamarin.com/api/property/Xamarin.Forms.TriggerBase.ExitActions/) di tipo `IList<TriggerAction>`

Il [ **EnterExitSwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EnterExitSwell) esempio viene illustrato come utilizzare queste raccolte.

### <a name="more-event-triggers"></a>Più trigger di evento

Il [ `ScaleUpAndDownAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleUpAndDownAction.cs) classe il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) chiamate della libreria `ScaleTo` due volte per applicare la scalabilità verticale. Il [ **ButtonGrowth** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonGrowth) esempio si utilizza in un stile `EventTrigger` per fornire indicazioni visive durante un `Button` viene premuto. Questa animazione double è inoltre possibile utilizzare due azioni nella raccolta di tipo [`DelayedScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DelayedScaleAction.cs)

Il [ `ShiverAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ShiverAction.cs) classe il **Xamarin.FormsBook.Toolkit** libreria definisce un'azione potenziale personalizzabile. Il [ **ShiverButtonDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverButtonDemo) esempio viene illustrato come.

Il [ `NumericValidationAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationAction.cs) classe il **Xamarin.FormsBook.Toolkit** library è limitato a `Entry` elementi e imposta il `TextColor` proprietà rosso se il `Text` proprietà non è un `double`. Il [ **TriggerEntryValidation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TriggerEntryValidation) esempio viene illustrato come.

### <a name="data-triggers"></a>Trigger Data

Il [ `DataTrigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.DataTrigger/) è simile al `Trigger` ad eccezione del fatto che invece di una proprietà per le modifiche dei valori di monitoraggio, esegue il monitoraggio un'associazione dati. In questo modo una proprietà in un elemento da assegnare a una proprietà in un altro elemento.

`DataTrigger` definisce tre proprietà:

- [`Binding`](https://developer.xamarin.com/api/property/Xamarin.Forms.DataTrigger.Binding/) di tipo `BindingBase`
- [`Value`](https://developer.xamarin.com/api/property/Xamarin.Forms.DataTrigger.Value/) di tipo `Object`
- [`Setters`](https://developer.xamarin.com/api/property/Xamarin.Forms.DataTrigger.Setters/) di tipo `IList<SetterBase>`

Il [ **GenderColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/GenderColors) esempio richiede il [ **SchoolOfFineArt** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) libreria e imposta i colori dei nomi degli studenti impostandolo sul blu o in base rosa il `Sex` proprietà:

[![Schermata triplo dei colori Gender](images/ch23fg04-small.png "colori Gender")](images/ch23fg04-large.png "Gender colori")

Il [ **ButtonEnabler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonEnabler) set di esempio di `IsEnabled` proprietà di un `Entry` per `False` se il `Length` proprietà del `Text` proprietà del `Entry`è uguale a 0. Si noti che il `Text` proprietà viene inizializzata su una stringa vuota, per impostazione predefinita è `null`e `DataTrigger` procedura non funziona correttamente.

### <a name="combining-conditions-in-the-multitrigger"></a>La combinazione di condizioni nel MultiTrigger

Il [ `MultiTrigger` ](https://developer.xamarin.com/api/type/Xamarin.Forms.MultiTrigger/) è una raccolta di condizioni. Quando si trovano tutti `true`, quindi vengono applicati i metodi di impostazione. La classe definisce due proprietà:

- [`Conditions`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiTrigger.Conditions/) di tipo `IList<Condition>`
- [`Setters`](https://developer.xamarin.com/api/property/Xamarin.Forms.MultiTrigger.Setters/) di tipo `IList<Setter>`

[`Condition`](https://developer.xamarin.com/api/type/Xamarin.Forms.Condition/) è una classe astratta e dispone di due classi discendente:

- [`PropertyCondition`](https://developer.xamarin.com/api/type/Xamarin.Forms.Condition/), che dispone di [ `Property` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PropertyCondition.Property/) e [ `Value` ](https://developer.xamarin.com/api/property/Xamarin.Forms.PropertyCondition.Value/) proprietà quali `Trigger`
- [`BindingCondition`](https://developer.xamarin.com/api/type/Xamarin.Forms.BindingCondition/), che dispone di [ `Binding` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingCondition.Binding/) e [ `Value` ](https://developer.xamarin.com/api/property/Xamarin.Forms.BindingCondition.Value/) proprietà quali `DataTrigger`

Nel [ **AndConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/AndConditions) esempio, un `BoxView` colorato solo quando quattro `Switch` elementi siano tutti accesi.

Il [ **OrConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/OrConditions) esempio viene illustrato come effettuare una `BoxView` un colore quando *qualsiasi* di quattro `Switch` elementi siano accesi. Ciò richiede che un'applicazione del diritto di Morgan De e inversione di tutta la logica.

La combinazione di e e o logica è semplice e richiede in genere invisibile `Switch` elementi per i risultati intermedi. Il [ **XorConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/XorConditions) esempio viene illustrato come un `Button` possono essere abilitate se uno di due `Entry` elementi hanno un testo digitato, ma non se entrambi i casi presentano un testo digitato.

## <a name="behaviors"></a>comportamenti

Qualsiasi elemento è possibile eseguire con un trigger, è inoltre possibile eseguire con un comportamento, ma i comportamenti richiedono sempre una classe che deriva da [ `Behavior<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Behavior%3CT%3E/) ed esegue l'override dei due metodi seguenti:

- [`OnAttachedTo`](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnAttachedTo/p/T/)
- [`OnDetachingFrom`](https://developer.xamarin.com/api/member/Xamarin.Forms.Behavior%3CT%3E.OnDetachingFrom/p/T/)

L'argomento è l'elemento collegato per il comportamento. In genere, il `OnAttachedTo` metodo allega alcuni gestori di eventi e `OnDetachingFrom` li rimuove. Poiché in genere, tale classe Salva lo stato di alcuni, in genere non può essere condiviso un `Style`.

[**BehaviorEntryValidation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BehaviorEntryValidation) è simile all'esempio **TriggerEntryValidation** ad eccezione del fatto che usa un comportamento & #x 2014; il [ `NumericValidationBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationBehavior.cs) classe il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria.

### <a name="behaviors-with-properties"></a>Comportamenti con proprietà

`Behavior<T>` deriva da `Behavior`, che deriva da `BindableObject`, pertanto è possibile definire proprietà associabili in un comportamento. Queste proprietà possono essere attive nel data binding.

Come illustrato nel [ **EmailValidationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationDemo) utilizzare programma che rende il [ `ValidEmailBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ValidEmailBehavior.cs) classe il [  **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria. `ValidEmailBehavior` funge da fonte data binding ha una proprietà associabile di sola lettura.

Il [ **EmailValidationConv** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationConv) esempio si utilizza lo stesso comportamento per visualizzare un altro tipo di indicatore per segnalare che un indirizzo di posta elettronica è valido.

Il [ **EmailValidationTrigger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationTrigger) esempio è una variante dell'esempio precedente. ButtonGlide utilizza un `DataTrigger` in combinazione con tale comportamento.

### <a name="toggles-and-check-boxes"></a>Attiva o disattiva e caselle di controllo

È possibile incapsulare il comportamento di un interruttore in una classe, ad esempio [ `ToggleBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBehavior.cs) nel [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria, quindi definire tutti gli oggetti visivi per l'elemento toggle interamente in XAML.

Il [ **ToggleLabel** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ToggleLabel) esempio viene utilizzata la `ToggleBehavior` con un `DataTrigger` per utilizzare un `Label` con due stringhe di testo per l'elemento toggle.

Il [ **FormattedTextToggle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/FormattedTextToggle) esempio estende questo concetto passando tra due `FormattedString` oggetti.

Il [ `ToggleBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBase.cs) classe il **Xamarin.FormsBook.Toolkit** libreria deriva da `ContentView`, definisce un `IsToggled` , proprietà e incorpora un `ToggleBehavior` per l'elemento toggle logica. Questo rende più semplice definire l'interruttore in XAML, come illustrato di [ **TranditionalCheckBox** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalCheckBox) esempio.

Il [ **SwitchCloneDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/SwitchCloneDemo) include un [ `SwitchClone` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter23/SwitchCloneDemo/SwitchCloneDemo/SwitchCloneDemo/SwitchClone.cs) classe che deriva da `ToggleBase` e utilizza un [ `TranslateAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TranslateAction.cs)classe per costruire un interruttore simile di xamarin. Forms `Switch`.

A [ `RotateAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RotateAction.cs) nel **Xamarin.FormsBook.Toolkit** fornisce un'animazione utilizzata per rendere un'animata leva il [ **LeverToggle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/LeverToggle)esempio.

### <a name="responding-to-taps"></a>Risponde alle scelte

Uno svantaggio di `EventTrigger` è che non è possibile collegare a un `TapGestureRecognizer` per rispondere a scelte. Recupero di tale problema è lo scopo di [ `TapBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TapBehavior.cs) nel [ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

Il [ **BoxViewTapShiver** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BoxViewTapShiver) esempio Usa `TapBehavior` utilizzare precedente `ShiverAction` per toccato `BoxView` elementi.

Il [ **ShiverViews** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverViews) esempio viene illustrato come ridurre il markup incapsulando una `ShiverView` classe.

### <a name="radio-buttons"></a>Pulsanti di opzione

Il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria include anche un [ `RadioBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioBehavior.cs) classe per l'esecuzione di pulsanti di opzione che vengono raggruppati in base una `string` nome del gruppo.

Il [ **RadioLabels** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioLabels) programma utilizza le stringhe di testo per il pulsante di opzione. Il [ **RadioStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioStyle) esempio viene utilizzata una `Style` per la differenza tra i pulsanti checked e unchecked l'aspetto. Il [ **RadioImages** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioImages) esempio utilizza immagini boxed per relativi pulsanti di opzione:

[![Schermata triplo delle immagini Radio](images/ch23fg17-small.png "le immagini dei pulsanti di opzione")](images/ch23fg17-large.png "le immagini dei pulsanti di opzione")

Il [ **TraditionalRadios** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalRadios) esempio disegna tradizionale vengono visualizzati pulsanti di opzione con un punto all'interno di un cerchio.

### <a name="fades-and-orientation"></a>Dissolvenza e l'orientamento

L'esempio finale, [ **MultiColorSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/MultiColorSliders) consente di passare tra tre visualizzazioni diverse-selezione del colore mediante pulsanti di opzione. Le tre viste dissolvenza in entrata e in uscita tramite un [ `FadeEnableAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/FadeEnableAction.cs) nel [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria.

Il programma risponde anche alle modifiche in orientamento verticale e orizzontale utilizzando un [ `GridOrientationBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/GridOrientationBehavior.cs) nel **Xamarin.FormsBook.Toolkit** libreria.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 23 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch23-Apr2016.pdf)
- [Esempi di capitolo 23](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)
- [Utilizzo di trigger](~/xamarin-forms/app-fundamentals/triggers.md)
- [Comportamenti di xamarin. Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md)
