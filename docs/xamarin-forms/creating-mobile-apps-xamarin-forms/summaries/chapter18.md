---
title: ''
description: ''
Creating Mobile Apps with Xamarin.Forms: Summary of Chapter 18. MVVM''
ms.prod: ''
ms.technology: ''
ms.assetid: ''
author: ''
ms.author: ''
ms.date: ''
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 1f180173a42654c54c5686e423ba20d9586271ea
ms.sourcegitcommit: 57bc714633364aeb34aba9803e88802bebf321ba
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84136708"
---
# <a name="summary-of-chapter-18-mvvm"></a>Riepilogo del capitolo 18. MVVM

[![Scaricare ](~/media/shared/download.png) l'esempio scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)

Uno dei modi migliori per progettare un'applicazione consiste nel separare l'interfaccia utente dal codice sottostante, che a volte viene definita *logica di business*. Esistono diverse tecniche, ma quella personalizzata per gli ambienti basati su XAML è nota come Model-View-ViewModel o MVVM.

## <a name="mvvm-interrelationships"></a>Interrelazioni MVVM

Un'applicazione MVVM è costituita da tre livelli:

- Il modello fornisce dati sottostanti, a volte tramite file o accessi Web
- La visualizzazione è l'interfaccia utente o il livello di presentazione, in genere implementato in XAML
- Il ViewModel connette il modello e la visualizzazione

Il modello ignora il ViewModel e il ViewModel ignora la visualizzazione. Questi tre livelli in genere si connettono tra loro usando i meccanismi seguenti:

![Visualizza, ViewModel e visualizza](images/ch18fg03.png "MVVM")

In molti programmi più piccoli (e anche quelli più grandi), spesso il modello è assente o la relativa funzionalità è integrata nell'elemento ViewModel.

## <a name="viewmodels-and-data-binding"></a>ViewModel e data binding

Per coinvolgere i data binding, un ViewModel deve essere in grado di notificare la visualizzazione quando una proprietà dell'elemento ViewModel è cambiata. Il ViewModel esegue questa operazione implementando l' [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) interfaccia nello `System.ComponentModel` spazio dei nomi. Questo fa parte di .NET anziché di Xamarin.Forms . (In genere i ViewModel tentano di mantenere l'indipendenza della piattaforma).

L' `INotifyPropertyChanged` interfaccia dichiara un singolo evento denominato [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) che indica la proprietà che è stata modificata.

### <a name="a-viewmodel-clock"></a>Clock ViewModel

[`DateTimeViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DateTimeViewModel.cs)Nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) viene definita una proprietà di tipo `DateTime` che cambia in base a un timer. La classe implementa `INotifyPropertyChanged` e genera l' `PropertyChanged` evento ogni volta che la `DateTime` proprietà viene modificata.

L'esempio [**MvvmClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/MvvmClock) crea un'istanza di questo ViewModel e usa le associazioni dati per il ViewModel per visualizzare le informazioni aggiornate sulla data e l'ora.

### <a name="interactive-properties-in-a-viewmodel"></a>Proprietà interattive in un elemento ViewModel

Le proprietà in un elemento ViewModel possono essere più interattive, come dimostrato dalla [`SimpleMultiplierViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/SimpleMultiplier/SimpleMultiplier/SimpleMultiplier/SimpleMultiplierViewModel.cs) classe, che fa parte dell'esempio [**SimpleMultiplier**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/SimpleMultiplier) . Le associazioni dati forniscono valori multiplicand e moltiplicatore da due `Slider` elementi e visualizzano il prodotto con un `Label` . Tuttavia, è possibile apportare modifiche estese a questa interfaccia utente in XAML senza apportare modifiche al file ViewModel o code-behind.

### <a name="a-color-viewmodel"></a>ViewModel di colore

[`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs)Nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) integra i modelli di colore RGB e HSL. Viene illustrato nell'esempio [**HslSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/HslSliders) :

[![Schermata tripla di TK](images/ch18fg08-small.png "Modello di colore HSL")](images/ch18fg08-large.png#lightbox "Modello di colore HSL")

### <a name="streamlining-the-viewmodel"></a>Ottimizzazione dell'elemento ViewModel

Il codice nei ViewModel può essere semplificato definendo un `OnPropertyChanged` Metodo usando l' [`CallerMemberName`](xref:System.Runtime.CompilerServices.CallerMemberNameAttribute) attributo, che ottiene automaticamente il nome della proprietà chiamante. La [`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ViewModelBase.cs) classe nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) esegue questa operazione e fornisce una classe base per i ViewModel.

## <a name="the-command-interface"></a>Interfaccia del comando

MVVM funziona con le associazioni dati e le associazioni dati funzionano con le proprietà, quindi MVVM sembra essere insufficiente quando si tratta di gestire un `Clicked` evento di un oggetto `Button` o `Tapped` di un evento di un oggetto `TapGestureRecognizer` . Per consentire ai ViewModel di gestire tali eventi, Xamarin.Forms supporta l' *interfaccia del comando*.

L'interfaccia del comando si manifesta in `Button` con due proprietà pubbliche:

- [`Command`](xref:Xamarin.Forms.Button.Command)di tipo [`ICommand`](xref:System.Windows.Input.ICommand) (definito nello `System.Windows.Input` spazio dei nomi)
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter)di tipo`Object`

Per supportare l'interfaccia del comando, un ViewModel deve definire una proprietà di tipo `ICommand` che è quindi associata ai dati `Command` della proprietà dell'oggetto `Button` . L' `ICommand` interfaccia dichiara due metodi e un evento:

- Un [`Execute`](xref:System.Windows.Input.ICommand.Execute(System.Object)) metodo con un argomento di tipo`object`
- Un [`CanExecute`](xref:System.Windows.Input.ICommand.CanExecute(System.Object)) metodo con un argomento di tipo `object` che restituisce`bool`
- Un [`CanExecuteChanged`](xref:System.Windows.Input.ICommand.CanExecuteChanged) evento

Internamente, un ViewModel imposta ogni proprietà di tipo `ICommand` su un'istanza di una classe che implementa l' `ICommand` interfaccia. Tramite il data binding, `Button` inizialmente chiama il `CanExecute` metodo e si disabilita se il metodo restituisce `false` . Viene inoltre impostato un gestore per l' `CanExecuteChanged` evento e `CanExecute` viene chiamato ogni volta che viene generato l'evento. Se `Button` è abilitato, chiama il `Execute` Metodo ogni volta che `Button` si fa clic su.

Potrebbero essere presenti alcuni ViewModel che Xamarin.Forms potrebbero già essere supportati dall'interfaccia di comando. Per i nuovi ViewModel destinati a essere usati solo con Xamarin.Forms , Xamarin.Forms fornisce una [`Command`](xref:Xamarin.Forms.Command) classe e una [`Command<T>`](xref:Xamarin.Forms.Command`1) classe che implementano l' `ICommand` interfaccia. Il tipo generico è il tipo di argomento per i `Execute` metodi e `CanExecute` .

### <a name="simple-method-executions"></a>Esecuzioni di metodi semplici

L'esempio [**PowersOfThree**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/PowersOfThree) illustra come usare l'interfaccia del comando in un elemento ViewModel. La [`PowersViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/PowersOfThree/PowersOfThree/PowersOfThree/PowersViewModel.cs) classe definisce due proprietà di tipo `ICommand` e definisce anche due proprietà private che passano al [ `Command` Costruttore](xref:Xamarin.Forms.Command.%23ctor(System.Action))più semplice. Il programma contiene associazioni dati da questo ViewModel alle `Command` proprietà di due `Button` elementi.

Gli `Button` elementi possono essere facilmente sostituiti con `TapGestureRecognizer` oggetti in XAML senza modifiche al codice.

### <a name="a-calculator-almost"></a>Una calcolatrice, quasi

L'esempio [**AddingMachine**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/AddingMachine) usa i `Execute` `CanExecute` metodi e di `ICommand` . Usa una [`AdderViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) classe nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) . Il ViewModel contiene sei proprietà di tipo `ICommand` . Questi vengono inizializzati dal [ `Command` Costruttore](xref:Xamarin.Forms.Command.%23ctor(System.Action)) e dal [ `Command` Costruttore](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean})) di `Command` e [ `Command<T>` ](https://docs.microsoft.com/dotnet/api/xamarin.forms.command.-ctor?view=xamarin-forms#Xamarin_Forms_Command__ctor_System_Action_System_Object__System_Func_System_Object_System_Boolean__) dal costruttore di `Command<T>` . I tasti numerici del computer di aggiunta sono tutti associati alla proprietà inizializzata con `Command<T>` e un `string` argomento a `Execute` e `CanExecute` identifica la chiave specifica.

## <a name="viewmodels-and-the-application-lifecycle"></a>ViewModel e ciclo di vita dell'applicazione

Il `AdderViewModel` usato nell'esempio **AddingMachine** definisce anche due metodi denominati `SaveState` e `RestoreState` . Questi metodi vengono chiamati dall'applicazione quando entra in stato di sospensione e quando viene riavviato.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 18 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf)
- [Capitolo 18 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)
- [Modelli di applicazioni aziendali con Xamarin.Forms eBook](~/xamarin-forms/enterprise-application-patterns/index.md)
