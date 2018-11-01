---
title: L'interfaccia di comando di xamarin. Forms
description: Questo articolo illustra come implementare la proprietà di comando con le associazioni di dati di xamarin. Forms. L'esecuzione dei comandi interfaccia fornisce un approccio alternativo all'implementazione di comandi che è decisamente più adatto all'architettura di MVVM.
ms.prod: xamarin
ms.assetid: 69922284-F398-45C3-B4CC-B8E29BB4C533
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 68c7869254ae861cef8307431d925368082be921
ms.sourcegitcommit: 729035af392dc60edb9d99d3dc13d1ef69d5e46c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2018
ms.locfileid: "50675255"
---
# <a name="the-xamarinforms-command-interface"></a>L'interfaccia di comando di xamarin. Forms

Nell'architettura Model-View-ViewModel (MVVM), associazioni dati sono definite tra le proprietà nel ViewModel, che corrisponde in genere una classe che deriva da `INotifyPropertyChanged`e le proprietà nella visualizzazione, che corrisponde in genere il file XAML. In alcuni casi un'applicazione ha esigenze che vanno oltre le associazioni di queste proprietà, richiedendo all'utente di avviare i comandi che influiscono su un elemento ViewModel. Questi comandi vengono in genere segnalati dal clic sui pulsanti o un dito tocca e in genere vengono elaborati nel file code-behind in un gestore per il `Clicked` eventi del `Button` o il `Tapped` eventi di un `TapGestureRecognizer`.

L'esecuzione dei comandi interfaccia fornisce un approccio alternativo all'implementazione di comandi che è decisamente più adatto all'architettura di MVVM. L'elemento ViewModel stesso può contenere i comandi, ovvero metodi che vengono eseguiti in risposta a un'attività specifica nella visualizzazione, ad esempio un `Button` fare clic su. Le associazioni dati sono definite tra questi comandi e `Button`.

Per consentire un'associazione di dati tra un `Button` e un elemento ViewModel, la `Button` definisce due proprietà:

- [`Command`](xref:Xamarin.Forms.Button.Command) di tipo [`System.Windows.Input.ICommand`](xref:System.Windows.Input.ICommand)
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) di tipo `Object`

Per utilizzare l'interfaccia di comando, si definisce un'associazione di dati che ha come destinazione il `Command` proprietà del `Button` in cui l'origine è una proprietà nel ViewModel di tipo `ICommand`. L'elemento ViewModel contiene codice associato che `ICommand` proprietà che viene eseguito quando si fa clic sul pulsante. È possibile impostare `CommandParameter` ai dati arbitrari per distinguere tra più pulsanti, se sono tutti associato alla stessa `ICommand` proprietà nel ViewModel.

Il `Command` e `CommandParameter` proprietà vengono definite anche dalle classi seguenti:

- [`MenuItem`](xref:Xamarin.Forms.MenuItem) e, pertanto [ `ToolbarItem` ](xref:Xamarin.Forms.ToolbarItem), che deriva da `MenuItem`
- [`TextCell`](xref:Xamarin.Forms.TextCell) e, pertanto [ `ImageCell` ](xref:Xamarin.Forms.ImageCell), che deriva da `TextCell`
- [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)

[`SearchBar`](xref:Xamarin.Forms.SearchBar) definisce un [ `SearchCommand` ](xref:Xamarin.Forms.SearchBar.SearchCommand) vlastnosti typu `ICommand` e un [ `SearchCommandParameter` ](xref:Xamarin.Forms.SearchBar.SearchCommandParameter) proprietà. Il [ `RefreshCommand` ](xref:Xamarin.Forms.ListView.RefreshCommand) proprietà della [ `ListView` ](xref:Xamarin.Forms.ListView) anche è di tipo `ICommand`.

Tutti questi comandi possono essere gestiti all'interno di un elemento ViewModel in modo che non dipende dall'oggetto particolare dell'interfaccia utente nella visualizzazione.

## <a name="the-icommand-interface"></a>L'interfaccia ICommand

Il [ `System.Windows.Input.ICommand` ](xref:System.Windows.Input.ICommand) interfaccia non fa parte di xamarin. Forms. In cui è definito invece di [spazi](xref:System.Windows.Input) dello spazio dei nomi ed è costituito da un evento e due metodi:

```csharp
public interface ICommand
{
    public void Execute (Object parameter);

    public bool CanExecute (Object parameter);

    public event EventHandler CanExecuteChanged;
}
```

Per usare l'interfaccia di comando, l'elemento ViewModel contiene le proprietà di tipo `ICommand`:

```csharp
public ICommand MyCommand { private set; get; }
```

L'elemento ViewModel deve anche fare riferimento a una classe che implementa il `ICommand` interfaccia. Questa classe verrà illustrata tra breve. Nella visualizzazione, il `Command` proprietà di un `Button` è associato a tale proprietà:

```xaml
<Button Text="Execute command"
        Command="{Binding MyCommand}" />
```

Quando l'utente preme il `Button`, il `Button` chiamate la `Execute` metodo nella `ICommand` associato l'oggetto relativo `Command` proprietà. Questa è la parte più semplice dell'interfaccia di esecuzione dei comandi.

Il `CanExecute` metodo è più complesso. Quando l'associazione viene definito prima nel `Command` proprietà del `Button`, e quando l'associazione dati viene modificato in qualche modo, il `Button` chiamate il `CanExecute` metodo nella `ICommand` oggetto. Se `CanExecute` restituisce `false`, quindi il `Button` disabilitato automaticamente. Ciò indica che il comando specificato è attualmente disponibile o non valido.

Il `Button` associa anche un gestore nel `CanExecuteChanged` eventi di `ICommand`. L'evento viene generato da entro l'elemento ViewModel. Quando viene generato tale evento, il `Button` chiamate `CanExecute` nuovamente. Il `Button` consente a se stessa `CanExecute` restituisce `true` e si disabilita automaticamente se `CanExecute` restituisce `false`.

> [!IMPORTANT]
> Non usare la `IsEnabled` proprietà di `Button` se si usa l'interfaccia di comando.  

## <a name="the-command-class"></a>La classe di comando

Quando i ViewModel definisce una proprietà di tipo `ICommand`, l'elemento ViewModel deve inoltre contengono o fanno riferimento a una classe che implementa il `ICommand` interfaccia. Questa classe deve contengono o fanno riferimento le `Execute` e `CanExecute` metodi e attivare il `CanExecuteChanged` evento ogni volta che il `CanExecute` metodo può restituire un valore diverso.

È possibile scrivere una classe di questo tipo manualmente, oppure è possibile usare una classe che qualcun altro ha scritto. Poiché `ICommand` fa parte di Microsoft Windows, è stato usato per anni con applicazioni MVVM Windows. Usando una classe di Windows che implementa `ICommand` consente di condividere i ViewModel tra le applicazioni di Windows e le applicazioni xamarin. Forms.

Se la condivisione di ViewModel tra Windows e xamarin. Forms non rappresenta un problema, è quindi possibile usare la [ `Command` ](xref:Xamarin.Forms.Command) oppure [ `Command<T>` ](xref:Xamarin.Forms.Command`1) classe incluso in xamarin. Forms per implementare il `ICommand`dell'interfaccia. Queste classi consentono di specificare i corpi delle `Execute` e `CanExecute` metodi nei costruttori di classe. Usare `Command<T>` quando si usa la `CommandParameter` associata la proprietà per distinguere tra più visualizzazioni alla stessa `ICommand` proprietà e la più semplice `Command` classe quando non è un requisito.

## <a name="basic-commanding"></a>Esecuzione dei comandi di base

Il **voce Person** nella pagina il [ **Data Binding Demo** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) programma illustra alcuni semplici comandi implementati in un elemento ViewModel.

Il `PersonViewModel` definisce tre proprietà denominate `Name`, `Age`, e `Skills` che definiscono una persona. Classe che svolge *non* contenere `ICommand` proprietà:

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

Il `PersonCollectionViewModel` illustrato di seguito consente di creare nuovi oggetti di tipo `PersonViewModel` e consente all'utente di inserire i dati. A tale scopo, la classe definisce le proprietà `IsEditing` typu `bool` e `PersonEdit` typu `PersonViewModel`. Inoltre, la classe definisce tre proprietà di tipo `ICommand` e una proprietà denominata `Persons` di tipo `IList<PersonViewModel>`:

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

Questo elenco abbreviato non include il costruttore della classe, ovvero dove le tre proprietà di tipo `ICommand` sono definiti, che verrà visualizzato a breve. Si noti che le modifiche apportate alle tre proprietà di tipo `ICommand` e il `Persons` proprietà non comportano `PropertyChanged` eventi generati. Queste proprietà sono tutti impostati quando la classe viene inizialmente creata e non cambiano in seguito.

Prima di esaminare il costruttore del `PersonCollectionViewModel` classe, esaminiamo il file XAML per il **persona voce** programma. Questo file contiene un `Grid` con relativo `BindingContext` impostata sul `PersonCollectionViewModel`. Il `Grid` contiene un `Button` con il testo **New** con relativo `Command` proprietà associata ai `NewCommand` proprietà nel ViewModel, un modulo di immissione con proprietà associata al `IsEditing` proprietà, come anche come proprietà della `PersonViewModel`, e altri due pulsanti associati ai `SubmitCommand` e `CancelCommand` proprietà dell'elemento ViewModel. L'elemento finale `ListView` Visualizza la raccolta delle persone già acceduto:

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

Ecco come funziona: l'utente preme prima la **New** pulsante. Ciò consente al form di immissione ma disabilita il **New** pulsante. L'utente immette quindi un nome, età e competenze. In qualsiasi momento durante il processo di modifica, l'utente potrà selezionare la **annullare** pulsante ricominciare da capo. Solo quando un nome e una durata valida sono stati immessi è il **Submit** pulsante è abilitato. Premendo questo **Submit** pulsante trasferisce la persona per la raccolta visualizzata per il `ListView`. Dopo che il **annullare** o **Submit** pulsante, viene cancellato al form di immissione e la **New** pulsante viene abilitato di nuovo.

La schermata iOS a sinistra mostra il layout prima viene immessa una durata valida. Android e UWP schermate Mostra le **Submit** pulsante è abilitato dopo aver impostata un'età:

[![Voce di persona](commanding-images/personentry-small.png "persona voce")](commanding-images/personentry-large.png#lightbox "persona voce")

Il programma non ha le funzionalità per la modifica di voci esistenti e non consente di salvare le voci quando si esce dalla pagina.

Tutta la logica per la **New**, **Submit**, e **Annulla** pulsanti viene gestito in `PersonCollectionViewModel` mediante le definizioni del `NewCommand`, `SubmitCommand`, e `CancelCommand` proprietà. Il costruttore del `PersonCollectionViewModel` imposta queste tre proprietà agli oggetti di tipo `Command`.  

Oggetto [costruttore](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean})) del `Command` classe consente di passare argomenti di tipo `Action` e `Func<bool>` corrispondente per il `Execute` e `CanExecute` metodi. È più semplice definire queste funzioni e le azioni come direttamente le funzioni lambda nel `Command` costruttore. Ecco la definizione del `Command` dell'oggetto per il `NewCommand` proprietà:

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

Quando l'utente fa clic il **New** pulsante, il `execute` funzione passato al `Command` costruttore viene eseguito. Verrà creata una nuova `PersonViewModel` dell'oggetto, imposta un gestore per tale oggetto `PropertyChanged` evento, imposta `IsEditing` al `true`e chiama il `RefreshCanExecutes` metodo definito dopo il costruttore.

Oltre all'implementazione di `ICommand` interfaccia, il `Command` classe definisce anche un metodo denominato `ChangeCanExecute`. L'elemento ViewModel deve chiamare `ChangeCanExecute` per un `ICommand` proprietà ogni volta che si esegue un'operazione che potrebbe cambiare il valore restituito del `CanExecute` (metodo). Una chiamata a `ChangeCanExecute` fa sì che il `Command` classe attivano la `CanExecuteChanged` (metodo). Il `Button` è associato un gestore dell'evento e risponde chiamando `CanExecute` anche in questo caso e quindi l'abilitazione di se stesso in base al valore restituito del metodo.

Quando la `execute` metodo di `NewCommand` chiamate `RefreshCanExecutes`, il `NewCommand` proprietà ottiene una chiamata a `ChangeCanExecute`e la `Button` chiamate il `canExecute` metodo, che ora restituisce `false` perché il `IsEditing`proprietà è ora `true`.

Il `PropertyChanged` gestore per il nuovo `PersonViewModel` object chiama il `ChangeCanExecute` metodo `SubmitCommand`. Ecco come viene implementato tale proprietà comando:


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

Il `canExecute` per funziona `SubmitCommand` viene chiamato ogni volta che è disponibile una proprietà modificata nel `PersonViewModel` in fase di modifica dell'oggetto. Viene restituito `true` solo quando il `Name` proprietà è lungo, contenere almeno un carattere e `Age` è maggiore di 0. A quel punto, il **Submit** pulsante viene abilitato.

Il `execute` funzionare per **Submit** rimuove il gestore di modifica della proprietà dal `PersonViewModel`, aggiunge l'oggetto al `Persons` insieme e restituisce tutti gli elementi e le condizioni iniziali.

Il `execute` funzionare per il **annullare** pulsante effettua le operazioni necessarie che il **Submit** pulsante tranne che aggiungere l'oggetto alla raccolta:

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

Il `canExecute` restituzione del metodo `true` in qualsiasi momento un `PersonViewModel` viene modificato.

Queste tecniche può essere adattate in scenari più complessi: una proprietà in `PersonCollectionViewModel` può essere associato ai `SelectedItem` proprietà del `ListView` per la modifica di elementi esistenti e un **eliminare** pulsante è stato possibile aggiungere per eliminare tali elementi.

Non è necessario definire il `execute` e `canExecute` metodi come funzioni lambda. È possibile scriverle come regolare i metodi privati in ViewModel e farvi riferimento nel `Command` costruttori. Questo approccio, tuttavia, tendono a causare molti dei metodi a cui fa riferimento solo una volta l'elemento ViewModel.

## <a name="using-command-parameters"></a>Utilizzo di parametri di comando

Talvolta è utile per uno o più pulsanti o altri oggetti dell'interfaccia utente da condividere lo stesso `ICommand` proprietà nel ViewModel. In questo caso, si utilizza il `CommandParameter` proprietà per distinguere tra i pulsanti.

È possibile continuare a usare il `Command` classe per questi condiviso `ICommand` proprietà. La classe definisce un [costruttore alternativo](xref:Xamarin.Forms.Command.%23ctor(System.Action{System.Object},System.Func{System.Object,System.Boolean})) che accetta `execute` e `canExecute` metodi con parametri di tipo `Object`. Questo è il modo in `CommandParameter` viene passato a questi metodi.

Tuttavia, quando si usa `CommandParameter`, è più facile utilizzare il metodo generico [ `Command<T>` ](xref:Xamarin.Forms.Command`1) classe per specificare il tipo dell'oggetto set `CommandParameter`. Il `execute` e `canExecute` metodi specificato hanno parametri di quel tipo.

Il **tastiera decimale** pagina questa tecnica viene mostrato come implementare un tastierino per l'immissione di numeri decimali. Il `BindingContext` per il `Grid` è un `DecimalKeypadViewModel`. Il `Entry` è associata la proprietà di tale elemento ViewModel per il `Text` proprietà di un `Label`. Tutti i `Button` gli oggetti sono associati a vari comandi nel ViewModel: `ClearCommand`, `BackspaceCommand`, e `DigitCommand`:

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

I 11 pulsanti per i 10 cifre e il separatore decimale condividono un'associazione a `DigitCommand`. Il `CommandParameter` consente di distinguere tra questi pulsanti. Il valore impostato su `CommandParameter` equivale in genere il testo visualizzato dal pulsante tranne il separatore decimale, che, per maggiore chiarezza, viene visualizzato con un carattere punto intermedio.

Ecco il programma in azione:

[![Tastiera decimale](commanding-images/decimalkeyboard-small.png "tastiera decimale")](commanding-images/decimalkeyboard-large.png#lightbox "decimale della tastiera")

Si noti che il pulsante per il separatore decimale in tutte le tre schermate è disabilitato perché il numero immesso contiene già un separatore decimale.

Il `DecimalKeypadViewModel` definisce un `Entry` vlastnosti typu `string` (che è l'unica proprietà che attiva un `PropertyChanged` eventi) e tre le proprietà di tipo `ICommand`:

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

Il pulsante corrispondente il `ClearCommand` è sempre abilitata e imposta semplicemente la voce di nuovo su "0":

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

Poiché il pulsante è sempre abilitato, non è necessario specificare una `canExecute` argomento in di `Command` costruttore.

La logica per l'immissione di numeri e un BACKSPACE è un'operazione delicata perché se non sono state inserite cifre, il `Entry` proprietà corrisponde alla stringa "0". Se l'utente digita più zeri, la `Entry` contiene comunque uno zero. Se l'utente digita qualsiasi altra cifra, tale cifra sostituisce lo zero. Tuttavia, se l'utente digita una cifra decimale prima di qualsiasi altra cifra, quindi `Entry` è la stringa "0".

Il **Backspace** pulsante è abilitato solo quando la lunghezza della voce è maggiore di 1 o se `Entry` non è uguale alla stringa "0":

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

La logica per la `execute` funzionare per il **Backspace** pulsante assicura che il `Entry` è almeno una stringa "0".

Il `DigitCommand` proprietà è associata ai 11 pulsanti, ognuno dei quali identifica se stesso con il `CommandParameter` proprietà. Il `DigitCommand` può essere impostata su un'istanza di normali `Command` classe, ma facile utilizzo il `Command<T>` classe generica. Quando si usa l'interfaccia dei comandi con XAML, il `CommandParameter` proprietà sono in genere le stringhe, e che corrisponde al tipo di argomento generico. Il `execute` e `canExecute` quindi dispongono degli argomenti di tipo `string`:

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

Il `execute` metodo aggiunge l'argomento stringa per il `Entry` proprietà. Tuttavia, se il risultato inizia con uno zero (ma non pari a zero e un separatore decimale) quindi tale zero iniziale deve essere rimossa utilizzando il `Substring` (funzione).

Il `canExecute` restituzione del metodo `false` solo se l'argomento è il separatore decimale (che indica che viene premuto il separatore decimale) e `Entry` già contiene un separatore decimale.

Tutti i `execute` chiamata di metodi `RefreshCanExecutes`, che a sua volta chiama `ChangeCanExecute` per entrambi `DigitCommand` e `ClearCommand`. Ciò garantisce che i pulsanti backspace e il separatore decimale sono abilitati o disabilitate in base alla sequenza corrente di cifre immesse.

## <a name="adding-commands-to-existing-views"></a>Aggiunta di comandi per le visualizzazioni esistenti

Se si vuole usare l'interfaccia dei comandi con le visualizzazioni che non la supportano, è possibile utilizzare un comportamento di xamarin. Forms che converte un evento in un comando. Come descritto nell'articolo [ **riutilizzabile EventToCommandBehavior**](~/xamarin-forms/app-fundamentals/behaviors/reusable/event-to-command-behavior.md).

## <a name="asynchronous-commanding-for-navigation-menus"></a>L'esecuzione di comandi per i menu di navigazione asincrona

L'esecuzione di comandi è utile per l'implementazione del menu di navigazione, ad esempio che nel [ **Data Binding Demo** ](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) programma stesso. Di seguito è parte della **MainPage. XAML**:

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

Quando si usa l'esecuzione dei comandi con XAML, `CommandParameter` in genere sono impostate su stringhe. In questo caso, tuttavia, un'estensione di markup XAML viene usata in modo che il `CommandParameter` JE typu `System.Type`.

Ciascuna `Command` proprietà è associata a una proprietà denominata `NavigateCommand`. Che proprietà è definita nel file code-behind **MainPage.xaml.cs**:

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

Il costruttore imposta la `NavigateCommand` proprietà per un `execute` metodo che crea un'istanza di `System.Type` parametro e quindi si passa a esso. Poiché il `PushAsync` chiamata richiede un' `await` operatore, la `execute` metodo deve essere contrassegnato come asincrona. Questa operazione viene eseguita con il `async` parola chiave prima l'elenco di parametri.

Il costruttore imposta anche il `BindingContext` della pagina a se stessa in modo che le associazioni fanno riferimento al `NavigateCommand` in questa classe.

L'ordine del codice in questo costruttore fa la differenza: I `InitializeComponent` chiamata determina il XAML da analizzare, ma in quel momento l'associazione a una proprietà denominata `NavigateCommand` non può essere risolta perché `BindingContext` è impostata su `null`. Se il `BindingContext` è impostato nel costruttore *prima* `NavigateCommand` è impostato, l'associazione può essere risolto quando `BindingContext` è impostata, ma in quel momento, `NavigateCommand` resta `null`. L'impostazione `NavigateCommand` dopo aver `BindingContext` non avrà alcun effetto sull'associazione perché una modifica alla `NavigateCommand` non viene attivato un `PropertyChanged` evento e l'associazione non sa che `NavigateCommand` è ora valido.

Se si impostano entrambe `NavigateCommand` e `BindingContext` (in qualsiasi ordine) prima della chiamata a `InitializeComponent` funzionerà poiché entrambi i componenti dell'associazione vengono impostati quando il parser XAML rileva la definizione di associazione.

Data Binding in alcuni casi può essere difficile, ma come si è visto in questa serie di articoli, sono potenti e versatili e contribuire notevolmente a organizzare il codice mediante la separazione logica sottostante dall'interfaccia utente.

## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demo (esempio)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)
- [Capitolo di associazione di dati dal libro di xamarin. Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter18.md)
