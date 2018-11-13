---
title: Riepilogo del capitolo 23. I trigger e comportamenti
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 23. I trigger e comportamenti'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 19E84B5D-46B4-4B6D-A255-87BEFB011261
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 4bfa2bed7061e031c55ccbdb7f576aa02c17581a
ms.sourcegitcommit: 03dfb4a2c20ad68515875b415e7d84ee9b0a8cb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2018
ms.locfileid: "51563992"
---
# <a name="summary-of-chapter-23-triggers-and-behaviors"></a>Riepilogo del capitolo 23. I trigger e comportamenti

I trigger e i comportamenti sono simili, in quanto entrambe sono concepite per essere usati nei file XAML per semplificare le interazioni elemento oltre l'uso di associazioni dati e per estendere la funzionalità degli elementi XAML. Trigger e i comportamenti sono quasi sempre usati con oggetti dell'interfaccia utente visual.

Per supportare i trigger e comportamenti predefiniti, entrambe `VisualElement` e `Style` supportano due proprietà di raccolta:

- [`VisualElement.Triggers`](xref:Xamarin.Forms.VisualElement.Triggers) e [ `Style.Triggers` ](xref:Xamarin.Forms.Style.Triggers) typu `IList<TriggerBase>`
- [`VisualElement.Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) e [ `Style.Behaviors` ](xref:Xamarin.Forms.Style.Behaviors) typu `IList<Behavior>`

## <a name="triggers"></a>Trigger

Un trigger è una condizione (modifica di una proprietà o l'attivazione di un evento) che genera la risposta (un'altra modifica della proprietà o eseguire il codice). Il `Triggers` proprietà di `VisualElement` e `Style` JE typu `IList<TriggersBase>`. [`TriggerBase`](xref:Xamarin.Forms.TriggerBase) è una classe astratta dalla quale derivano le classi sealed quattro:

- [`Trigger`](xref:Xamarin.Forms.Trigger) per le risposte in base alle modifiche di proprietà
- [`EventTrigger`](xref:Xamarin.Forms.EventTrigger) per le risposte in base a esecuzioni di evento
- [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) per le risposte di base sul data binding
- [`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger) per le risposte in base a più trigger

Il trigger è sempre impostato sull'elemento con la proprietà viene modificata dal trigger.

### <a name="the-simplest-trigger"></a>Il trigger più semplice

Il [ `Trigger` ](xref:Xamarin.Forms.Trigger) verifica la presenza di una modifica del valore della proprietà di classe e risponde impostando un'altra proprietà dello stesso elemento.

`Trigger` definisce tre proprietà:

- [`Property`](xref:Xamarin.Forms.Trigger.Property) di tipo `BindableProperty`
- [`Value`](xref:Xamarin.Forms.Trigger.Value) di tipo `Object`
- [`Setters`](xref:Xamarin.Forms.Trigger.Setters) di tipo `IList<SetterBase>`, la proprietà di contenuto di `Trigger`

È inoltre `Trigger` richiede che la seguente proprietà ereditata da `TriggerBase` impostare:

- [`TargetType`](xref:Xamarin.Forms.TriggerBase.TargetType) per indicare il tipo di elemento sul quale il `Trigger` collegata

Il `Property` e `Value` costituiscono la condizione e `Setters` raccolta corrisponde alla risposta. Quando indicato `Property` ha il valore indicato dal `Value`, il `Setter` gli oggetti nel `Setters` vengono applicati insieme. Quando il `Property` ha un valore diverso, vengono rimossi i Setter. `Setter` definisce due proprietà che corrispondono alle prime due proprietà di `Trigger`:

- [`Property`](xref:Xamarin.Forms.Setter.Property) di tipo `BindableProperty`
- [`Value`](xref:Xamarin.Forms.Setter.Value) di tipo `Object`

Il [ **EntryPop** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPop) esempio viene illustrato come un `Trigger` applicate a un `Entry` può aumentare le dimensioni del `Entry` tramite il `Scale` proprietà quando il `IsFocused`della proprietà di `Entry` è `true`.

Sebbene non sia comune, il `Trigger` può essere impostata nel codice, come i [ **EntryPopCode** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPopCode) esempio viene illustrato.

Il [ **StyledTriggers** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/StyledTriggers) esempio viene illustrato come il `Trigger` può essere impostata un `Style` da applicare a più `Entry` elementi.

### <a name="trigger-actions-and-animations"></a>Le azioni dei trigger e le animazioni

È anche possibile eseguire un piccolo codice basato su un trigger. Questo codice può essere un'animazione che ha come destinazione una proprietà. Un modo comune consiste nell'usare un [ `EventTrigger` ](xref:Xamarin.Forms.EventTrigger), che definisce due proprietà:

- [`Event`](xref:Xamarin.Forms.EventTrigger.Event) di tipo `string`, il nome di un evento
- [`Actions`](xref:Xamarin.Forms.EventTrigger.Actions) di tipo `IList<TriggerAction>`, un elenco di azioni da eseguire in risposta.

Per usarlo, è necessario scrivere una classe che deriva da [ `TriggerAction<T>` ](xref:Xamarin.Forms.TriggerAction`1), in genere `TriggerAction<VisualElement>`. È possibile definire le proprietà di questa classe. Questi sono semplici proprietà CLR anziché le proprietà associabili perché `TriggerAction` non deriva da `BindableObject`. È necessario eseguire l'override di [ `Invoke` ](xref:Xamarin.Forms.TriggerAction`1.Invoke*) metodo che viene chiamato quando viene richiamata l'azione. L'argomento è l'elemento di destinazione.

Il [ `ScaleAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleAction.cs) classe la [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria è un esempio. Chiama il `ScaleTo` proprietà da animare il `Scale` proprietà di un elemento. Perché una delle sue proprietà è di tipo `Easing`, il [ `EasingConverter` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/EasingConverter.cs) classe consente di usare lo standard `Easing` campi statici in XAML.

Il [ **EntrySwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntrySwell) esempio viene illustrato come richiamare il `ScaleAction` da `EventTrigger` gli oggetti che monitora il `Focused` e `Unfocused` eventi.

Il [ **CustomEasingSwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/CustomEasingSwell) esempio viene illustrato come definire una funzione di interpolazione personalizzata per `ScaleAction` in un file code-behind.

È anche possibile richiamare le azioni mediante un `Trigger` (come distinguished da `EventTrigger`). Ciò è necessario essere a conoscenza che `TriggerBase` definisce due raccolte:

- [`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions) di tipo `IList<TriggerAction>`
- [`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions) di tipo `IList<TriggerAction>`

Il [ **EnterExitSwell** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EnterExitSwell) esempio viene illustrato come utilizzare queste raccolte.

### <a name="more-event-triggers"></a>Più trigger di evento

Il [ `ScaleUpAndDownAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleUpAndDownAction.cs) classe la [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) chiamate alla libreria `ScaleTo` due volte per applicare la scalabilità verticale. Il [ **ButtonGrowth** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonGrowth) esempio utilizza questo in un stile `EventTrigger` per fornire un feedback visivo quando un `Button` viene premuto. Questa animazione double è anche possibile usando due azioni nella raccolta di tipo [`DelayedScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DelayedScaleAction.cs)

Il [ `ShiverAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ShiverAction.cs) classe la **Xamarin.FormsBook.Toolkit** libreria definisce un'azione potenziale personalizzabili. Il [ **ShiverButtonDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverButtonDemo) esempio illustra come fare.

Il [ `NumericValidationAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationAction.cs) classe la **Xamarin.FormsBook.Toolkit** library è limitato ai `Entry` elementi e i set il `TextColor` proprietà rosso se il `Text` proprietà non è un `double`. Il [ **TriggerEntryValidation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TriggerEntryValidation) esempio illustra come fare.

### <a name="data-triggers"></a>Trigger di dati

Il [ `DataTrigger` ](xref:Xamarin.Forms.DataTrigger) è simile al `Trigger` ad eccezione del fatto che invece di una proprietà per le modifiche dei valori di monitoraggio, consente di monitorare un data binding. In questo modo una proprietà in un elemento da assegnare una proprietà in un altro elemento.

`DataTrigger` definisce tre proprietà:

- [`Binding`](xref:Xamarin.Forms.DataTrigger.Binding) di tipo `BindingBase`
- [`Value`](xref:Xamarin.Forms.DataTrigger.Value) di tipo `Object`
- [`Setters`](xref:Xamarin.Forms.DataTrigger.Setters) di tipo `IList<SetterBase>`

Il [ **GenderColors** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/GenderColors) esempio richiede il [ **SchoolOfFineArt** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) libreria e imposta i colori dei nomi degli studenti impostandolo sul blu o rosa base il `Sex` proprietà:

[![Schermata triplo dei colori di sesso](images/ch23fg04-small.png "colori Gender")](images/ch23fg04-large.png#lightbox "Gender colori")

Il [ **ButtonEnabler** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonEnabler) set di esempio il `IsEnabled` proprietà di un `Entry` per `False` se il `Length` proprietà del `Text` proprietà la `Entry`è uguale a 0. Si noti che il `Text` proprietà viene inizializzata su una stringa vuota, per impostazione predefinita è `null`e il `DataTrigger` non avrebbero funzionato correttamente.

### <a name="combining-conditions-in-the-multitrigger"></a>La combinazione delle condizioni nel MultiTrigger

Il [ `MultiTrigger` ](xref:Xamarin.Forms.MultiTrigger) è una raccolta di condizioni. Quando si trovano tutti `true`, quindi vengono applicati Setter. La classe definisce due proprietà:

- [`Conditions`](xref:Xamarin.Forms.MultiTrigger.Conditions) di tipo `IList<Condition>`
- [`Setters`](xref:Xamarin.Forms.MultiTrigger.Setters) di tipo `IList<Setter>`

[`Condition`](xref:Xamarin.Forms.Condition) è una classe astratta e ha due le classi discendenti:

- [`PropertyCondition`](xref:Xamarin.Forms.Condition), che dispone [ `Property` ](xref:Xamarin.Forms.PropertyCondition.Property) e [ `Value` ](xref:Xamarin.Forms.PropertyCondition.Value) proprietà, ad esempio `Trigger`
- [`BindingCondition`](xref:Xamarin.Forms.BindingCondition), che dispone [ `Binding` ](xref:Xamarin.Forms.BindingCondition.Binding) e [ `Value` ](xref:Xamarin.Forms.BindingCondition.Value) proprietà, ad esempio `DataTrigger`

Nel [ **AndConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/AndConditions) campione, una `BoxView` viene colorato solo quando quattro `Switch` elementi siano tutti accesi.

Il [ **OrConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/OrConditions) esempio viene illustrato come è possibile rendere un `BoxView` un colore quando *qualsiasi* pari a quattro `Switch` elementi siano accesi. Ciò richiede che un'applicazione della legge di Morgan De e l'annullamento della tutta la logica.

La combinazione di AND e o per la logica non è così semplice e richiede in genere invisibile `Switch` elementi per i risultati intermedi. Il [ **XorConditions** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/XorConditions) esempio viene illustrato come un `Button` può essere abilitata se uno di due `Entry` elementi hanno un testo digitato, ma non se hanno entrambi un testo digitato.

## <a name="behaviors"></a>comportamenti

Tutto ciò è possibile eseguire con un trigger, è anche possibile eseguire con un comportamento, ma i comportamenti richiedono sempre una classe che deriva da [ `Behavior<T>` ](xref:Xamarin.Forms.Behavior`1) ed esegue l'override dei due metodi seguenti:

- [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo*)
- [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom*)

L'argomento è l'elemento a cui è associato il comportamento. In genere, il `OnAttachedTo` metodo allega alcuni gestori di eventi, e `OnDetachingFrom` Scollega li. Poiché in genere, tale classe salva alcuni stati, non a livello generale può essere condiviso un `Style`.

[**BehaviorEntryValidation** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BehaviorEntryValidation) è simile all'esempio **TriggerEntryValidation** ad eccezione del fatto che usa un comportamento &mdash; il [ `NumericValidationBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationBehavior.cs) classe di [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria.

### <a name="behaviors-with-properties"></a>Comportamenti con proprietà

`Behavior<T>` deriva da `Behavior`, che deriva da `BindableObject`, pertanto è possibile definire proprietà associabili in un comportamento. Queste proprietà possono essere attive nel data binding.

Come illustrato nel [ **EmailValidationDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationDemo) programma che consente di utilizzare il [ `ValidEmailBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ValidEmailBehavior.cs) classe la [  **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria. `ValidEmailBehavior` dispone di una proprietà associabile di sola lettura e funge da un'origine nel data binding.

Il [ **EmailValidationConv** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationConv) esempio viene usato lo stesso comportamento per visualizzare un altro tipo di indicatore per segnalare che un indirizzo di posta elettronica sia valido.

Il [ **EmailValidationTrigger** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationTrigger) esempio è una variante dell'esempio precedente. ButtonGlide Usa un `DataTrigger` in combinazione con tale comportamento.

### <a name="toggles-and-check-boxes"></a>Gli elementi Toggle e caselle di controllo

È possibile incapsulare il comportamento di un interruttore in una classe, ad esempio [ `ToggleBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBehavior.cs) nel [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria, quindi definire tutti gli oggetti visivi per l'elemento toggle interamente in XAML.

Il [ **ToggleLabel** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ToggleLabel) nell'esempio il `ToggleBehavior` con un `DataTrigger` da utilizzare un `Label` con due stringhe di testo per l'elemento toggle.

Il [ **FormattedTextToggle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/FormattedTextToggle) esempio estende questo concetto passando tra due `FormattedString` oggetti.

Il [ `ToggleBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBase.cs) classe la **Xamarin.FormsBook.Toolkit** libreria deriva da `ContentView`, definisce un `IsToggled` proprietà e incorpora un `ToggleBehavior` per l'elemento toggle per la logica. Questo rende più semplice definire l'interruttore in XAML, come dimostrato dal [ **TraditionalCheckBox** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalCheckBox) esempio.

Il [ **SwitchCloneDemo** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/SwitchCloneDemo) include un [ `SwitchClone` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter23/SwitchCloneDemo/SwitchCloneDemo/SwitchCloneDemo/SwitchClone.cs) classe che deriva da `ToggleBase` e Usa un [ `TranslateAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TranslateAction.cs)classi per costruire un interruttore che è simile a xamarin. Forms `Switch`.

Oggetto [ `RotateAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RotateAction.cs) nel **Xamarin.FormsBook.Toolkit** fornisce un'animazione utilizzata per rendere una levetta animato nel [ **LeverToggle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/LeverToggle)esempio.

### <a name="responding-to-taps"></a>Rispondere alle scelte

Uno svantaggio del `EventTrigger` è che non è possibile collegare a un `TapGestureRecognizer` rispondere alle scelte. È lo scopo di ottenere temporaneamente tale problema [ `TapBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TapBehavior.cs) nel [ **Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

Il [ **BoxViewTapShiver** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BoxViewTapShiver) esempio viene usato `TapBehavior` usare precedente `ShiverAction` per toccato `BoxView` elementi.

Il [ **ShiverViews** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverViews) esempio viene illustrato come ridurre il markup incapsulando una `ShiverView` classe.

### <a name="radio-buttons"></a>Pulsanti di opzione

Il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria ha anche una [ `RadioBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioBehavior.cs) classe per l'esecuzione di pulsanti di opzione che sono raggruppati in base un `string` nome del gruppo.

Il [ **RadioLabels** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioLabels) programma utilizza le stringhe di testo per il pulsante di opzione. Il [ **RadioStyle** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioStyle) esempio viene usato un `Style` per la differenza tra i pulsanti checked e unchecked l'aspetto. Il [ **RadioImages** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioImages) esempio Usa immagini sottoposto a conversione boxing per relativi pulsanti di opzione:

[![Schermata triplo delle immagini Radio](images/ch23fg17-small.png "le icone dei pulsanti di opzione")](images/ch23fg17-large.png#lightbox "le icone dei pulsanti di opzione")

Il [ **TraditionalRadios** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalRadios) esempio disegna tradizionale vengono visualizzati pulsanti di opzione con un punto all'interno di un cerchio.

### <a name="fades-and-orientation"></a>Dissolvenze e l'orientamento

L'esempio finale [ **MultiColorSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/MultiColorSliders) consente di passare tra tre visualizzazioni di selezione di colore diverso mediante pulsanti di opzione. Le tre viste dissolvenza in entrata e in uscita tramite un [ `FadeEnableAction` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/FadeEnableAction.cs) nel [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) libreria.

Il programma risponde anche alle modifiche apportate orientamento verticale e orizzontale usando un [ `GridOrientationBehavior` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/GridOrientationBehavior.cs) nel **Xamarin.FormsBook.Toolkit** libreria.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 23 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch23-Apr2016.pdf)
- [Capitolo 23 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)
- [Utilizzo di trigger](~/xamarin-forms/app-fundamentals/triggers.md)
- [Comportamenti di Xamarin.Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md)
