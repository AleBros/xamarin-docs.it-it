---
title: Riepilogo del capitolo 18. MVVM
description: 'Creazione di app per dispositivi mobili con Xamarin.Forms: Riepilogo del capitolo 18. MVVM'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 6A774510-7709-4F60-8EF5-29D478176F8F
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 32c16409f30d6b6d502b7cc074eafb182898594a
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70771066"
---
# <a name="summary-of-chapter-18-mvvm"></a>Riepilogo del capitolo 18. MVVM

[![Scaricare l'esempio](~/media/shared/download.png) scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)

Uno dei modi migliori per progettare un'applicazione consiste nel separare l'interfaccia utente dal codice sottostante, che talvolta viene denominato il *logica di business*. Esistono diverse tecniche, ma quello che è progettato appositamente per gli ambienti basati su XAML è noto come Model-View-ViewModel o MVVM.

## <a name="mvvm-interrelationships"></a>Relazioni reciproche MVVM

Un'applicazione MVVM composto da tre livelli:

- Il modello fornisce i dati sottostanti, in alcuni casi tramite file o web accede a
- La vista è il livello presentazione o interfaccia utente, in genere implementato in XAML
- L'elemento ViewModel si connette il modello e la visualizzazione

Il modello è che non riconoscono la di ViewModel e l'elemento ViewModel è che non riconoscono la della visualizzazione. Questi tre livelli in genere connettono tra loro usando i meccanismi seguenti:

![View e ViewModel visualizzazione](images/ch18fg03.png "MVVM")

In molti programmi di dimensioni ridotte e quelli ancora più grandi, spesso il modello è assente o la relativa funzionalità è integrato nel ViewModel.

## <a name="viewmodels-and-data-binding"></a>ViewModel e il data binding

Per contattare i data binding, un elemento ViewModel deve essere in grado di avvisare la vista quando viene modificata una proprietà dell'elemento ViewModel. L'elemento ViewModel viene eseguita implementando il [ `INotifyPropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged) interfaccia nel `System.ComponentModel` dello spazio dei nomi. Ciò fa parte di .NET anziché a Xamarin.Forms. (In genere ViewModel tenta di mantenere l'indipendenza dalla piattaforma.)

Il `INotifyPropertyChanged` interfaccia dichiara un singolo evento denominato [ `PropertyChanged` ](xref:System.ComponentModel.INotifyPropertyChanged) che indica la proprietà che è stato modificato.

### <a name="a-viewmodel-clock"></a>Un orologio ViewModel

Il [ `DateTimeViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DateTimeViewModel.cs) nel [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) libreria definisce una proprietà di tipo `DateTime` che cambia in base un timer. La classe implementa `INotifyPropertyChanged` e viene attivato il `PropertyChanged` evento ogni volta che il `DateTime` le modifiche alle proprietà.

Il [ **MvvmClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/MvvmClock) esempio crea un'istanza di tale elemento ViewModel e utilizza le associazioni dati per l'elemento ViewModel per visualizzare informazioni sull'ora e data di aggiornamento.

### <a name="interactive-properties-in-a-viewmodel"></a>Proprietà interattiva in un elemento ViewModel

Proprietà nel ViewModel può essere più interattiva, come dimostrato dal [ `SimpleMultiplierViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/SimpleMultiplier/SimpleMultiplier/SimpleMultiplier/SimpleMultiplierViewModel.cs) (classe), che fa parte del [ **SimpleMultiplier** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/SimpleMultiplier) esempio. I data binding forniti valori multiplicand e moltiplicatore da due `Slider` elementi e visualizzare il prodotto con una `Label`. Tuttavia, è possibile apportare modifiche estese all'interfaccia utente in XAML senza modifiche conseguenti ViewModel o il file code-behind.

### <a name="a-color-viewmodel"></a>Un elemento ViewModel colore

Il [ `ColorViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) nel [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) libreria integra i modelli di colore RGB e HSL. È dimostrata nel [ **HslSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/HslSliders) esempio:

[![Schermata triplo della TK](images/ch18fg08-small.png "modello di colori HSL")](images/ch18fg08-large.png#lightbox "modello colori HSL")

### <a name="streamlining-the-viewmodel"></a>Semplificando l'elemento ViewModel

Il codice nel ViewModel può essere semplificato definendo un `OnPropertyChanged` metodo usando il [ `CallerMemberName` ](xref:System.Runtime.CompilerServices.CallerMemberNameAttribute) attributo che si ottiene automaticamente il nome della proprietà chiamata. Il [ `ViewModelBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ViewModelBase.cs) classe la [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) libreria esegue questa operazione e fornisce una classe base per ViewModel.

## <a name="the-command-interface"></a>L'interfaccia di comando

MVVM funziona con le associazioni dati e le associazioni dati funzionano con le proprietà, in modo MVVM sembra essere mancanti se si desidera la gestione una `Clicked` eventi di un `Button` o una `Tapped` eventi di un `TapGestureRecognizer`. Per consentire a ViewModel gestire tali eventi, Xamarin.Forms supporta il *interfaccia di comando*.

L'interfaccia di comando si manifesta nel `Button` con due proprietà pubbliche:

- [`Command`](xref:Xamarin.Forms.Button.Command) typu [ `ICommand` ](xref:System.Windows.Input.ICommand) (definito nel `System.Windows.Input` dello spazio dei nomi)
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) di tipo `Object`

Per supportare l'interfaccia di comando, un ViewModel necessario definire una proprietà di tipo `ICommand` vale a dire quindi associato a dati per il `Command` proprietà del `Button`. Il `ICommand` interfaccia dichiara un evento e due metodi:

- Un' [ `Execute` ](xref:System.Windows.Input.ICommand.Execute(System.Object)) metodo con un argomento di tipo `object`
- Oggetto [ `CanExecute` ](xref:System.Windows.Input.ICommand.CanExecute(System.Object)) metodo con un argomento di tipo `object` che restituisce `bool`
- Oggetto [ `CanExecuteChanged` ](xref:System.Windows.Input.ICommand.CanExecuteChanged) evento

Internamente, un ViewModel imposta ogni proprietà di tipo `ICommand` a un'istanza di una classe che implementa il `ICommand` interfaccia. Tramite il data binding, il `Button` chiamati inizialmente il `CanExecute` metodo e se si disabilita automaticamente se il metodo restituisce `false`. Imposta anche un gestore per il `CanExecuteChanged` eventi e chiamate `CanExecute` ogni volta che viene generato tale evento. Se il `Button` è abilitata, viene chiamato il `Execute` metodo ogni volta che il `Button` si fa clic.

È possibile avere alcune ViewModels risalenti a Xamarin.Forms e queste potrebbero supportare già l'interfaccia di comando. Per nuovo ViewModel deve essere utilizzata solo con Xamarin.Forms, Xamarin.Forms fornisce una [ `Command` ](xref:Xamarin.Forms.Command) classe e un [ `Command<T>` ](xref:Xamarin.Forms.Command`1) classe che implementano il `ICommand` interfaccia. Il tipo generico è il tipo dell'argomento per il `Execute` e `CanExecute` metodi.

### <a name="simple-method-executions"></a>Esecuzioni di metodo semplice

Il [ **PowersOfThree** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/PowersOfThree) esempio viene illustrato come usare l'interfaccia di comando in un elemento ViewModel. Il [ `PowersViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/PowersOfThree/PowersOfThree/PowersOfThree/PowersViewModel.cs) classe definisce due proprietà di tipo `ICommand` e definisce anche due proprietà privata che viene passato alla più semplici [ `Command` costruttore](xref:Xamarin.Forms.Command.%23ctor(System.Action)). Il programma contiene le associazioni di dati da tale elemento ViewModel per il `Command` delle proprietà di due `Button` elementi.

Il `Button` gli elementi possono essere facilmente sostituiti con `TapGestureRecognizer` oggetti in XAML senza modifiche al codice.

### <a name="a-calculator-almost"></a>Una calcolatrice, quasi

Il [ **AddingMachine** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/AddingMachine) rende di esempio usano entrambi il `Execute` e `CanExecute` metodi `ICommand`. Usa un' [ `AdderViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) classe la [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) libreria. ViewModel contiene sei proprietà di tipo `ICommand`. Questi sono inizializzati dal [ `Command` costruttore](xref:Xamarin.Forms.Command.%23ctor(System.Action)) e [ `Command` costruttore](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean})) del `Command` e il [ `Command<T>` costruttore](https://docs.microsoft.com/dotnet/api/xamarin.forms.command.-ctor?view=xamarin-forms#Xamarin_Forms_Command__ctor_System_Action_System_Object__System_Func_System_Object_System_Boolean__) di `Command<T>`. I tasti numerici della calcolatrice sono associati alla proprietà che viene inizializzata con `Command<T>`e una `string` argomento `Execute` e `CanExecute` identifica la chiave specifica.

## <a name="viewmodels-and-the-application-lifecycle"></a>ViewModel e il ciclo di vita dell'applicazione

Il `AdderViewModel` usato nel **AddingMachine** esempio definisce anche due metodi denominati `SaveState` e `RestoreState`. Questi metodi vengono chiamati dall'applicazione quando passa alla modalità sospensione e al momento dell'avvio nuovamente.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 18 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf)
- [Capitolo 18 campioni](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)
- [Modelli di applicazioni aziendali tramite e-book di Xamarin.Forms](~/xamarin-forms/enterprise-application-patterns/index.md)
