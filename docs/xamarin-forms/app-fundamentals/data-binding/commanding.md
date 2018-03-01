---
title: L'interfaccia di comando
description: "Implementare il `Command` proprietà con le associazioni dati"
ms.topic: article
ms.prod: xamarin
ms.assetid: 69922284-F398-45C3-B4CC-B8E29BB4C533
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 0bc039385a6b2077c3b5fa5114b35b586a14a150
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="the-command-interface"></a>L'interfaccia di comando

Nell'architettura Model-View-ViewModel (MVVM), vengono definiti i data binding tra le proprietà di ViewModel, che corrisponde in genere una classe che deriva da `INotifyPropertyChanged`e le proprietà nella vista, che corrisponde in genere il file XAML. In alcuni casi un'applicazione ha esigenze che vanno oltre le associazioni di proprietà richiedendo all'utente di avviare i comandi che influiscono su un elemento all'interno di ViewModel. Questi comandi sono in genere segnalati dal pulsante o un dito scelte e in genere vengono elaborati nel file code-behind in un gestore per il `Clicked` evento del `Button` o `Tapped` evento di un `TapGestureRecognizer`. 

L'esecuzione di comandi interfaccia fornisce un approccio alternativo all'implementazione di comandi che è molto più adatto per l'architettura MVVM. ViewModel stesso possono contenere comandi, ovvero i metodi che vengono eseguiti in risposta a una specifica attività nella visualizzazione, ad esempio un `Button` fare clic su. Tra questi comandi vengono definiti i data binding e `Button`.

Per consentire un'associazione dati tra un `Button` e ViewModel, il `Button` definisce due proprietà:

- [`Command`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.Command/) di tipo [`ICommand`](https://developer.xamarin.com/api/type/System.Windows.Input.ICommand/)
- [`CommandParameter`](https://developer.xamarin.com/api/property/Xamarin.Forms.Button.CommandParameter/) di tipo `Object`

Per utilizzare l'interfaccia di comando, si definisce un'associazione di dati che ha come destinazione il `Command` proprietà del `Button` in cui l'origine è una proprietà ViewModel di tipo `ICommand`. L'elemento ViewModel contiene codice associato che `ICommand` proprietà che viene eseguito quando si fa clic sul pulsante. È possibile impostare `CommandParameter` ai dati arbitrari per distinguere tra più pulsanti se sono tutti associati alla stessa `ICommand` proprietà il ViewModel.

Il `Command` e `CommandParameter` proprietà vengono definite anche dalle classi seguenti:

- [`MenuItem`](https://developer.xamarin.com/api/type/Xamarin.Forms.MenuItem/) e di conseguenza, [ `ToolbarItem` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ToolbarItem/), che deriva da `MenuItem`
- [`TextCell`](https://developer.xamarin.com/api/type/Xamarin.Forms.TextCell/) e di conseguenza, [ `ImageCell` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ImageCell/), che deriva da `TextCell`
- [`TapGestureRecognizer`](https://developer.xamarin.com/api/type/Xamarin.Forms.TapGestureRecognizer/)

[`SearchBar`](https://developer.xamarin.com/api/type/Xamarin.Forms.SearchBar/) definisce un [ `SearchCommand` ](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.SearchCommand/) proprietà di tipo `ICommand` e [ `SearchCommandParameter` ](https://developer.xamarin.com/api/property/Xamarin.Forms.SearchBar.SearchCommandParameter/) proprietà. Il [ `RefreshCommand` ](https://developer.xamarin.com/api/property/Xamarin.Forms.ListView.RefreshCommand/) proprietà di [ `ListView` ](https://developer.xamarin.com/api/type/Xamarin.Forms.ListView/) anche è di tipo `ICommand`. 

Tutti questi comandi possono essere gestiti all'interno di un elemento ViewModel in modo che non dipende dall'oggetto dell'interfaccia utente specifico nella visualizzazione.

## <a name="the-icommand-interface"></a>L'interfaccia ICommand

Il [ `ICommand` ](https://developer.xamarin.com/api/type/System.Windows.Input.ICommand/) interfaccia non fa parte di xamarin. Forms. In cui è definito invece di [ `System.Windows.Input` ](https://developer.xamarin.com/api/namespace/System.Windows.Input/) dello spazio dei nomi ed è costituito da un evento e i due metodi:

```csharp
public interface ICommand
{
    public void Execute (Object parameter);

    public bool CanExecute (Object parameter);

    public event EventHandler CanExecuteChanged;
}
```

Per utilizzare l'interfaccia di comando, il ViewModel contiene le proprietà di tipo `ICommand`:

```csharp
public ICommand MyCommand { private set; get; }
```

L'elemento ViewModel anche deve fare riferimento a una classe che implementa il `ICommand` interfaccia. Questa classe verrà descritta a breve. Nella visualizzazione, il `Command` proprietà di un `Button` è associato a tale proprietà: 

```xaml
<Button Text="Execute command"
        Command="{Binding MyCommand}" />
```

Quando l'utente preme il `Button`, `Button` chiamate il `Execute` metodo nel `ICommand` oggetto associato a relativo `Command` proprietà. Questa è la parte più semplice dell'interfaccia dei comandi.

Il `CanExecute` metodo è più complesso. Quando l'associazione viene innanzitutto definito nel `Command` proprietà del `Button`, e quando l'associazione di dati viene modificato in qualche modo, il `Button` chiamate il `CanExecute` metodo il `ICommand` oggetto. Se `CanExecute` restituisce `false`, quindi il `Button` disabilitato automaticamente. Ciò indica che il comando specifico è attualmente non disponibile o non valido.

Il `Button` collega anche un gestore nel `CanExecuteChanged` evento di `ICommand`. L'evento è generato all'interno di ViewModel. Quando viene generato l'evento, il `Button` chiamate `CanExecute` nuovamente. Il `Button` consente a se stessa se `CanExecute` restituisce `true` e si disabilita automaticamente se `CanExecute` restituisce `false`.

> [!IMPORTANT]
> Non utilizzare il `IsEnabled` proprietà `Button` se si utilizza l'interfaccia di comando.  

## <a name="the-command-class"></a>La classe di comando

Quando il ViewModel definisce una proprietà di tipo `ICommand`, l'elemento ViewModel deve inoltre contenere o fanno riferimento a una classe che implementa il `ICommand` interfaccia. Questa classe deve contengono o fanno riferimento le `Execute` e `CanExecute` metodi e attivare il `CanExecuteChanged` evento ogni volta che il `CanExecute` metodo può restituire un valore diverso.

È possibile scrivere manualmente tale classe oppure è possibile utilizzare una classe che qualcun altro ha scritto. Poiché `ICommand` fa parte di Microsoft Windows, è stato utilizzato per gli anni con applicazioni di Windows MVVM. Utilizzo di una classe di Windows che implementa `ICommand` consente di condividere il ViewModel tra le applicazioni di Windows e applicazioni di xamarin. Forms.

Se la condivisione ViewModel tra Windows e xamarin. Forms non rappresenta un problema, è quindi possibile utilizzare il [ `Command` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command/) o [ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command%3CT%3E/) classe inclusa in xamarin. Forms per implementare il `ICommand`interfaccia. Queste classi consentono di specificare i corpi del `Execute` e `CanExecute` metodi nei costruttori di classe. Utilizzare `Command<T>` quando si utilizza il `CommandParameter` proprietà per distinguere tra più visualizzazioni associata allo stesso `ICommand` , proprietà e la più semplice `Command` classe quando che non è un requisito.

## <a name="basic-commanding"></a>Comandi di base

Il **voce persona** nella pagina di [ **Data Binding Demo** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) programma illustra alcuni semplici comandi implementati in un ViewModel.

Il `PersonViewModel` definisce tre proprietà denominata `Name`, `Age`, e `Skills` che definiscono una persona. Questa classe *non* contenere `ICommand` proprietà:

```csharp
public class PersonViewModel : INotifyPropertyChanged
{
    string name;
    double age;
    string skills;

    public event PropertyChangedEventHandler PropertyChanged;

    public string Name
    {
        set { SetProperty(ref name, value); }
        get { return name; }
    }

    public double Age
    {
        set { SetProperty(ref age, value); }
        get { return age; }
    }

    public string Skills
    {
        set { SetProperty(ref skills, value); }
        get { return skills; }
    }

    public override string ToString()
    {
        return Name + ", age " + Age;
    }

    bool SetProperty<T>(ref T storage, T value, [CallerMemberName] string propertyName = null)
    {
        if (Object.Equals(storage, value))
            return false;

        storage = value;
        OnPropertyChanged(propertyName);
        return true;
    }

    protected void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }
}
```

Il `PersonCollectionViewModel` illustrato di seguito vengono creati nuovi oggetti di tipo `PersonViewModel` e consente all'utente di inserire i dati. A tale scopo, la classe definisce le proprietà `IsEditing` di tipo `bool` e `PersonEdit` di tipo `PersonViewModel`. Inoltre, la classe definisce tre proprietà di tipo `ICommand` e una proprietà denominata `Persons` di tipo `IList<PersonViewModel>`: 

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{
    PersonViewModel personEdit;
    bool isEditing;

    public event PropertyChangedEventHandler PropertyChanged;

    ···

    public bool IsEditing
    {
        private set { SetProperty(ref isEditing, value); }
        get { return isEditing; }
    }

    public PersonViewModel PersonEdit
    {
        set { SetProperty(ref personEdit, value); }
        get { return personEdit; }
    }

    public ICommand NewCommand { private set; get; }

    public ICommand SubmitCommand { private set; get; }

    public ICommand CancelCommand { private set; get; }

    public IList<PersonViewModel> Persons { get; } = new ObservableCollection<PersonViewModel>();

    bool SetProperty<T>(ref T storage, T value, [CallerMemberName] string propertyName = null)
    {
        if (Object.Equals(storage, value))
            return false;

        storage = value;
        OnPropertyChanged(propertyName);
        return true;
    }

    protected void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(propertyName));
    }
}
```

Questo elenco abbreviato non include il costruttore della classe, ovvero dove le tre proprietà di tipo `ICommand` sono definiti, che verrà visualizzata a breve. Si noti che le modifiche apportate alle tre proprietà di tipo `ICommand` e `Persons` proprietà non vengano `PropertyChanged` eventi generati. Queste proprietà sono tutti impostati quando viene creata la classe e non cambiano in seguito.

Prima di esaminare il costruttore del `PersonCollectionViewModel` classe, si esaminerà il file XAML per la **voce persona** programma. Contiene un `Grid` con relativo `BindingContext` impostata il `PersonCollectionViewModel`. Il `Grid` contiene un `Button` con il testo **New** con relativo `Command` proprietà associata al `NewCommand` proprietà, l'elemento ViewModel associato un modulo voce con le proprietà di `IsEditing` proprietà, come nonché le proprietà di `PersonViewModel`, e due pulsanti più associato al `SubmitCommand` e `CancelCommand` il ViewModel di proprietà. Finale `ListView` Visualizza la raccolta delle persone già immesso:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.PersonEntryPage"
             Title="Person Entry">
    <Grid Margin="10">
        <Grid.BindingContext>
            <local:PersonCollectionViewModel />
        </Grid.BindingContext>
        
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
            <RowDefinition Height="*" />
        </Grid.RowDefinitions>

        <!-- New Button -->
        <Button Text="New"
                Grid.Row="0"
                Command="{Binding NewCommand}"
                HorizontalOptions="Start" />

        <!-- Entry Form -->
        <Grid Grid.Row="1"
              IsEnabled="{Binding IsEditing}">
            
            <Grid BindingContext="{Binding PersonEdit}">
                <Grid.RowDefinitions>
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                    <RowDefinition Height="Auto" />
                </Grid.RowDefinitions>

                <Grid.ColumnDefinitions>
                    <ColumnDefinition Width="Auto" />
                    <ColumnDefinition Width="*" />
                </Grid.ColumnDefinitions>

                <Label Text="Name: " Grid.Row="0" Grid.Column="0" />
                <Entry Text="{Binding Name}" 
                       Grid.Row="0" Grid.Column="1" />

                <Label Text="Age: " Grid.Row="1" Grid.Column="0" />
                <StackLayout Orientation="Horizontal" 
                             Grid.Row="1" Grid.Column="1">
                    <Stepper Value="{Binding Age}"
                             Maximum="100" />
                    <Label Text="{Binding Age, StringFormat='{0} years old'}"
                           VerticalOptions="Center" />
                </StackLayout>

                <Label Text="Skills: " Grid.Row="2" Grid.Column="0" />
                <Entry Text="{Binding Skills}"
                       Grid.Row="2" Grid.Column="1" />

            </Grid>
        </Grid>

        <!-- Submit and Cancel Buttons -->
        <Grid Grid.Row="2">
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="*" />
                <ColumnDefinition Width="*" />
            </Grid.ColumnDefinitions>

            <Button Text="Submit"
                    Grid.Column="0"
                    Command="{Binding SubmitCommand}"
                    VerticalOptions="CenterAndExpand" />

            <Button Text="Cancel"
                    Grid.Column="1"
                    Command="{Binding CancelCommand}"
                    VerticalOptions="CenterAndExpand" />
        </Grid>

        <!-- List of Persons -->
        <ListView Grid.Row="3"
                  ItemsSource="{Binding Persons}" />
    </Grid>
</ContentPage>
```

Ecco come funziona: l'utente preme prima il **New** pulsante. Ciò consente al form di immissione ma disabilita il **New** pulsante. L'utente immette quindi un nome, età e competenze. In qualsiasi momento durante la modifica, l'utente è possibile premere il **Annulla** pulsante ricominciare. Solo quando sia stato immesso un nome e una durata valida è la **Invia** pulsante abilitato. Premendo questo **Invia** pulsante trasferisce l'utente alla raccolta visualizzata per il `ListView`. Dopo che il **Annulla** o **Invia** pulsante, viene cancellato al form di immissione e la **New** pulsante viene abilitato nuovamente.

La schermata di iOS a sinistra viene mostrato il layout prima dell'immissione di una data valida. Di Android e UWP schermate Mostra il **Invia** pulsante abilitato dopo aver impostata un'età:

[![Voce di persona](commanding-images/personentry-small.png "voce persona")](commanding-images/personentry-large.png "voce contatto")

Il programma non dispone di una qualsiasi struttura per la modifica delle voci esistenti e le voci non vengono salvate quando si esce dalla pagina.

Tutta la logica per il **New**, **Invia**, e **Annulla** pulsanti viene gestito in `PersonCollectionViewModel` tramite definizioni del `NewCommand`, `SubmitCommand`, e `CancelCommand` proprietà. Il costruttore del `PersonCollectionViewModel` imposta queste tre proprietà per gli oggetti di tipo `Command`.  

A [costruttore](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command.Command/p/System.Action/System.Func%7BSystem.Boolean%7D/) del `Command` classe consente di passare argomenti di tipo `Action` e `Func<bool>` corrispondente per il `Execute` e `CanExecute` metodi. È più semplice definire queste funzioni e le azioni come direttamente le funzioni lambda nel `Command` costruttore. Qui è la definizione di `Command` dell'oggetto per il `NewCommand` proprietà:

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{

    ···

    public PersonCollectionViewModel()
    {
        NewCommand = new Command(
            execute: () =>
            {
                PersonEdit = new PersonViewModel();
                PersonEdit.PropertyChanged += OnPersonEditPropertyChanged;
                IsEditing = true;
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return !IsEditing;
            });

        ···

    }

    void OnPersonEditPropertyChanged(object sender, PropertyChangedEventArgs args)
    {
        (SubmitCommand as Command).ChangeCanExecute();
    }
    
    void RefreshCanExecutes()
    {
        (NewCommand as Command).ChangeCanExecute();
        (SubmitCommand as Command).ChangeCanExecute();
        (CancelCommand as Command).ChangeCanExecute();
    }

    ···

}
```

Quando l'utente fa clic il **New** pulsante, il `execute` funzione passato al `Command` costruttore viene eseguito. Verrà creato un nuovo `PersonViewModel` oggetto, imposta un gestore per tale oggetto `PropertyChanged` evento, imposta `IsEditing` a `true`e chiama il `RefreshCanExecutes` metodo definito dopo il costruttore.

Oltre all'implementazione di `ICommand` interfaccia, il `Command` classe definisce inoltre un metodo denominato `ChangeCanExecute`. Deve chiamare il ViewModel `ChangeCanExecute` per un `ICommand` proprietà ogni volta che si esegue un'operazione che potrebbe modificare il valore restituito del `CanExecute` metodo. Una chiamata a `ChangeCanExecute` provoca il `Command` classe attivano il `CanExecuteChanged` metodo. Il `Button` è associato un gestore per l'evento e risponde chiamando `CanExecute` nuovamente e quindi l'abilitazione di se stesso in base al valore restituito del metodo.

Quando il `execute` metodo `NewCommand` chiamate `RefreshCanExecutes`, il `NewCommand` proprietà ottiene una chiamata a `ChangeCanExecute`e `Button` chiamate il `canExecute` metodo, che ora restituisce `false` perché il `IsEditing`proprietà è ora `true`.

Il `PropertyChanged` gestore per il nuovo `PersonViewModel` chiama il `ChangeCanExecute` metodo `SubmitCommand`. Ecco l'implementazione di tale proprietà comando:


```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{

    ···

    public PersonCollectionViewModel()
    {

        ···

        SubmitCommand = new Command(
            execute: () =>
            {
                Persons.Add(PersonEdit);
                PersonEdit.PropertyChanged -= OnPersonEditPropertyChanged;
                PersonEdit = null;
                IsEditing = false;
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return PersonEdit != null && 
                       PersonEdit.Name != null && 
                       PersonEdit.Name.Length > 1 && 
                       PersonEdit.Age > 0;
            });

        ···
    }

    ···

}
```

Il `canExecute` funzione per `SubmitCommand` viene chiamato ogni volta che è disponibile una proprietà modificata nel `PersonViewModel` dell'oggetto da modificare. Restituisce `true` solo quando il `Name` proprietà è lungo, almeno un carattere e `Age` è maggiore di 0. In quel momento, il **Invia** pulsante viene attivato. 

Il `execute` funzione per **Invia** rimuove il gestore di modifica della proprietà dal `PersonViewModel`, aggiunge l'oggetto per il `Persons` insieme e restituisce tutti gli elementi per condizioni iniziali.

Il `execute` funzione per il **Annulla** pulsante esegue tutto il **Invia** pulsante fa execept aggiungere l'oggetto alla raccolta:

```csharp
public class PersonCollectionViewModel : INotifyPropertyChanged
{

    ···

    public PersonCollectionViewModel()
    {

        ···

        CancelCommand = new Command(
            execute: () =>
            {
                PersonEdit.PropertyChanged -= OnPersonEditPropertyChanged;
                PersonEdit = null;
                IsEditing = false;
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return IsEditing;
            }); 
    }

    ···

}
```

Il `canExecute` restituisce `true` in qualsiasi momento un `PersonViewModel` viene modificato.

Queste tecniche può essere adattate in scenari più complessi: una proprietà in `PersonCollectionViewModel` potrebbe essere associato al `SelectedItem` proprietà del `ListView` per la modifica di elementi esistenti e un **eliminare** pulsante è stato possibile aggiungere da eliminare tali elementi.

Non è necessario definire il `execute` e `canExecute` metodi come funzioni lambda. È possibile scriverli metodi privati come regolari il ViewModel e farvi riferimento nel `Command` costruttori. Tuttavia, questo approccio tendono a comportare una notevole quantità di metodi a cui viene fatto riferimento solo una volta il ViewModel.

## <a name="using-command-parameters"></a>Utilizzo di parametri di comando

Talvolta è utile per uno o più pulsanti o altri oggetti dell'interfaccia utente per condividere lo stesso `ICommand` proprietà il ViewModel. In questo caso, utilizzare il `CommandParameter` proprietà per distinguere tra i pulsanti. 

È possibile continuare a utilizzare il `Command` classe per questi condiviso `ICommand` proprietà. La classe definisce un [costruttore alternativo](https://developer.xamarin.com/api/constructor/Xamarin.Forms.Command.Command/p/System.Action%7BSystem.Object%7D/System.Func%7BSystem.Object,System.Boolean%7D/) che accetta `execute` e `canExecute` metodi con parametri di tipo `Object`. Questo è il modo in `CommandParameter` viene passato a questi metodi.

Tuttavia, quando si utilizza `CommandParameter`, è consigliabile utilizzare il metodo generico [ `Command<T>` ](https://developer.xamarin.com/api/type/Xamarin.Forms.Command%3CT%3E/) classe per specificare il tipo dell'oggetto impostato su `CommandParameter`. Il `execute` e `canExecute` dispongono di metodi che specificano parametri di tipo.

Il **tastiera decimale** pagina illustra questa tecnica viene mostrato come implementare un tastierino per l'immissione di numeri decimali. Il `BindingContext` per il `Grid` è un `DecimalKeypadViewModel`. Il `Entry` proprietà questo ViewModel è associata al `Text` proprietà di un `Label`. Tutti i `Button` gli oggetti sono associati ai vari comandi di ViewModel: `ClearCommand`, `BackspaceCommand`, e `DigitCommand`:

```xaml
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.DecimalKeypadPage"
             Title="Decimal Keyboard">

    <Grid WidthRequest="240"
          HeightRequest="480"
          ColumnSpacing="2"
          RowSpacing="2"
          HorizontalOptions="Center"
          VerticalOptions="Center">

        <Grid.BindingContext>
            <local:DecimalKeypadViewModel />
        </Grid.BindingContext>
        
        <Grid.Resources>
            <ResourceDictionary>
                <Style TargetType="Button">
                    <Setter Property="FontSize" Value="32" />
                    <Setter Property="BorderWidth" Value="1" />
                    <Setter Property="BorderColor" Value="Black" />
                </Style>
            </ResourceDictionary>
        </Grid.Resources>

        <Label Text="{Binding Entry}"
               Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="3"
               FontSize="32"
               LineBreakMode="HeadTruncation"
               VerticalTextAlignment="Center"
               HorizontalTextAlignment="End" />

        <Button Text="CLEAR"
                Grid.Row="1" Grid.Column="0" Grid.ColumnSpan="2"
                Command="{Binding ClearCommand}" />

        <Button Text="&#x21E6;"
                Grid.Row="1" Grid.Column="2" 
                Command="{Binding BackspaceCommand}" />

        <Button Text="7"
                Grid.Row="2" Grid.Column="0" 
                Command="{Binding DigitCommand}"
                CommandParameter="7" />

        <Button Text="8"
                Grid.Row="2" Grid.Column="1" 
                Command="{Binding DigitCommand}"
                CommandParameter="8" />
        
        <Button Text="9"
                Grid.Row="2" Grid.Column="2" 
                Command="{Binding DigitCommand}"
                CommandParameter="9" />

        <Button Text="4"
                Grid.Row="3" Grid.Column="0" 
                Command="{Binding DigitCommand}"
                CommandParameter="4" />

        <Button Text="5"
                Grid.Row="3" Grid.Column="1" 
                Command="{Binding DigitCommand}"
                CommandParameter="5" />

        <Button Text="6"
                Grid.Row="3" Grid.Column="2" 
                Command="{Binding DigitCommand}"
                CommandParameter="6" />

        <Button Text="1"
                Grid.Row="4" Grid.Column="0" 
                Command="{Binding DigitCommand}"
                CommandParameter="1" />

        <Button Text="2"
                Grid.Row="4" Grid.Column="1" 
                Command="{Binding DigitCommand}"
                CommandParameter="2" />

        <Button Text="3"
                Grid.Row="4" Grid.Column="2" 
                Command="{Binding DigitCommand}"
                CommandParameter="3" />

        <Button Text="0"
                Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2"
                Command="{Binding DigitCommand}"
                CommandParameter="0" />

        <Button Text="&#x00B7;"
                Grid.Row="5" Grid.Column="2" 
                Command="{Binding DigitCommand}"
                CommandParameter="." />
    </Grid>
</ContentPage>
```

I 11 pulsanti per 10 cifre e la virgola decimale condividono un'associazione a `DigitCommand`. Il `CommandParameter` viene fatta distinzione tra questi pulsanti. Il valore impostato su `CommandParameter` corrisponde in genere al testo visualizzato dal pulsante tranne il separatore decimale, che, per maggiore chiarezza, viene visualizzato con un carattere di punto centrale.

Ecco il programma in azione:

[![Tastiera decimale](commanding-images/decimalkeyboard-small.png "tastiera decimale")](commanding-images/decimalkeyboard-large.png "tastiera decimale")

Si noti che il pulsante per il separatore decimale in tutte e tre le schermate è disabilitato perché il numero immesso contiene già un separatore decimale. 

Il `DecimalKeypadViewModel` definisce un `Entry` proprietà di tipo `string` (che è l'unica proprietà che attiva un `PropertyChanged` eventi) e tre le proprietà di tipo `ICommand`:

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{
    string entry = "0";

    public event PropertyChangedEventHandler PropertyChanged;

    ···

    public string Entry
    {
        private set
        {
            if (entry != value)
            {
                entry = value;
                PropertyChanged?.Invoke(this, new PropertyChangedEventArgs("Entry"));
            }
        }
        get
        {
            return entry;
        }
    }

    public ICommand ClearCommand { private set; get; }

    public ICommand BackspaceCommand { private set; get; }

    public ICommand DigitCommand { private set; get; }
}
```

Pulsante corrispondente per il `ClearCommand` è sempre abilitata e imposta semplicemente la voce "0":

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{

    ···

    public DecimalKeypadViewModel()
    {
        ClearCommand = new Command(
            execute: () =>
            {
                Entry = "0";
                RefreshCanExecutes();
            });

        ···
    
    }

    void RefreshCanExecutes()
    {
        ((Command)BackspaceCommand).ChangeCanExecute();
        ((Command)DigitCommand).ChangeCanExecute();
    }

    ···
    
}
```

Poiché il pulsante è sempre abilitato, non è necessario specificare un `canExecute` argomento in di `Command` costruttore.

La logica per l'immissione di numeri e un BACKSPACE è un po' complesso perché se non sono stati immessi cifre, il `Entry` proprietà corrisponde alla stringa "0". Se l'utente digita più zeri, la `Entry` contiene ancora uno zero. Se l'utente digita una qualsiasi altra cifra, tale cifra sostituisce lo zero. Tuttavia, se l'utente digita un separatore decimale prima di qualsiasi altra cifra, quindi `Entry` è la stringa "0".

Il **Backspace** pulsante è abilitato solo quando la lunghezza della voce è maggiore di 1, o se `Entry` non è uguale alla stringa "0":

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{

    ···

    public DecimalKeypadViewModel()
    {

        ···

        BackspaceCommand = new Command(
            execute: () =>
            {
                Entry = Entry.Substring(0, Entry.Length - 1);
                if (Entry == "")
                {
                    Entry = "0";
                }
                RefreshCanExecutes();
            },
            canExecute: () =>
            {
                return Entry.Length > 1 || Entry != "0";
            });

        ···

    }

    ···
    
}
```

La logica per il `execute` funzione per il **Backspace** pulsante assicura che il `Entry` è almeno una stringa "0".

Il `DigitCommand` proprietà è associata ai 11 pulsanti, con ognuno dei quali si identifica il `CommandParameter` proprietà. Il `DigitCommand` può essere impostata su un'istanza di normali `Command` classe, ma la più facile da utilizzare il `Command<T>` classe generica. Quando si utilizza l'interfaccia dei comandi con XAML, il `CommandParameter` le proprietà sono in genere stringhe e questo è il tipo di argomento generico. Il `execute` e `canExecute` quindi dispongono degli argomenti di tipo `string`:

```csharp
public class DecimalKeypadViewModel : INotifyPropertyChanged
{

    ···

    public DecimalKeypadViewModel()
    {

        ···

        DigitCommand = new Command<string>(
            execute: (string arg) =>
            {
                Entry += arg;
                if (Entry.StartsWith("0") && !Entry.StartsWith("0."))
                {
                    Entry = Entry.Substring(1);
                }
                RefreshCanExecutes();
            },
            canExecute: (string arg) =>
            {
                return !(arg == "." && Entry.Contains("."));
            });
    }

    ···
    
}
```

Il `execute` metodo aggiunge l'argomento di stringa per il `Entry` proprietà. Tuttavia, se il risultato inizia con uno zero (ma non il valore zero e un separatore decimale) quindi tale zero iniziale devono essere rimossi tramite il `Substring` (funzione).

Il `canExecute` restituisce `false` solo se l'argomento è la virgola decimale (che indica che viene premuto il separatore decimale) e `Entry` contiene già un separatore decimale. 

Tutti i `execute` chiamata di metodi `RefreshCanExecutes`, che a sua volta chiama `ChangeCanExecute` per entrambi `DigitCommand` e `ClearCommand`. Ciò garantisce che i pulsanti di backspace e il separatore decimale sono abilitati o disabilitati in base alla sequenza corrente di cifre specificati.

## <a name="adding-commands-to-existing-views"></a>Aggiunta di comandi per le visualizzazioni esistenti

Se si desidera utilizzare l'interfaccia dei comandi con le viste che non lo supporta, è possibile utilizzare un comportamento di xamarin. Forms che converte un evento in un comando. Come descritto nell'articolo [ **riutilizzabile EventToCommandBehavior**](~/xamarin-forms/app-fundamentals/behaviors/reusable/event-to-command-behavior.md).

## <a name="asynchronous-commanding-for-navigation-menus"></a>Esecuzione di comandi per i menu di navigazione asincrona

Esecuzione di comandi è ideale per l'implementazione di un menu di navigazione, ad esempio che nel [ **Data Binding Demo** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) programma stesso. Di seguito è parte di **MainPage. XAML**:


```xaml
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             xmlns:local="clr-namespace:DataBindingDemos"
             x:Class="DataBindingDemos.MainPage"
             Title="Data Binding Demos"
             Padding="10">
    <TableView Intent="Menu">
        <TableRoot>
            <TableSection Title="Basic Bindings">

                <TextCell Text="Basic Code Binding"
                          Detail="Define a data-binding in code"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:BasicCodeBindingPage}" />

                <TextCell Text="Basic XAML Binding"
                          Detail="Define a data-binding in XAML"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:BasicXamlBindingPage}" />

                <TextCell Text="Alternative Code Binding"
                          Detail="Define a data-binding in code without a BindingContext"
                          Command="{Binding NavigateCommand}"
                          CommandParameter="{x:Type local:AlternativeCodeBindingPage}" />

                ···

            </TableSection>
        </TableRoot>
    </TableView>
</ContentPage>
```

Quando si usa l'esecuzione di comandi con XAML, `CommandParameter` in genere sono impostate su stringhe. In questo caso, tuttavia, un'estensione di markup XAML viene usata in modo che il `CommandParameter` è di tipo `System.Type`.

Ogni `Command` proprietà è associata a una proprietà denominata `NavigateCommand`. Che proprietà è definita nel file code-behind, **MainPage.xaml.cs**:

```csharp
public partial class MainPage : ContentPage
{
    public MainPage()
    {
        InitializeComponent();

        NavigateCommand = new Command<Type>(
            async (Type pageType) =>
            {
                Page page = (Page)Activator.CreateInstance(pageType);
                await Navigation.PushAsync(page);
            });

        BindingContext = this;
    }

    public ICommand NavigateCommand { private set; get; }
}
```

Costruttore imposta la `NavigateCommand` proprietà per un `execute` metodo che crea un'istanza di `System.Type` parametro e quindi si passa a esso. Poiché il `PushAsync` chiamata richiede un `await` (operatore), il `execute` (metodo) deve essere contrassegnato come asincrona. Questa operazione viene eseguita con il `async` (parola chiave) prima dell'elenco di parametri. 

Imposta inoltre il costruttore di `BindingContext` della pagina a se stessa in modo che fanno riferimento le associazioni di `NavigateCommand` in questa classe.

L'ordine del codice in questo costruttore fa la differenza: I `InitializeComponent` chiamata genera il codice XAML da analizzare, ma in quel momento l'associazione a una proprietà denominata `NavigateCommand` non può essere risolta perché `BindingContext` è impostato su `null`. Se il `BindingContext` è impostata nel costruttore *prima* `NavigateCommand` è impostato, l'associazione può essere risolto quando `BindingContext` è impostata, ma in quel momento, `NavigateCommand` è ancora `null`. Impostazione `NavigateCommand` dopo `BindingContext` non avrà alcun effetto sull'associazione perché una modifica a `NavigateCommand` non viene attivato un `PropertyChanged` evento e l'associazione non riconosce che `NavigateCommand` è ora valido.

Se si impostano entrambe `NavigateCommand` e `BindingContext` (in qualsiasi ordine) prima della chiamata a `InitializeComponent` funzionerà poiché entrambi i componenti dell'associazione vengono impostati quando il parser XAML rileva la definizione dell'associazione. 

Data Binding a volte può risultare difficile, ma come è stato illustrato in questa serie di articoli, sono potenti e versatile e notevolmente consentono di organizzare il codice per la separazione logica sottostante dall'interfaccia utente.



## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capitolo di associazione di dati dalla Rubrica di xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter18.md)
