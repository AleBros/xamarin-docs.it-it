---
title: Riepilogo del capitolo 18. MVVM
ms.topic: article
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: 6A774510-7709-4F60-8EF5-29D478176F8F
author: charlespetzold
ms.author: chape
ms.date: 11/07/2017
ms.openlocfilehash: cadab2432d0b6c29ead9cde1f4220bb64e1e1886
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="summary-of-chapter-18-mvvm"></a>Riepilogo del capitolo 18. MVVM

Uno dei modi migliori per progettare un'applicazione è separando l'interfaccia utente dal codice sottostante, che viene chiamato a volte il *logica di business*. Esistono diverse tecniche, ma quello progettato per ambienti basati su XAML è noto come Model-View-ViewModel o MVVM.

## <a name="mvvm-interrelationships"></a>Relazioni reciproche MVVM

Un'applicazione MVVM prevede tre livelli:

- Il modello fornisce i dati sottostanti, a volte tramite file o che accede a web
- La vista è il livello presentazione o di interfaccia utente, in genere implementato in XAML
- L'elemento ViewModel si connette il modello e la visualizzazione

Il modello è sostanze il ViewModel e l'elemento ViewModel è che non riconoscono della vista. Questi tre livelli in genere connettono tra loro tramite i seguenti meccanismi:

![Visualizzazione, ViewModel e visualizzazione](images/ch18fg03.png "MVVM")

In molti programmi di dimensioni ridotte e quelli ancora maggiori, spesso il modello è assente o la relativa funzionalità è integrata nel ViewModel.

## <a name="viewmodels-and-data-binding"></a>ViewModel e l'associazione dati

Per contattare data binding, un ViewModel deve essere in grado di notificare la vista quando viene modificata una proprietà del ViewModel. Il ViewModel viene eseguita implementando il [ `INotifyPropertyChanged` ](https://developer.xamarin.com/api/type/System.ComponentModel.INotifyPropertyChanged/) interfaccia nel `System.ComponentModel` dello spazio dei nomi. Questo fa parte di .NET anziché xamarin. Forms. (In genere ViewModel tenta di mantenere l'indipendenza dalla piattaforma.)

Il `INotifyPropertyChanged` interfaccia dichiara un singolo evento denominato [ `PropertyChanged` ](https://developer.xamarin.com/api/type/System.ComponentModel.INotifyPropertyChanged/) che indica la proprietà che è stato modificato.

### <a name="a-viewmodel-clock"></a>Un clock ViewModel

Il [ `DateTimeViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/DateTimeViewModel.cs) nel [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) libreria definisce una proprietà di tipo `DateTime` che le modifiche in base a un timer. La classe implementa `INotifyPropertyChanged` e genera un avviso di `PropertyChanged` evento ogni volta che il `DateTime` le modifiche alle proprietà.

Il [ **MvvmClock** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/MvvmClock) crea un'istanza di questa ViewModel di esempio e utilizza le associazioni dati per l'elemento ViewModel per visualizzare le informazioni di ora e data di aggiornamento.

### <a name="interactive-properties-in-a-viewmodel"></a>Proprietà interattiva in un elemento ViewModel

Le proprietà in un elemento ViewModel possono essere più interattive, come illustrato di [ `SimpleMultiplierViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/SimpleMultiplier/SimpleMultiplier/SimpleMultiplier/SimpleMultiplierViewModel.cs) (classe), che fa parte di [ **SimpleMultiplier** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/SimpleMultiplier) esempio. Le associazioni dati forniscono valori moltiplicando e moltiplicatore da due `Slider` elementi e visualizzare il prodotto con una `Label`. Tuttavia, è possibile apportare modifiche estese all'interfaccia utente in XAML senza conseguente modifiche per l'elemento ViewModel o il file code-behind.

### <a name="a-color-viewmodel"></a>ViewModel un colore

Il [ `ColorViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ColorViewModel.cs) nel [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) libreria integra i modelli di colore RGB e HSL. Viene illustrata la [ **HslSliders** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/HslSliders) esempio:

[![La schermata di TK](images/ch18fg08-small.png "colore HSL")](images/ch18fg08-large.png#lightbox "colore HSL")

### <a name="streamlining-the-viewmodel"></a>Semplificando il ViewModel

Il codice in ViewModel può essere semplificato definendo un `OnPropertyChanged` metodo usando il [ `CallerMemberName` ](https://developer.xamarin.com/api/type/System.Runtime.CompilerServices.CallerMemberNameAttribute/) attributo, che ottiene il nome della proprietà chiamata automaticamente. Il [ `ViewModelBase` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/ViewModelBase.cs) classe il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit) libreria esegue questa operazione e fornisce una classe base per ViewModel.

## <a name="the-command-interface"></a>L'interfaccia di comando

MVVM funziona con le associazioni dati e le associazioni dati funzionano con le proprietà, pertanto MVVM sembra essere mancanti per quanto riguarda la gestione un `Clicked` evento di un `Button` o `Tapped` evento di un `TapGestureRecognizer`. Per consentire ViewModel gestire tali eventi, xamarin. Forms supporta il *interfaccia comando*.

L'interfaccia di comando si manifesta nel `Button` con due proprietà pubbliche:

- [`Command`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.Command/) di tipo [ `ICommand` ](https://developer.xamarin.com/api/type/System.Windows.Input.ICommand/) (definito nel `System.Windows.Input` dello spazio dei nomi)
- [`CommandParameter`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.CommandParameter/) di tipo `Object`

Per supportare l'interfaccia di comando, un ViewModel deve definire una proprietà di tipo `ICommand` che è quindi associato a dati per il `Command` proprietà del `Button`. Il `ICommand` interfaccia dichiara un evento e i due metodi:

- Un [ `Execute` ](https://developer.xamarin.com/api/member/System.Windows.Input.ICommand.Execute/p/System.Object/) metodo con un argomento di tipo `object`
- Oggetto [ `CanExecute` ](https://developer.xamarin.com/api/member/System.Windows.Input.ICommand.CanExecute/p/System.Object/) metodo con un argomento di tipo `object` che restituisce `bool`
- Oggetto [ `CanExecuteChanged` ](https://developer.xamarin.com/api/event/System.Windows.Input.ICommand.CanExecuteChanged/) evento

Internamente, un ViewModel imposta ogni proprietà di tipo `ICommand` a un'istanza di una classe che implementa il `ICommand` interfaccia. Tramite l'associazione dati, il `Button` chiamati inizialmente il `CanExecute` (metodo) e se si disabilita automaticamente se il metodo restituisce `false`. Imposta inoltre un gestore per il `CanExecuteChanged` eventi e chiamate `CanExecute` ogni volta che viene generato l'evento. Se il `Button` è abilitata, viene chiamato il `Execute` metodo ogni volta che il `Button` si fa clic.

È possibile avere alcuni ViewModel risalenti a xamarin. Forms e questi già potrebbe supportare l'interfaccia di comando. Nuovo ViewModel destinato a essere utilizzato solo con xamarin. Forms, xamarin. Forms fornisce un [ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) classe e un [ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command%3CT%3E/) classi che implementano il `ICommand` interfaccia. Il tipo generico è il tipo dell'argomento di `Execute` e `CanExecute` metodi.

### <a name="simple-method-executions"></a>Esecuzioni di un metodo semplice

Il [ **PowersOfThree** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/PowersOfThree) esempio viene illustrato come utilizzare l'interfaccia di comando in un elemento ViewModel. Il [ `PowersViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Chapter18/PowersOfThree/PowersOfThree/PowersOfThree/PowersViewModel.cs) classe definisce due proprietà di tipo `ICommand` e definisce anche due proprietà privata che passa il più semplice [ `Command` costruttore](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command.Command/p/System.Action/). Il programma contiene le associazioni di dati da questo ViewModel per il `Command` le proprietà di due `Button` elementi.

Il `Button` elementi possono essere facilmente sostituiti con `TapGestureRecognizer` oggetti in XAML senza apportare modifiche al codice.

### <a name="a-calculator-almost"></a>Una calcolatrice, quasi

Il [ **AddingMachine** ](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18/AddingMachine) esempio rende usano entrambi il `Execute` e `CanExecute` metodi di `ICommand`. Usa un [ `AdderViewModel` ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) classe il [ **Xamarin.FormsBook.Toolkit** ](https://github.com/xamarin/xamarin-forms-book-samples/blob/master/Libraries/Xamarin.FormsBook.Toolkit/Xamarin.FormsBook.Toolkit/AdderViewModel.cs) libreria. L'elemento ViewModel contiene sei proprietà di tipo `ICommand`. Questi sono inizializzati dal [ `Command` costruttore](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command.Command/p/System.Action/) e [ `Command` costruttore](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command.Command/p/System.Action/System.Func%7BSystem.Boolean%7D/) di `Command` e [ `Command<T>` costruttore](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command%3CT%3E.Command%3CT%3E/p/System.Action%7BT%7D/System.Func%7BT,System.Boolean%7D/) di `Command<T>`. I tasti numerici della macchina aggiunta sono associati alla proprietà che viene inizializzata con `Command<T>`e un `string` argomento `Execute` e `CanExecute` identifica la chiave particolare.

## <a name="viewmodels-and-the-application-lifecycle"></a>ViewModel e il ciclo di vita dell'applicazione

Il `AdderViewModel` utilizzato nel **AddingMachine** esempio definisce anche due metodi denominati `SaveState` e `RestoreState`. Questi metodi vengono chiamati dall'applicazione quando passa alla modalità sospensione e al momento dell'avvio nuovamente.



## <a name="related-links"></a>Collegamenti correlati

- [Capitolo 18 full-text (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch18-Apr2016.pdf)
- [Esempi di capitolo 18](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter18)
