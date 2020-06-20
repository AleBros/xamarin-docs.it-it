---
title: Riepilogo del capitolo 23. Trigger e comportamenti
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms : riepilogo del capitolo 23. Trigger e comportamenti'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 19E84B5D-46B4-4B6D-A255-87BEFB011261
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 9a0206354254f79756e29f834c85837240736eca
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84136656"
---
# <a name="summary-of-chapter-23-triggers-and-behaviors"></a>Riepilogo del capitolo 23. Trigger e comportamenti

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)

I trigger e i comportamenti sono simili, in quanto sono entrambi destinati a essere usati nei file XAML per semplificare le interazioni tra gli elementi oltre l'uso delle associazioni dati e per estendere la funzionalità degli elementi XAML. I trigger e i comportamenti vengono usati quasi sempre con gli oggetti dell'interfaccia utente visivi.

Per supportare trigger e comportamenti, sia `VisualElement` che `Style` supportano due proprietà della raccolta:

- [`VisualElement.Triggers`](xref:Xamarin.Forms.VisualElement.Triggers)e [`Style.Triggers`](xref:Xamarin.Forms.Style.Triggers) di tipo`IList<TriggerBase>`
- [`VisualElement.Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors)e [`Style.Behaviors`](xref:Xamarin.Forms.Style.Behaviors) di tipo`IList<Behavior>`

## <a name="triggers"></a>Trigger

Un trigger è una condizione (una modifica della proprietà o l'attivazione di un evento) che restituisce una risposta (un'altra modifica della proprietà o l'esecuzione di codice). La `Triggers` proprietà di `VisualElement` e `Style` è di tipo `IList<TriggersBase>` . [`TriggerBase`](xref:Xamarin.Forms.TriggerBase)è una classe astratta da cui derivano quattro classi sealed:

- [`Trigger`](xref:Xamarin.Forms.Trigger)per le risposte in base alle modifiche delle proprietà
- [`EventTrigger`](xref:Xamarin.Forms.EventTrigger)per le risposte in base alle attivazioni di eventi
- [`DataTrigger`](xref:Xamarin.Forms.DataTrigger)per le risposte basate sui data binding
- [`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger)per risposte basate su più trigger

Il trigger è sempre impostato sull'elemento la cui proprietà viene modificata dal trigger.

### <a name="the-simplest-trigger"></a>Trigger più semplice

La [`Trigger`](xref:Xamarin.Forms.Trigger) classe verifica la presenza di una modifica del valore di una proprietà e risponde impostando un'altra proprietà dello stesso elemento.

`Trigger`definisce tre proprietà:

- [`Property`](xref:Xamarin.Forms.Trigger.Property)di tipo`BindableProperty`
- [`Value`](xref:Xamarin.Forms.Trigger.Value)di tipo`Object`
- [`Setters`](xref:Xamarin.Forms.Trigger.Setters)di tipo `IList<SetterBase>` , la proprietà Content di`Trigger`

`Trigger`Richiede inoltre che venga impostata la proprietà seguente ereditata `TriggerBase` :

- [`TargetType`](xref:Xamarin.Forms.TriggerBase.TargetType)per indicare il tipo dell'elemento a cui `Trigger` è collegato l'oggetto

`Property`E `Value` costituiscono la condizione e la `Setters` raccolta è la risposta. Quando l'oggetto indicato `Property` ha il valore indicato da `Value` , `Setter` vengono applicati gli oggetti della `Setters` raccolta. Quando `Property` ha un valore diverso, i setter vengono rimossi. `Setter`definisce due proprietà che corrispondono alle prime due proprietà di `Trigger` :

- [`Property`](xref:Xamarin.Forms.Setter.Property)di tipo`BindableProperty`
- [`Value`](xref:Xamarin.Forms.Setter.Value)di tipo`Object`

Nell'esempio [**EntryPop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPop) viene illustrato come un oggetto `Trigger` applicato a un oggetto `Entry` può aumentare le dimensioni di `Entry` tramite la `Scale` proprietà quando la `IsFocused` proprietà dell'oggetto `Entry` è `true` .

Sebbene non sia comune, `Trigger` può essere impostato nel codice, come illustrato nell'esempio [**EntryPopCode**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPopCode) .

Nell'esempio [**StyledTriggers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/StyledTriggers) viene illustrato come `Trigger` è possibile impostare l'oggetto in un oggetto `Style` da applicare a più `Entry` elementi.

### <a name="trigger-actions-and-animations"></a>Azioni trigger e animazioni

È anche possibile eseguire un po' di codice in base a un trigger. Questo codice può essere un'animazione destinata a una proprietà. Un modo comune consiste nell'usare un oggetto [`EventTrigger`](xref:Xamarin.Forms.EventTrigger) , che definisce due proprietà:

- [`Event`](xref:Xamarin.Forms.EventTrigger.Event)di tipo `string` , il nome di un evento
- [`Actions`](xref:Xamarin.Forms.EventTrigger.Actions)di tipo `IList<TriggerAction>` , un elenco di azioni da eseguire in risposta.

Per usarlo, è necessario scrivere una classe che deriva da [`TriggerAction<T>`](xref:Xamarin.Forms.TriggerAction`1) , in genere `TriggerAction<VisualElement>` . È possibile definire le proprietà in questa classe. Si tratta di proprietà CLR normali anziché di proprietà associabili `TriggerAction` , perché non deriva da `BindableObject` . È necessario eseguire l'override del [`Invoke`](xref:Xamarin.Forms.TriggerAction`1.Invoke*) metodo chiamato quando viene richiamata l'azione. L'argomento è l'elemento target.

La [`ScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleAction.cs) classe nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) è un esempio. Viene chiamata la `ScaleTo` proprietà per animare la `Scale` proprietà di un elemento. Poiché una delle proprietà è di tipo `Easing` , la [`EasingConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/EasingConverter.cs) classe consente di usare i `Easing` campi statici standard in XAML.

Nell'esempio [**EntrySwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntrySwell) viene illustrato come richiamare `ScaleAction` da `EventTrigger` oggetti che monitorano gli `Focused` eventi e `Unfocused` .

Nell'esempio [**CustomEasingSwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/CustomEasingSwell) viene illustrato come definire una funzione di interpolazione personalizzata per `ScaleAction` in un file code-behind.

È anche possibile richiamare le azioni usando un oggetto `Trigger` , come si distingue da `EventTrigger` . A tale scopo, è necessario tenere presente che `TriggerBase` definisce due raccolte:

- [`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions)di tipo`IList<TriggerAction>`
- [`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions)di tipo`IList<TriggerAction>`

L'esempio [**EnterExitSwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EnterExitSwell) illustra come usare queste raccolte.

### <a name="more-event-triggers"></a>Altri trigger di evento

La [`ScaleUpAndDownAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleUpAndDownAction.cs) classe nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) chiama `ScaleTo` due volte per eseguire la scalabilità verticale e verticale. L'esempio [**ButtonGrowth**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonGrowth) usa questo oggetto in un oggetto `EventTrigger` con stile per fornire un feedback visivo quando `Button` si preme un oggetto. Questa animazione Doppia è possibile anche usando due azioni nella raccolta di tipo[`DelayedScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DelayedScaleAction.cs)

La [`ShiverAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ShiverAction.cs) classe nella libreria **Novell. FormsBook. Toolkit** definisce un'azione Shiver personalizzabile. Viene illustrato l'esempio [**ShiverButtonDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverButtonDemo) .

La [`NumericValidationAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationAction.cs) classe nella libreria **Novell. FormsBook. Toolkit** è limitata agli `Entry` elementi e imposta la `TextColor` proprietà su rosso se la `Text` proprietà non è un oggetto `double` . Viene illustrato l'esempio [**TriggerEntryValidation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TriggerEntryValidation) .

### <a name="data-triggers"></a>Trigger di dati

[`DataTrigger`](xref:Xamarin.Forms.DataTrigger)È simile a, ad `Trigger` eccezione del fatto che anziché monitorare una proprietà per le modifiche ai valori, viene monitorata una data binding. Ciò consente a una proprietà in un elemento di influenzare una proprietà in un altro elemento.

`DataTrigger`definisce tre proprietà:

- [`Binding`](xref:Xamarin.Forms.DataTrigger.Binding)di tipo`BindingBase`
- [`Value`](xref:Xamarin.Forms.DataTrigger.Value)di tipo`Object`
- [`Setters`](xref:Xamarin.Forms.DataTrigger.Setters)di tipo`IList<SetterBase>`

L'esempio [**GenderColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/GenderColors) richiede la libreria [**SchoolOfFineArt**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) e imposta i colori dei nomi degli studenti su blu o rosa in base alla `Sex` proprietà:

[![Schermata tripla dei colori del sesso](images/ch23fg04-small.png "Colori sesso")](images/ch23fg04-large.png#lightbox "Colori sesso")

L'esempio [**ButtonEnabler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonEnabler) imposta la `IsEnabled` proprietà di un `Entry` su `False` se la `Length` proprietà della `Text` proprietà di `Entry` è uguale a 0. Si noti che la `Text` proprietà viene inizializzata su una stringa vuota, per impostazione predefinita è `null` e il `DataTrigger` non funzionerebbe correttamente.

### <a name="combining-conditions-in-the-multitrigger"></a>Combinazione di condizioni nel MultiTrigger

[`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger)È una raccolta di condizioni. Quando sono tutti `true` , vengono applicati i setter. La classe definisce due proprietà:

- [`Conditions`](xref:Xamarin.Forms.MultiTrigger.Conditions)di tipo`IList<Condition>`
- [`Setters`](xref:Xamarin.Forms.MultiTrigger.Setters)di tipo`IList<Setter>`

[`Condition`](xref:Xamarin.Forms.Condition)è una classe astratta e ha due classi discendenti:

- [`PropertyCondition`](xref:Xamarin.Forms.Condition), che include [`Property`](xref:Xamarin.Forms.PropertyCondition.Property) [`Value`](xref:Xamarin.Forms.PropertyCondition.Value) proprietà e come`Trigger`
- [`BindingCondition`](xref:Xamarin.Forms.BindingCondition), che include [`Binding`](xref:Xamarin.Forms.BindingCondition.Binding) [`Value`](xref:Xamarin.Forms.BindingCondition.Value) proprietà e come`DataTrigger`

Nell'esempio [**AndConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/AndConditions) , un `BoxView` è colorato solo quando quattro `Switch` elementi sono tutti attivati.

L'esempio [**OrConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/OrConditions) illustra come è possibile creare un `BoxView` colore quando *uno* dei quattro `Switch` elementi è attivato. Questa operazione richiede un'applicazione della legge de Morgan e inverte tutta la logica.

La combinazione della logica and e OR non è così semplice e richiede in genere elementi invisibili `Switch` per i risultati intermedi. Nell'esempio [**XorConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/XorConditions) viene illustrato come `Button` abilitare un oggetto se uno dei due `Entry` elementi presenta un testo digitato in, ma non se è presente un testo digitato in.

## <a name="behaviors"></a>comportamenti

Qualsiasi operazione che è possibile eseguire con un trigger può essere eseguita anche con un comportamento, ma i comportamenti richiedono sempre una classe che derivi da [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) e esegua l'override dei due metodi seguenti:

- [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo*)
- [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom*)

L'argomento è l'elemento a cui è associato il comportamento. In genere, il `OnAttachedTo` metodo associa alcuni gestori eventi e `OnDetachingFrom` li disconnette. Poiché tale classe generalmente salva uno stato, in genere non può essere condivisa in un oggetto `Style` .

L'esempio [**BehaviorEntryValidation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BehaviorEntryValidation) è simile a **TriggerEntryValidation** , ad eccezione del fatto che usa un comportamento &mdash; della [`NumericValidationBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationBehavior.cs) classe nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) .

### <a name="behaviors-with-properties"></a>Comportamenti con proprietà

`Behavior<T>`deriva da, `Behavior` che deriva da `BindableObject` , quindi le proprietà associabili possono essere definite in un comportamento. Queste proprietà possono essere attive nelle associazioni dati.

Questa operazione viene illustrata nel programma [**EmailValidationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationDemo) che usa la [`ValidEmailBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ValidEmailBehavior.cs) classe nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) . `ValidEmailBehavior`dispone di una proprietà associabile di sola lettura e funge da origine nelle associazioni dati.

L'esempio [**EmailValidationConv**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationConv) usa questo stesso comportamento per visualizzare un altro tipo di indicatore per segnalare che un indirizzo di posta elettronica è valido.

L'esempio [**EmailValidationTrigger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationTrigger) è una variante dell'esempio precedente. ButtonGlide usa un insieme a `DataTrigger` questo comportamento.

### <a name="toggles-and-check-boxes"></a>Interruttori e caselle di controllo

È possibile incapsulare il comportamento di un interruttore in una classe, ad esempio [`ToggleBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBehavior.cs) nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) , quindi definire tutti gli oggetti visivi per l'interruttore interamente in XAML.

Nell'esempio [**ToggleLabel**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ToggleLabel) viene usato `ToggleBehavior` con un oggetto `DataTrigger` per usare un oggetto `Label` con due stringhe di testo per l'interruttore.

L'esempio [**FormattedTextToggle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/FormattedTextToggle) estende questo concetto passando tra due `FormattedString` oggetti.

La [`ToggleBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBase.cs) classe nella libreria **Novell. FormsBook. Toolkit** deriva da `ContentView` , definisce una `IsToggled` proprietà e incorpora un `ToggleBehavior` per la logica di attivazione/disattivazione. Questo consente di definire più facilmente l'interruttore in XAML, come illustrato nell'esempio [**TraditionalCheckBox**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalCheckBox) .

[**SwitchCloneDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/SwitchCloneDemo) include una [`SwitchClone`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter23/SwitchCloneDemo/SwitchCloneDemo/SwitchCloneDemo/SwitchClone.cs) classe che deriva da `ToggleBase` e usa una [`TranslateAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TranslateAction.cs) classe per costruire un interruttore simile a Xamarin.Forms `Switch` .

Un oggetto [`RotateAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RotateAction.cs) in **Novell. FormsBook. Toolkit** fornisce un'animazione utilizzata per creare una levetta animata nell'esempio [**LeverToggle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/LeverToggle) .

### <a name="responding-to-taps"></a>Risposta ai rubinetti

Uno svantaggio di `EventTrigger` è che non è possibile collegarlo a un `TapGestureRecognizer` per rispondere ai rubinetti. Aggirare il problema è lo scopo di [`TapBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TapBehavior.cs) in [ **Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

Nell'esempio [**BoxViewTapShiver**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BoxViewTapShiver) viene usato `TapBehavior` per usare l'oggetto precedente `ShiverAction` per `BoxView` gli elementi toccati.

Nell'esempio [**ShiverViews**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverViews) viene illustrato come ridurre il markup incapsulando una `ShiverView` classe.

### <a name="radio-buttons"></a>Pulsanti di opzione

La libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) dispone anche [`RadioBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioBehavior.cs) di una classe per la creazione di pulsanti di opzione raggruppati in base a un nome di `string` gruppo.

Il programma [**Radiolabels**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioLabels) usa le stringhe di testo per il pulsante di opzione. Nell'esempio di [**radiostile**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioStyle) viene utilizzato un oggetto `Style` per la differenza nell'aspetto tra i pulsanti controllati e deselezionati. L'esempio [**Radioimages**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioImages) usa le immagini boxed per i pulsanti di opzione:

[![Schermata tripla delle immagini radiofoniche](images/ch23fg17-small.png "Immagini del pulsante di opzione")](images/ch23fg17-large.png#lightbox "Immagini del pulsante di opzione")

L'esempio [**TraditionalRadios**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalRadios) disegna i pulsanti di opzione di visualizzazione tradizionali con un punto all'interno di un cerchio.

### <a name="fades-and-orientation"></a>Dissolvenza e orientamento

L'esempio finale [**MultiColorSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/MultiColorSliders) consente di passare tra tre diverse visualizzazioni di selezione dei colori usando i pulsanti di opzione. Le tre visualizzazioni si dissolveranno in entrata e in uscita usando un oggetto [`FadeEnableAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/FadeEnableAction.cs) nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) .

Il programma risponde inoltre alle modifiche nell'orientamento tra verticale e orizzontale utilizzando un [`GridOrientationBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/GridOrientationBehavior.cs) nella libreria **Novell. FormsBook. Toolkit** .

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 23 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch23-Apr2016.pdf)
- [Esempi del capitolo 23](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)
- [Utilizzo di trigger](~/xamarin-forms/app-fundamentals/triggers.md)
- [Xamarin.FormsComportamenti](~/xamarin-forms/app-fundamentals/behaviors/creating.md)
