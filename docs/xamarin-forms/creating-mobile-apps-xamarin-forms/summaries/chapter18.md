---
title: Riassunto del capitolo 18. MVVM
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: riepilogo del capitolo 18. MVVM'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 6A774510-7709-4F60-8EF5-29D478176F8F
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 32c16409f30d6b6d502b7cc074eafb182898594a
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "70771066"
---
# <a name="summary-of-chapter-18-mvvm"></a>Riassunto del capitolo 18. MVVM

[![Scarica](~/media/shared/download.png) l'esempio Scarica l'esempioDownload Sample Download the sample](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)

Uno dei modi migliori per progettare un'applicazione consiste nel separare l'interfaccia utente dal codice sottostante, talvolta denominato logica di *business.* Esistono diverse tecniche, ma quella personalizzata per gli ambienti basati su XAML è nota come Model-View-ViewModel o MVVM.

## <a name="mvvm-interrelationships"></a>Interrelazioni MVVM

Un'applicazione MVVM ha tre livelli:

- Il Modello fornisce i dati sottostanti, a volte tramite file o accessi Web
- La vista è l'interfaccia utente o il livello di presentazione, generalmente implementato in XAML
- Il ViewModel collega il modello e la vista

Il Model è ignorante del ViewModel e il ViewModel è ignorante della visualizzazione. Questi tre livelli in genere si connettono tra loro utilizzando i seguenti meccanismi:

![Visualizzazione, ViewModel e visualizzazione](images/ch18fg03.png "MVVM")

In molti programmi più piccoli (e anche più grandi), spesso il modello è assente o la sua funzionalità è integrata nel ViewModel.

## <a name="viewmodels-and-data-binding"></a>ViewModel e associazione datiViewModels and data binding

Per eseguire le associazioni dati, un ViewModel deve essere in grado di notificare il View quando una proprietà del ViewModel è stata modificata. Il ViewModel esegue questa [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) operazione `System.ComponentModel` implementando l'interfaccia nello spazio dei nomi. Questo è parte di .NET piuttosto che Xamarin.Forms. (In genere ViewModel tentano di mantenere l'indipendenza della piattaforma.)

L'interfaccia `INotifyPropertyChanged` dichiara un [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) singolo evento denominato che indica la proprietà modificata.

### <a name="a-viewmodel-clock"></a>Un orologio ViewModel

La [`DateTimeViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DateTimeViewModel.cs) libreria [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) definisce una `DateTime` proprietà di tipo che cambia in base a un timer. La classe `INotifyPropertyChanged` implementa e `PropertyChanged` genera `DateTime` l'evento ogni volta che la proprietà viene modificata.

L'esempio [**MvvmClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/MvvmClock) crea un'istanza di questo ViewModel e utilizza le associazioni dati per il ViewModel per visualizzare le informazioni aggiornate su data e ora.

### <a name="interactive-properties-in-a-viewmodel"></a>Proprietà interattive in un ViewModel

Proprietà in un ViewModel può essere più [`SimpleMultiplierViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/SimpleMultiplier/SimpleMultiplier/SimpleMultiplier/SimpleMultiplierViewModel.cs) interattivo, come dimostrato dalla classe, che fa parte del [**SimpleMultiplier**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/SimpleMultiplier) esempio. Le associazioni dati forniscono valori multiplieri `Slider` e multipli da `Label`due elementi e visualizzano il prodotto con un oggetto . Tuttavia, è possibile apportare modifiche estese a questa interfaccia utente in XAML senza modifiche seguenti al ViewModel o il file code-behind.

### <a name="a-color-viewmodel"></a>A Color ViewModel

La [`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) libreria [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) integra i modelli di colore RGB e HSL. È illustrato nell'esempio [**HslSliders:It**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/HslSliders) is demonstrated in the HslSliders sample:

[![Triplo screenshot di TK](images/ch18fg08-small.png "Modello di colore HSL")](images/ch18fg08-large.png#lightbox "Modello di colore HSL")

### <a name="streamlining-the-viewmodel"></a>Semplificazione del ViewModel

Il codice in ViewModels può essere `OnPropertyChanged` semplificato [`CallerMemberName`](xref:System.Runtime.CompilerServices.CallerMemberNameAttribute) definendo un metodo utilizzando l'attributo , che ottiene automaticamente il nome della proprietà chiamante. La [`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ViewModelBase.cs) classe nella libreria [**Xamarin.FormsBook.Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) esegue questa operazione e fornisce una classe base per ViewModels.

## <a name="the-command-interface"></a>L'interfaccia Command

MVVM funziona con le associazioni dati e le associazioni dati funzionano con le proprietà, pertanto `Clicked` MVVM sembra essere carente quando si tratta di gestire un evento di un `Button` evento o un `Tapped` evento di un `TapGestureRecognizer`oggetto . Per consentire a ViewModels di gestire tali eventi, Xamarin.Forms supporta *l'interfaccia*di comando .

L'interfaccia di comando `Button` si manifesta con due proprietà pubbliche:The command interface manifests itself in the with two public properties:

- [`Command`](xref:Xamarin.Forms.Button.Command)di [`ICommand`](xref:System.Windows.Input.ICommand) tipo (definito `System.Windows.Input` nello spazio dei nomi)
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter)di tipo`Object`

Per supportare l'interfaccia di comando, un `ICommand` ViewModel deve definire `Command` una `Button`proprietà di tipo che viene quindi associata a dati alla proprietà dell'oggetto . L'interfaccia `ICommand` dichiara due metodi e un evento:The interface declares two methods and one event:

- Un [`Execute`](xref:System.Windows.Input.ICommand.Execute(System.Object)) metodo con un argomento di tipo`object`
- Un [`CanExecute`](xref:System.Windows.Input.ICommand.CanExecute(System.Object)) metodo con un `object` argomento di tipo che restituisce`bool`
- Un [`CanExecuteChanged`](xref:System.Windows.Input.ICommand.CanExecuteChanged) evento

Internamente, un ViewModel imposta `ICommand` ogni proprietà di tipo a `ICommand` un'istanza di una classe che implementa l'interfaccia. Tramite l'associazione dati, l'oggetto `Button` chiama inizialmente il `CanExecute` metodo e si disabilita se il metodo restituisce `false`. Imposta inoltre un gestore per l'evento `CanExecuteChanged` e chiama `CanExecute` ogni volta che viene generato l'evento. Se `Button` l'oggetto è `Execute` abilitato, `Button` chiama il metodo ogni volta che si fa clic su di esso.

È possibile che alcuni ViewModel che precedano Xamarin.Forms e questi potrebbero già supportare l'interfaccia dei comandi. Per i nuovi ViewModels destinati a essere utilizzati solo con Xamarin.Forms, Xamarin.Forms fornisce una [`Command`](xref:Xamarin.Forms.Command) classe e una [`Command<T>`](xref:Xamarin.Forms.Command`1) classe che implementano l'interfaccia. `ICommand` Il tipo generico è il `Execute` tipo `CanExecute` dell'argomento per i metodi e .

### <a name="simple-method-executions"></a>Esecuzione semplice del metodo

Nell'esempio PowersOfThree viene illustrato come utilizzare l'interfaccia di comando in un ViewModel.The [**PowersOfThree**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/PowersOfThree) sample demonstrates how to use the command interface in a ViewModel. La [`PowersViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/PowersOfThree/PowersOfThree/PowersOfThree/PowersViewModel.cs) classe definisce due `ICommand` proprietà di tipo e definisce anche due proprietà private che passa al [ `Command` costruttore](xref:Xamarin.Forms.Command.%23ctor(System.Action))più semplice. Il programma contiene associazioni dati da `Command` questo ViewModel alle proprietà di due `Button` elementi.

Gli `Button` elementi possono essere `TapGestureRecognizer` facilmente sostituiti con oggetti in XAML senza modifiche al codice.

### <a name="a-calculator-almost"></a>Una calcolatrice, quasi

Nell'esempio [**AddingMachine**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/AddingMachine) vengono `Execute` utilizzati entrambi i metodi e `CanExecute` di `ICommand`. Utilizza una [`AdderViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) classe nella libreria [**Xamarin.FormsBook.Toolkit.**](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) Il ViewModel contiene sei `ICommand`proprietà di tipo . Questi vengono inizializzati dal [ `Command` costruttore](xref:Xamarin.Forms.Command.%23ctor(System.Action)) `Command` e `Command<T>` [ `Command` dal costruttore](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean})) di e dal [ `Command<T>` costruttore](https://docs.microsoft.com/dotnet/api/xamarin.forms.command.-ctor?view=xamarin-forms#Xamarin_Forms_Command__ctor_System_Action_System_Object__System_Func_System_Object_System_Boolean__) di . I tasti numerici del computer di aggiunta sono tutti `Command<T>`associati `string` alla `Execute` proprietà `CanExecute` inizializzata con , e un argomento e identifica la chiave specifica.

## <a name="viewmodels-and-the-application-lifecycle"></a>ViewModels e il ciclo di vita dell'applicazione

L'esempio `AdderViewModel` utilizzato nell'esempio **AddingMachine** `SaveState` definisce inoltre due metodi denominati e `RestoreState`. Questi metodi vengono chiamati dall'applicazione quando passa alla modalità di sospensione e quando viene riavviata.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 18 testo completo (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf)
- [Capitolo 18 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)
- [Modelli di applicazioni aziendali con eBook Xamarin.Forms](~/xamarin-forms/enterprise-application-patterns/index.md)
