---
title: Riassunto del capitolo 23. Trigger e comportamenti
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 23. Trigger e comportamenti'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 19E84B5D-46B4-4B6D-A255-87BEFB011261
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 8a1274a8447f49ce39f9c92703bbaec9e875b9e9
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "70760585"
---
# <a name="summary-of-chapter-23-triggers-and-behaviors"></a>Riassunto del capitolo 23. Trigger e comportamenti

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)

I trigger e i comportamenti sono simili, in quanto sono entrambi destinati a essere usati nei file XAML per semplificare le interazioni degli elementi oltre l'uso delle associazioni dati e per estendere la funzionalità degli elementi XAML. Sia i trigger che i comportamenti vengono quasi sempre utilizzati con gli oggetti dell'interfaccia utente visivi.

Per supportare trigger e `VisualElement` comportamenti, entrambe le proprietà della raccolta e supportano due proprietà della raccolta:To support triggers and behaviors, both and `Style` support two collection properties:

- [`VisualElement.Triggers`](xref:Xamarin.Forms.VisualElement.Triggers)e [`Style.Triggers`](xref:Xamarin.Forms.Style.Triggers) di tipo`IList<TriggerBase>`
- [`VisualElement.Behaviors`](xref:Xamarin.Forms.VisualElement.Behaviors)e [`Style.Behaviors`](xref:Xamarin.Forms.Style.Behaviors) di tipo`IList<Behavior>`

## <a name="triggers"></a>Trigger

Un trigger è una condizione (una modifica di proprietà o la generazione di un evento) che genera una risposta (un'altra modifica di proprietà o l'esecuzione di codice). La `Triggers` proprietà `VisualElement` `Style` di ed `IList<TriggersBase>`è di tipo . [`TriggerBase`](xref:Xamarin.Forms.TriggerBase)è una classe astratta da cui derivano quattro classi sealed:Is an abstract class from which four sealed classes derive:

- [`Trigger`](xref:Xamarin.Forms.Trigger)per le risposte in base alle modifiche alle proprietà
- [`EventTrigger`](xref:Xamarin.Forms.EventTrigger)per le risposte in base alle attivazioni di eventi
- [`DataTrigger`](xref:Xamarin.Forms.DataTrigger)per le risposte basate su associazioni dati
- [`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger)per le risposte basate su più trigger

Il trigger viene sempre impostato sull'elemento la cui proprietà viene modificata dal trigger.

### <a name="the-simplest-trigger"></a>Il grilletto più semplice

La [`Trigger`](xref:Xamarin.Forms.Trigger) classe verifica la modifica di un valore di proprietà e risponde impostando un'altra proprietà dello stesso elemento.

`Trigger`definisce tre proprietà:

- [`Property`](xref:Xamarin.Forms.Trigger.Property)di tipo`BindableProperty`
- [`Value`](xref:Xamarin.Forms.Trigger.Value)di tipo`Object`
- [`Setters`](xref:Xamarin.Forms.Trigger.Setters)di `IList<SetterBase>`tipo , la proprietà content di`Trigger`

Inoltre, `Trigger` è necessario che sia `TriggerBase` impostata la seguente proprietà ereditata da:

- [`TargetType`](xref:Xamarin.Forms.TriggerBase.TargetType)per indicare il tipo di `Trigger` elemento a cui è associato l'oggetto

Il `Property` `Value` e comprendono la `Setters` condizione e la raccolta è la risposta. Quando `Property` l'oggetto indicato ha `Value`il valore `Setter` indicato `Setters` da , vengono applicati gli oggetti nell'insieme. Quando `Property` l'oggetto ha un valore diverso, i setter vengono rimossi. `Setter`definisce due proprietà le stesse delle prime `Trigger`due proprietà di :

- [`Property`](xref:Xamarin.Forms.Setter.Property)di tipo`BindableProperty`
- [`Value`](xref:Xamarin.Forms.Setter.Value)di tipo`Object`

Nell'esempio [**EntryPop**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPop) `Trigger` viene illustrato `Entry` come un oggetto `Entry` applicato `Scale` a `IsFocused` un oggetto `Entry` `true`può aumentare le dimensioni della proprietà tramite la proprietà quando la proprietà dell'oggetto è .

Anche se non è `Trigger` comune, l'oggetto può essere impostato nel codice, come illustrato nell'esempio [**EntryPopCode.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntryPopCode)

Il [**StyledTriggers**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/StyledTriggers) esempio `Trigger` viene illustrato come `Style` il può `Entry` essere impostato in un da applicare a più elementi.

### <a name="trigger-actions-and-animations"></a>Azioni trigger e animazioni

È anche possibile eseguire un po' di codice in base a un trigger. Questo codice può essere un'animazione destinata a una proprietà. Un modo comune consiste [`EventTrigger`](xref:Xamarin.Forms.EventTrigger)nell'utilizzare un oggetto , che definisce due proprietà:

- [`Event`](xref:Xamarin.Forms.EventTrigger.Event)di `string`tipo , il nome di un evento
- [`Actions`](xref:Xamarin.Forms.EventTrigger.Actions)di `IList<TriggerAction>`tipo , un elenco di azioni da eseguire in risposta.

Per utilizzare questa opzione, è necessario scrivere [`TriggerAction<T>`](xref:Xamarin.Forms.TriggerAction`1)una `TriggerAction<VisualElement>`classe che deriva da , in genere . È possibile definire le proprietà in questa classe. Si tratta di proprietà CLR semplici `TriggerAction` anziché di `BindableObject`proprietà associabili perché non derivano da . È necessario [`Invoke`](xref:Xamarin.Forms.TriggerAction`1.Invoke*) eseguire l'override del metodo chiamato quando viene richiamata l'azione. L'argomento è l'elemento di destinazione.

La [`ScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleAction.cs) classe nella libreria [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) è un esempio. Chiama la `ScaleTo` proprietà per `Scale` animare la proprietà di un elemento. Poiché una delle relative `Easing`proprietà [`EasingConverter`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/EasingConverter.cs) è di tipo `Easing` , la classe consente di utilizzare i campi statici standard in XAML.

Nell'esempio [**EntrySwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EntrySwell) viene `ScaleAction` illustrato `EventTrigger` come richiamare gli oggetti from che monitorano gli `Focused` eventi e `Unfocused` .

Nell'esempio [**CustomEasingSwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/CustomEasingSwell) viene illustrato come definire `ScaleAction` una funzione di andamento personalizzata per in un file code-behind.

È inoltre possibile richiamare azioni utilizzando un `Trigger` oggetto (come distinto da `EventTrigger`). A tale scopo, `TriggerBase` è necessario che si definisca due raccolte:This requires that you are aware that defines two collections:

- [`EnterActions`](xref:Xamarin.Forms.TriggerBase.EnterActions)di tipo`IList<TriggerAction>`
- [`ExitActions`](xref:Xamarin.Forms.TriggerBase.ExitActions)di tipo`IList<TriggerAction>`

Nell'esempio [**EnterExitSwell**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EnterExitSwell) viene illustrato come utilizzare queste raccolte.

### <a name="more-event-triggers"></a>Altri trigger di evento

La [`ScaleUpAndDownAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ScaleUpAndDownAction.cs) classe nella libreria [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) chiama `ScaleTo` due volte per aumentare e ridurre. Il [**ButtonGrowth**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonGrowth) esempio utilizza questo `EventTrigger` in uno stile `Button` per fornire un feedback visivo quando viene premuto un oggetto. Questa doppia animazione è possibile anche utilizzando due azioni nella raccolta di tipo[`DelayedScaleAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DelayedScaleAction.cs)

La [`ShiverAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ShiverAction.cs) classe nella libreria **Xamarin.FormsBook.Toolkit** definisce un'azione di brivido personalizzabile. [**L'esempio ShiverButtonDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverButtonDemo) lo dimostra.

La [`NumericValidationAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationAction.cs) classe nella libreria **Xamarin.FormsBook.Toolkit** è `Entry` limitata `TextColor` agli elementi `Text` e imposta `double`la proprietà su rosso se la proprietà non è un oggetto . [**Nell'esempio TriggerEntryValidation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TriggerEntryValidation) viene illustrato un'azione.

### <a name="data-triggers"></a>Trigger di dati

L'oggetto [`DataTrigger`](xref:Xamarin.Forms.DataTrigger) è `Trigger` simile al fatto che, anziché monitorare una proprietà per le modifiche dei valori, esegue il monitoraggio di un'associazione dati. Ciò consente a una proprietà in un elemento di influire su una proprietà in un altro elemento.

`DataTrigger`definisce tre proprietà:

- [`Binding`](xref:Xamarin.Forms.DataTrigger.Binding)di tipo`BindingBase`
- [`Value`](xref:Xamarin.Forms.DataTrigger.Value)di tipo`Object`
- [`Setters`](xref:Xamarin.Forms.DataTrigger.Setters)di tipo`IList<SetterBase>`

L'esempio [**GenderColors**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/GenderColors) richiede la libreria [**SchoolOfFineArt**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/SchoolOfFineArt) e imposta i colori dei `Sex` nomi degli studenti in blu o rosa in base alla proprietà:

[![Tripla schermata di Gender Colors](images/ch23fg04-small.png "Colori di genere")](images/ch23fg04-large.png#lightbox "Colori di genere")

Il [**ButtonEnabler**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ButtonEnabler) esempio `IsEnabled` imposta `Entry` la `False` proprietà `Length` di `Text` un su `Entry` se la proprietà della proprietà di uguale a 0. Si noti che la `Text` proprietà viene inizializzata su una stringa vuota. per impostazione `null`predefinita `DataTrigger` è , e l'oggetto non funzionerebbe correttamente.

### <a name="combining-conditions-in-the-multitrigger"></a>Combinazione di condizioni in MultiTriggerCombining conditions in the MultiTrigger

Il [`MultiTrigger`](xref:Xamarin.Forms.MultiTrigger) è una raccolta di condizioni. Quando sono `true`tutti , vengono applicati i setter. La classe definisce due proprietà:

- [`Conditions`](xref:Xamarin.Forms.MultiTrigger.Conditions)di tipo`IList<Condition>`
- [`Setters`](xref:Xamarin.Forms.MultiTrigger.Setters)di tipo`IList<Setter>`

[`Condition`](xref:Xamarin.Forms.Condition)è una classe astratta e ha due classi discendenti:

- [`PropertyCondition`](xref:Xamarin.Forms.Condition), che [`Property`](xref:Xamarin.Forms.PropertyCondition.Property) [`Value`](xref:Xamarin.Forms.PropertyCondition.Value) ha e proprietà come`Trigger`
- [`BindingCondition`](xref:Xamarin.Forms.BindingCondition), che [`Binding`](xref:Xamarin.Forms.BindingCondition.Binding) [`Value`](xref:Xamarin.Forms.BindingCondition.Value) ha e proprietà come`DataTrigger`

Nell'esempio [**AndConditions,**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/AndConditions) a `BoxView` è `Switch` colorato solo quando sono tutti attivati quattro elementi.

[**Nell'esempio OrConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/OrConditions) viene illustrato `BoxView` come creare un `Switch` colore *quando* uno qualsiasi dei quattro elementi è attivato. Ciò richiede un'applicazione della Legge di De Morgan e l'inversione di tutta la logica.

La combinazione di logica AND e OR `Switch` non è così facile e in genere richiede elementi invisibili per i risultati intermedi. Il [**XorConditions**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/XorConditions) esempio `Button` viene illustrato come un `Entry` può essere abilitato se uno dei due elementi hanno del testo digitato, ma non se entrambi hanno del testo digitato.

## <a name="behaviors"></a>Comportamenti

Qualsiasi operazione che è possibile eseguire con un trigger, è anche possibile eseguire [`Behavior<T>`](xref:Xamarin.Forms.Behavior`1) con un comportamento, ma i comportamenti richiedono sempre una classe che derivi e ne esegua l'override dei due metodi seguenti:Anything you can do with a trigger, you can also do with a behavior, but behaviors always require a class that derives from and overrides the following two methods:

- [`OnAttachedTo`](xref:Xamarin.Forms.Behavior`1.OnAttachedTo*)
- [`OnDetachingFrom`](xref:Xamarin.Forms.Behavior`1.OnDetachingFrom*)

L'argomento è l'elemento a cui è associato il comportamento. In genere, il `OnAttachedTo` metodo associa alcuni `OnDetachingFrom` gestori eventi e li scollega. Poiché tale classe in genere salva uno stato, `Style`in genere non può essere condivisa in un oggetto .

[**BehaviorEntryValidation**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BehaviorEntryValidation) esempio è simile a **TriggerEntryValidation,** ad eccezione del fatto che utilizza un comportamento &mdash; della [`NumericValidationBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/NumericValidationBehavior.cs) classe nella libreria [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

### <a name="behaviors-with-properties"></a>Comportamenti con proprietà

`Behavior<T>`deriva da `Behavior`, che `BindableObject`deriva da , in modo che le proprietà associabili possano essere definite in un comportamento. Queste proprietà possono essere attive nelle associazioni dati.

Ciò è dimostrato nel programma [**EmailValidationDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationDemo) [`ValidEmailBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ValidEmailBehavior.cs) che fa uso della classe nella libreria [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) `ValidEmailBehavior`dispone di una proprietà associabile di sola lettura e funge da origine nelle associazioni dati.

Nell'esempio [**EmailValidationConv**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationConv) viene utilizzato lo stesso comportamento per visualizzare un altro tipo di indicatore per segnalare che un indirizzo di posta elettronica è valido.

L'esempio [**EmailValidationTrigger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/EmailValidationTrigger) è una variante dell'esempio precedente. ButtonGlide utilizza `DataTrigger` un in combinazione con tale comportamento.

### <a name="toggles-and-check-boxes"></a>Attiva/disattiva e caselle di controllo

È possibile incapsulare il comportamento di un interruttore in una classe, ad [`ToggleBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBehavior.cs) esempio nella libreria [**Xamarin.FormsBook.Toolkit,**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) quindi definire tutti gli oggetti visivi per l'interruttore interamente in XAML.

L'esempio [**ToggleLabel**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ToggleLabel) usa l'oggetto `ToggleBehavior` con a `DataTrigger` per usare un `Label` con due stringhe di testo per l'interruttore.

L'esempio [**FormattedTextToggle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/FormattedTextToggle) estende questo concetto passando da un oggetto all'altro. `FormattedString`

La [`ToggleBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ToggleBase.cs) classe nella libreria **Xamarin.FormsBook.Toolkit** deriva `ContentView`da `IsToggled` , definisce `ToggleBehavior` una proprietà e incorpora un oggetto per la logica di attivazione/disattivazione. In questo modo è più semplice definire il pulsante di attivazione/disattivazione in XAML, come illustrato nell'esempio [**TraditionalCheckBox.This**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalCheckBox) makes it easier to define the toggle button in XAML, as demonstrated by the TraditionalCheckBox sample.

[**SwitchCloneDemo**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/SwitchCloneDemo) include [`SwitchClone`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter23/SwitchCloneDemo/SwitchCloneDemo/SwitchCloneDemo/SwitchClone.cs) una classe che `ToggleBase` deriva [`TranslateAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TranslateAction.cs) da e utilizza una classe per costruire un `Switch`pulsante di attivazione/disattivazione simile a Xamarin.Forms .

Oggetto [`RotateAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RotateAction.cs) in **Xamarin.FormsBook.Toolkit** fornisce un'animazione utilizzata per creare una leva animata nell'esempio [**LeverToggle.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/LeverToggle)

### <a name="responding-to-taps"></a>Rispondere ai rubinetti

Uno svantaggio `EventTrigger` è che non è possibile `TapGestureRecognizer` collegarlo a un per rispondere ai rubinetti. Come aggirare questo problema [`TapBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/TapBehavior.cs) è lo scopo di in [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

Il [**BoxViewTapShiver**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/BoxViewTapShiver) `TapBehavior` esempio utilizza `ShiverAction` per utilizzare `BoxView` il precedente per gli elementi toccati.

Il [**ShiverViews**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/ShiverViews) esempio viene illustrato come ridurre il `ShiverView` markup incapsulando una classe.

### <a name="radio-buttons"></a>Pulsanti di opzione

La libreria [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit) [`RadioBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/RadioBehavior.cs) dispone anche di una classe per `string` creare pulsanti di opzione raggruppati in base al nome di un gruppo.

Il programma [**RadioLabels**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioLabels) utilizza stringhe di testo per il pulsante di opzione. L'esempio [**RadioStyle**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioStyle) usa un `Style` per la differenza di aspetto tra i pulsanti selezionati e non selezionati. Nell'esempio RadioImages vengono utilizzate immagini boxed per i relativi pulsanti di opzione:The [**RadioImages**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/RadioImages) sample uses boxed images for its radio buttons:

[![Triplo screenshot di Radio Images](images/ch23fg17-small.png "Immagini dei pulsanti di opzione")](images/ch23fg17-large.png#lightbox "Immagini dei pulsanti di opzione")

L'esempio [**TraditionalRadios**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/TraditionalRadios) disegna i pulsanti di opzione tradizionali con un punto all'interno di un cerchio.

### <a name="fades-and-orientation"></a>Dissolvenze e orientamento

L'esempio finale, [**MultiColorSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23/MultiColorSliders) consente di passare da tre diverse viste di selezione dei colori utilizzando i pulsanti di opzione. Le tre visualizzazioni vengono [`FadeEnableAction`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/FadeEnableAction.cs) in entrata e in uscita utilizzando un oggetto nella libreria [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit)

Il programma risponde anche ai cambiamenti di [`GridOrientationBehavior`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/GridOrientationBehavior.cs) orientamento tra verticale e orizzontale utilizzando un nella libreria **Xamarin.FormsBook.Toolkit.**

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 23 testo completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch23-Apr2016.pdf)
- [Capitolo 23 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter23)
- [Utilizzo di trigger](~/xamarin-forms/app-fundamentals/triggers.md)
- [Comportamenti di Xamarin.Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md)
