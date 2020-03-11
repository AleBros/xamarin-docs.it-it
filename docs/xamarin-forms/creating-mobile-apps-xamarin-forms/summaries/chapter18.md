---
title: Riepilogo del capitolo 18. MVVM
description: 'Creazione di App per dispositivi mobili con xamarin. Forms: riepilogo del capitolo 18. MVVM'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 6A774510-7709-4F60-8EF5-29D478176F8F
author: davidbritch
ms.author: dabritch
ms.date: 11/07/2017
ms.openlocfilehash: 32c16409f30d6b6d502b7cc074eafb182898594a
ms.sourcegitcommit: 9ee02a2c091ccb4a728944c1854312ebd51ca05b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2020
ms.locfileid: "70771066"
---
# <a name="summary-of-chapter-18-mvvm"></a>Riepilogo del capitolo 18. MVVM

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)

Uno dei modi migliori per progettare un'applicazione consiste nel separare l'interfaccia utente dal codice sottostante, che a volte viene definita *logica di business*. Esistono diverse tecniche, ma quello che è progettato appositamente per gli ambienti basati su XAML è noto come Model-View-ViewModel o MVVM.

## <a name="mvvm-interrelationships"></a>Relazioni reciproche MVVM

Un'applicazione MVVM composto da tre livelli:

- Il modello fornisce i dati sottostanti, in alcuni casi tramite file o web accede a
- La vista è il livello presentazione o interfaccia utente, in genere implementato in XAML
- L'elemento ViewModel si connette il modello e la visualizzazione

Il modello è che non riconoscono la di ViewModel e l'elemento ViewModel è che non riconoscono la della visualizzazione. Questi tre livelli in genere connettono tra loro usando i meccanismi seguenti:

![Visualizza, ViewModel e visualizza](images/ch18fg03.png "MVVM")

In molti programmi di dimensioni ridotte e quelli ancora più grandi, spesso il modello è assente o la relativa funzionalità è integrato nel ViewModel.

## <a name="viewmodels-and-data-binding"></a>ViewModel e il data binding

Per contattare i data binding, un elemento ViewModel deve essere in grado di avvisare la vista quando viene modificata una proprietà dell'elemento ViewModel. Il ViewModel esegue questa operazione implementando l'interfaccia [`INotifyPropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) nello spazio dei nomi `System.ComponentModel`. Ciò fa parte di .NET anziché a xamarin. Forms. (In genere ViewModel tenta di mantenere l'indipendenza dalla piattaforma.)

L'interfaccia `INotifyPropertyChanged` dichiara un singolo evento denominato [`PropertyChanged`](xref:System.ComponentModel.INotifyPropertyChanged) che indica la proprietà che è stata modificata.

### <a name="a-viewmodel-clock"></a>Un orologio ViewModel

Il [`DateTimeViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DateTimeViewModel.cs) nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) definisce una proprietà di tipo `DateTime` che viene modificata in base a un timer. La classe implementa `INotifyPropertyChanged` e genera l'evento `PropertyChanged` ogni volta che viene modificata la proprietà `DateTime`.

L'esempio [**MvvmClock**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/MvvmClock) crea un'istanza di questo ViewModel e usa le associazioni dati per il ViewModel per visualizzare le informazioni aggiornate sulla data e l'ora.

### <a name="interactive-properties-in-a-viewmodel"></a>Proprietà interattiva in un elemento ViewModel

Le proprietà in un elemento ViewModel possono essere più interattive, come dimostrato dalla classe [`SimpleMultiplierViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/SimpleMultiplier/SimpleMultiplier/SimpleMultiplier/SimpleMultiplierViewModel.cs) , che fa parte dell'esempio [**SimpleMultiplier**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/SimpleMultiplier) . Le associazioni dati forniscono valori multiplicand e moltiplicatore da due elementi `Slider` e visualizzano il prodotto con una `Label`. Tuttavia, è possibile apportare modifiche estese all'interfaccia utente in XAML senza modifiche conseguenti ViewModel o il file code-behind.

### <a name="a-color-viewmodel"></a>Un elemento ViewModel colore

Il [`ColorViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) integra i modelli di colore RGB e HSL. Viene illustrato nell'esempio [**HslSliders**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/HslSliders) :

[![Schermata tripla di TK](images/ch18fg08-small.png "Modello di colore HSL")](images/ch18fg08-large.png#lightbox "Modello di colore HSL")

### <a name="streamlining-the-viewmodel"></a>Semplificando l'elemento ViewModel

Il codice nei ViewModel può essere semplificato definendo un metodo di `OnPropertyChanged` usando l'attributo [`CallerMemberName`](xref:System.Runtime.CompilerServices.CallerMemberNameAttribute) , che ottiene automaticamente il nome della proprietà chiamante. La classe [`ViewModelBase`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ViewModelBase.cs) nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) esegue questa operazione e fornisce una classe base per i ViewModel.

## <a name="the-command-interface"></a>L'interfaccia di comando

MVVM funziona con le associazioni dati e le associazioni dati funzionano con le proprietà, quindi MVVM sembra essere insufficiente quando si tratta di gestire un evento `Clicked` di un `Button` o di un evento `Tapped` di una `TapGestureRecognizer`. Per consentire ai ViewModel di gestire tali eventi, Novell. Forms supporta l' *interfaccia di comando*.

L'interfaccia del comando si manifesta nel `Button` con due proprietà pubbliche:

- [`Command`](xref:Xamarin.Forms.Button.Command) di tipo [`ICommand`](xref:System.Windows.Input.ICommand) (definito nello spazio dei nomi `System.Windows.Input`)
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) di tipo `Object`

Per supportare l'interfaccia del comando, un elemento ViewModel deve definire una proprietà di tipo `ICommand` che è quindi associata ai dati della proprietà `Command` dell'`Button`. L'interfaccia `ICommand` dichiara due metodi e un evento:

- Metodo [`Execute`](xref:System.Windows.Input.ICommand.Execute(System.Object)) con un argomento di tipo `object`
- Metodo [`CanExecute`](xref:System.Windows.Input.ICommand.CanExecute(System.Object)) con un argomento di tipo `object` che restituisce `bool`
- Evento [`CanExecuteChanged`](xref:System.Windows.Input.ICommand.CanExecuteChanged)

Internamente, un ViewModel imposta ogni proprietà di tipo `ICommand` a un'istanza di una classe che implementa l'interfaccia `ICommand`. Tramite il data binding, il `Button` chiama inizialmente il metodo `CanExecute` e si disabilita se il metodo restituisce `false`. Viene inoltre impostato un gestore per l'evento `CanExecuteChanged` e viene chiamato `CanExecute` ogni volta che viene generato l'evento. Se la `Button` è abilitata, chiama il metodo `Execute` ogni volta che viene fatto clic sul `Button`.

È possibile avere alcune ViewModels risalenti a xamarin. Forms e queste potrebbero supportare già l'interfaccia di comando. Per i nuovi ViewModel destinati all'uso solo con Novell. Forms, Novell. Forms fornisce una classe [`Command`](xref:Xamarin.Forms.Command) e una classe [`Command<T>`](xref:Xamarin.Forms.Command`1) che implementano l'interfaccia `ICommand`. Il tipo generico è il tipo di argomento per i metodi `Execute` e `CanExecute`.

### <a name="simple-method-executions"></a>Esecuzioni di metodo semplice

L'esempio [**PowersOfThree**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/PowersOfThree) illustra come usare l'interfaccia del comando in un elemento ViewModel. La classe [`PowersViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/PowersOfThree/PowersOfThree/PowersOfThree/PowersViewModel.cs) definisce due proprietà di tipo `ICommand` e definisce anche due proprietà private che passano al [costruttore di`Command`](xref:Xamarin.Forms.Command.%23ctor(System.Action))più semplice. Il programma contiene associazioni dati da questo ViewModel alle proprietà `Command` di due elementi di `Button`.

Gli elementi `Button` possono essere facilmente sostituiti con `TapGestureRecognizer` oggetti in XAML senza modifiche al codice.

### <a name="a-calculator-almost"></a>Una calcolatrice, quasi

Nell'esempio [**AddingMachine**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/AddingMachine) vengono usati i metodi `Execute` e `CanExecute` di `ICommand`. Usa una classe [`AdderViewModel`](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) nella libreria [**Novell. FormsBook. Toolkit**](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) . Il ViewModel contiene sei proprietà di tipo `ICommand`. Questi vengono inizializzati dal [costruttore`Command`](xref:Xamarin.Forms.Command.%23ctor(System.Action)) e [`Command` Costruttore](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean})) di `Command` e dal [costruttore di`Command<T>`](https://docs.microsoft.com/dotnet/api/xamarin.forms.command.-ctor?view=xamarin-forms#Xamarin_Forms_Command__ctor_System_Action_System_Object__System_Func_System_Object_System_Boolean__) di `Command<T>`. I tasti numerici del computer di aggiunta sono tutti associati alla proprietà inizializzata con `Command<T>`e un argomento `string` per `Execute` e `CanExecute` identifica la chiave specifica.

## <a name="viewmodels-and-the-application-lifecycle"></a>ViewModel e il ciclo di vita dell'applicazione

Il `AdderViewModel` usato nell'esempio **AddingMachine** definisce anche due metodi denominati `SaveState` e `RestoreState`. Questi metodi vengono chiamati dall'applicazione quando passa alla modalità sospensione e al momento dell'avvio nuovamente.

## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 18 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf)
- [Capitolo 18 esempi](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)
- [Modelli di applicazioni aziendali con l'eBook Novell. Forms](~/xamarin-forms/enterprise-application-patterns/index.md)
