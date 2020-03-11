---
title: Riepilogo del capitolo 23. I trigger e comportamenti
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 23. I trigger e comportamenti'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 19E84B5D-46B4-4B6D-A255-87BEFB011261
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 8a1274a8447f49ce39f9c92703bbaec9e875b9e9
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "70760585"
---
# <a name="summary-of-chapter-23-triggers-and-behaviors"></a>Riepilogo del capitolo 23. I trigger e comportamenti

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)

I trigger e i comportamenti sono simili, in quanto entrambe sono concepite per essere usati nei file XAML per semplificare le interazioni elemento oltre l'uso di associazioni dati e per estendere la funzionalità degli elementi XAML. Trigger e i comportamenti sono quasi sempre usati con oggetti dell'interfaccia utente visual.

Per supportare trigger e comportamenti, sia `VisualElement` che `Style` supportano due proprietà della raccolta:

- [`VisualElement.Triggers`](xref:Xamarin.Forms.VisualElement.Triggers) e [`Style.Triggers`](xref:Xamarin.Forms.Style.Triggers) di tipo `IList<TriggerBase>`
- [`VisualElement.Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors) e [`Style.Behaviors`](xref:Xamarin.Forms.Style.Behaviors) di tipo `IList<Behavior>`

## <a name="triggers"></a>Trigger

Un trigger è una condizione (modifica di una proprietà o l'attivazione di un evento) che genera la risposta (un'altra modifica della proprietà o eseguire il codice). La proprietà `Triggers` di `VisualElement` e `Style` è di tipo `IList<TriggersBase>`. [`TriggerBase`](xref:Xamarin.Forms.TriggerBase) è una classe astratta da cui derivano quattro classi sealed:

- [`Trigger`](xref:Xamarin.Forms.Trigger) per le risposte in base alle modifiche delle proprietà
- [`EventTrigger`](xref:Xamarin.Forms.EventTrigger) per le risposte in base alla generazione di eventi
- [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) per le risposte basate sui data binding
- [`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger) per le risposte basate su più trigger

Il trigger è sempre impostato sull'elemento con la proprietà viene modificata dal trigger.

### <a name="the-simplest-trigger"></a>Il trigger più semplice

La classe [`Trigger`](xref:Xamarin.Forms.Trigger) controlla la presenza di una modifica del valore di una proprietà e risponde impostando un'altra proprietà dello stesso elemento.

`Trigger` definisce tre proprietà:

- [`Property`](xref:Xamarin.Forms.Trigger.Property) di tipo `BindableProperty`
- [`Value`](xref:Xamarin.Forms.Trigger.Value) di tipo `Object`
- [`Setters`](xref:Xamarin.Forms.Trigger.Setters) di tipo `IList<SetterBase>`, la proprietà content di `Trigger`

`Trigger` richiede inoltre che venga impostata la proprietà seguente ereditata da `TriggerBase`:

- [`TargetType`](xref:Xamarin.Forms.TriggerBase.TargetType) per indicare il tipo dell'elemento a cui è collegato il `Trigger`

Il `Property` e `Value` costituiscono la condizione e la raccolta di `Setters` è la risposta. Quando il `Property` indicato ha il valore indicato da `Value`, vengono applicati gli oggetti `Setter` nella raccolta `Setters`. Quando il `Property` ha un valore diverso, i setter vengono rimossi. `Setter` definisce due proprietà che corrispondono alle prime due proprietà di `Trigger`:

- [`Property`](xref:Xamarin.Forms.Setter.Property) di tipo `BindableProperty`
- [`Value`](xref:Xamarin.Forms.Setter.Value) di tipo `Object`

Nell'esempio [**EntryPop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPop) viene illustrato il modo in cui un `Trigger` applicato a una `Entry` può aumentare la dimensione del `Entry` tramite la proprietà `Scale` quando la proprietà `IsFocused` del `Entry` è `true`.

Sebbene non sia comune, il `Trigger` può essere impostato nel codice, come illustrato nell'esempio [**EntryPopCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPopCode) .

Nell'esempio [**StyledTriggers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/StyledTriggers) viene illustrato come è possibile impostare l'`Trigger` in un `Style` da applicare a più elementi `Entry`.

### <a name="trigger-actions-and-animations"></a>Le azioni dei trigger e le animazioni

È anche possibile eseguire un piccolo codice basato su un trigger. Questo codice può essere un'animazione che ha come destinazione una proprietà. Un modo comune consiste nell'usare un [`EventTrigger`](xref:Xamarin.Forms.EventTrigger), che definisce due proprietà:

- [`Event`](xref:Xamarin.Forms.EventTrigger.Event) di tipo `string`, il nome di un evento
- [`Actions`](xref:Xamarin.Forms.EventTrigger.Actions) di tipo `IList<TriggerAction>`, un elenco di azioni da eseguire in risposta.

Per usarlo, è necessario scrivere una classe che deriva da [`TriggerAction<T>`](xref:Xamarin.Forms.TriggerAction`1), in genere `TriggerAction<VisualElement>`. È possibile definire le proprietà di questa classe. Si tratta di proprietà CLR normali anziché di proprietà associabili, perché `TriggerAction` non deriva da `BindableObject`. È necessario eseguire l'override del metodo [`Invoke`](xref:Xamarin.Forms.TriggerAction`1.Invoke*) chiamato quando viene richiamata l'azione. L'argomento è l'elemento di destinazione.

La classe [`ScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleAction.cs) nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) è un esempio. Chiama la proprietà `ScaleTo` per animare la proprietà `Scale` di un elemento. Poiché una delle proprietà è di tipo `Easing`, la classe [`EasingConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/EasingConverter.cs) consente di usare i campi statici `Easing` statici in XAML.

Nell'esempio [**EntrySwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntrySwell) viene illustrato come richiamare il `ScaleAction` da `EventTrigger` oggetti che monitorano gli eventi `Focused` e `Unfocused`.

Nell'esempio [**CustomEasingSwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/CustomEasingSwell) viene illustrato come definire una funzione di interpolazione personalizzata per `ScaleAction` in un file code-behind.

È anche possibile richiamare le azioni utilizzando un `Trigger` (come si distingue da `EventTrigger`). A tale scopo, è necessario tenere presente che `TriggerBase` definisce due raccolte:

- [`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions) di tipo `IList<TriggerAction>`
- [`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions) di tipo `IList<TriggerAction>`

L'esempio [**EnterExitSwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EnterExitSwell) illustra come usare queste raccolte.

### <a name="more-event-triggers"></a>Più trigger di evento

La classe [`ScaleUpAndDownAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleUpAndDownAction.cs) nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) chiama due volte `ScaleTo` per la scalabilità verticale e verticale. L'esempio [**ButtonGrowth**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonGrowth) usa questo oggetto in un `EventTrigger` con stile per fornire un feedback visivo quando viene premuto un `Button`. Questa animazione Doppia è inoltre possibile utilizzando due azioni nella raccolta di tipo [`DelayedScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DelayedScaleAction.cs)

La classe [`ShiverAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ShiverAction.cs) nella libreria **Novell. FormsBook. Toolkit** definisce un'azione Shiver personalizzabile. Viene illustrato l'esempio [**ShiverButtonDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverButtonDemo) .

La classe [`NumericValidationAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationAction.cs) nella libreria **Novell. FormsBook. Toolkit** è limitata agli elementi `Entry` e imposta la proprietà `TextColor` su rosso se la proprietà `Text` non è un `double`. Viene illustrato l'esempio [**TriggerEntryValidation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TriggerEntryValidation) .

### <a name="data-triggers"></a>Trigger di dati

Il [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) è simile al `Trigger` ad eccezione del fatto che invece di monitorare una proprietà per le modifiche ai valori, viene monitorato un data binding. In questo modo una proprietà in un elemento da assegnare una proprietà in un altro elemento.

`DataTrigger` definisce tre proprietà:

- [`Binding`](xref:Xamarin.Forms.DataTrigger.Binding) di tipo `BindingBase`
- [`Value`](xref:Xamarin.Forms.DataTrigger.Value) di tipo `Object`
- [`Setters`](xref:Xamarin.Forms.DataTrigger.Setters) di tipo `IList<SetterBase>`

L'esempio [**GenderColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/GenderColors) richiede la libreria [**SchoolOfFineArt**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) e imposta i colori dei nomi degli studenti su blu o rosa in base alla proprietà `Sex`:

[![Schermata tripla dei colori del sesso](images/ch23fg04-small.png "Colori sesso")](images/ch23fg04-large.png#lightbox "Colori sesso")

L'esempio [**ButtonEnabler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonEnabler) imposta la proprietà `IsEnabled` di un `Entry` per `False` se la proprietà `Length` della proprietà `Text` del `Entry` è uguale a 0. Si noti che la proprietà `Text` è inizializzata su una stringa vuota. per impostazione predefinita è `null`e il `DataTrigger` non funzionerà correttamente.

### <a name="combining-conditions-in-the-multitrigger"></a>La combinazione delle condizioni nel MultiTrigger

Il [`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger) è una raccolta di condizioni. Quando sono tutti `true`, vengono applicati i setter. La classe definisce due proprietà:

- [`Conditions`](xref:Xamarin.Forms.MultiTrigger.Conditions) di tipo `IList<Condition>`
- [`Setters`](xref:Xamarin.Forms.MultiTrigger.Setters) di tipo `IList<Setter>`

[`Condition`](xref:Xamarin.Forms.Condition) è una classe astratta e ha due classi discendenti:

- [`PropertyCondition`](xref:Xamarin.Forms.Condition), che dispone di proprietà [`Property`](xref:Xamarin.Forms.PropertyCondition.Property) e [`Value`](xref:Xamarin.Forms.PropertyCondition.Value) come `Trigger`
- [`BindingCondition`](xref:Xamarin.Forms.BindingCondition), che dispone di proprietà [`Binding`](xref:Xamarin.Forms.BindingCondition.Binding) e [`Value`](xref:Xamarin.Forms.BindingCondition.Value) come `DataTrigger`

Nell'esempio [**AndConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/AndConditions) , un `BoxView` è colorato solo quando quattro elementi `Switch` sono tutti attivati.

Nell'esempio [**OrConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/OrConditions) viene illustrato come è possibile creare un `BoxView` un colore quando *uno* dei quattro elementi `Switch` sono attivati. Ciò richiede che un'applicazione della legge di Morgan De e l'annullamento della tutta la logica.

La combinazione di logica and e OR non è così semplice e richiede in genere elementi `Switch` invisibili per i risultati intermedi. Nell'esempio [**XorConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/XorConditions) viene illustrato come abilitare un `Button` se uno dei due elementi `Entry` dispone di un testo digitato in, ma non nel caso in cui sia presente un testo digitato.

## <a name="behaviors"></a>Comportamenti

Qualsiasi operazione che è possibile eseguire con un trigger può essere eseguita anche con un comportamento, ma i comportamenti richiedono sempre una classe che derivi da [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) ed esegua l'override dei due metodi seguenti:

- [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo*)
- [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom*)

L'argomento è l'elemento a cui è associato il comportamento. In genere, il metodo `OnAttachedTo` associa alcuni gestori di eventi e `OnDetachingFrom` li disconnette. Poiché tale classe generalmente salva uno stato, in genere non può essere condivisa in un `Style`.

L'esempio [**BehaviorEntryValidation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BehaviorEntryValidation) è simile a **TriggerEntryValidation** , ad eccezione del fatto che usa un comportamento &mdash; classe [`NumericValidationBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationBehavior.cs) nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) .

### <a name="behaviors-with-properties"></a>Comportamenti con proprietà

`Behavior<T>` deriva da `Behavior`, che deriva da `BindableObject`, quindi è possibile definire proprietà associabili in base a un comportamento. Queste proprietà possono essere attive nel data binding.

Questa operazione viene illustrata nel programma [**EmailValidationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationDemo) che usa la classe [`ValidEmailBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ValidEmailBehavior.cs) nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . `ValidEmailBehavior` dispone di una proprietà associabile di sola lettura e funge da origine nelle associazioni dati.

L'esempio [**EmailValidationConv**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationConv) usa questo stesso comportamento per visualizzare un altro tipo di indicatore per segnalare che un indirizzo di posta elettronica è valido.

L'esempio [**EmailValidationTrigger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationTrigger) è una variante dell'esempio precedente. ButtonGlide usa un `DataTrigger` in combinazione con tale comportamento.

### <a name="toggles-and-check-boxes"></a>Gli elementi Toggle e caselle di controllo

È possibile incapsulare il comportamento di un interruttore in una classe, ad esempio [`ToggleBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBehavior.cs) nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) , quindi definire tutti gli oggetti visivi per l'interruttore interamente in XAML.

L'esempio [**ToggleLabel**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ToggleLabel) usa il `ToggleBehavior` con un `DataTrigger` per usare un `Label` con due stringhe di testo per l'elemento di attivazione.

L'esempio [**FormattedTextToggle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/FormattedTextToggle) estende questo concetto passando tra due oggetti `FormattedString`.

La classe [`ToggleBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBase.cs) nella libreria **Novell. FormsBook. Toolkit** deriva da `ContentView`, definisce una proprietà `IsToggled` e incorpora una `ToggleBehavior` per la logica di attivazione/disattivazione. Questo consente di definire più facilmente l'interruttore in XAML, come illustrato nell'esempio [**TraditionalCheckBox**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalCheckBox) .

[**SwitchCloneDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/SwitchCloneDemo) include una classe [`SwitchClone`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter23/SwitchCloneDemo/SwitchCloneDemo/SwitchCloneDemo/SwitchClone.cs) che deriva da `ToggleBase` e usa una classe [`TranslateAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TranslateAction.cs) per costruire un interruttore simile al `Switch`Novell. Forms.

Un [`RotateAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RotateAction.cs) in **Novell. FormsBook. Toolkit** fornisce un'animazione utilizzata per creare una levetta animata nell'esempio [**LeverToggle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/LeverToggle) .

### <a name="responding-to-taps"></a>Rispondere alle scelte

Uno svantaggio di `EventTrigger` è che non è possibile collegarlo a una `TapGestureRecognizer` per rispondere ai rubinetti. Aggirare il problema è lo scopo di [`TapBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TapBehavior.cs) in [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

L'esempio [**BoxViewTapShiver**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BoxViewTapShiver) USA `TapBehavior` per usare i `ShiverAction` precedenti per gli elementi `BoxView` toccati.

Nell'esempio [**ShiverViews**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverViews) viene illustrato come ridurre il markup incapsulando una classe `ShiverView`.

### <a name="radio-buttons"></a>Pulsanti di opzione

La libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) dispone anche di una classe [`RadioBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioBehavior.cs) per la creazione di pulsanti di opzione raggruppati in base a un nome di gruppo di `string`.

Il programma [**Radiolabels**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioLabels) usa le stringhe di testo per il pulsante di opzione. Nell'esempio di [**radiostile**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioStyle) viene utilizzato un `Style` per la differenza nell'aspetto tra i pulsanti controllati e deselezionati. L'esempio [**Radioimages**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioImages) usa le immagini boxed per i pulsanti di opzione:

[![Schermata tripla delle immagini radiofoniche](images/ch23fg17-small.png "Immagini del pulsante di opzione")](images/ch23fg17-large.png#lightbox "Immagini del pulsante di opzione")

L'esempio [**TraditionalRadios**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalRadios) disegna i pulsanti di opzione di visualizzazione tradizionali con un punto all'interno di un cerchio.

### <a name="fades-and-orientation"></a>Dissolvenze e l'orientamento

L'esempio finale [**MultiColorSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/MultiColorSliders) consente di passare tra tre diverse visualizzazioni di selezione dei colori usando i pulsanti di opzione. Le tre visualizzazioni vengono rivolte in entrata e in uscita usando un [`FadeEnableAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/FadeEnableAction.cs) nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) .

Il programma risponde inoltre alle modifiche nell'orientamento tra verticale e orizzontale utilizzando un [`GridOrientationBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/GridOrientationBehavior.cs) nella libreria **Novell. FormsBook. Toolkit** .

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 23 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch23-Apr2016.pdf)
- [Esempi del capitolo 23](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)
- [Uso dei trigger](~/xamarin-forms/app-fundamentals/triggers.md)
- [Comportamenti di Xamarin.Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md)
