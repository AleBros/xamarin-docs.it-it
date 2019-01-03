---
title: Interfaccia di comando di Xamarin.Forms
description: Questo articolo illustra come implementare la proprietà Command con i data binding di Xamarin.Forms. L'interfaccia di esecuzione dei comandi consente un approccio alternativo all'implementazione di comandi decisamente più adatto all'architettura MVVM.
ms.prod: xamarin
ms.assetid: 69922284-F398-45C3-B4CC-B8E29BB4C533
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 01/05/2018
ms.openlocfilehash: 37fbc0107414521a87c263d327ffd9b8940384eb
ms.sourcegitcommit: be6f6a8f77679bb9675077ed25b5d2c753580b74
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2018
ms.locfileid: "53053464"
---
# <a name="the-xamarinforms-command-interface"></a>Interfaccia di comando di Xamarin.Forms

[![Scaricare esempio](~/media/shared/download.png) Scaricare l'esempio](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/)

Nell'architettura Model-View-ViewModel (MVVM), vengono definiti data binding tra le proprietà in ViewModel, che in genere è una classe che deriva da `INotifyPropertyChanged`, e le proprietà in View, che in genere è il file XAML. In alcuni casi un'applicazione ha esigenze che vanno oltre queste associazioni di proprietà, richiedendo che l'utente avvii comandi che hanno un effetto all'interno di ViewModel. Questi comandi vengono in genere segnalati dal clic su un pulsante o dal tocco con un dito e in genere vengono elaborati nel file code-behind in un gestore per l'evento `Clicked` dell'elemento `Button` o per l'evento `Tapped` di un elemento `TapGestureRecognizer`.

L'interfaccia di esecuzione dei comandi consente un approccio alternativo all'implementazione di comandi decisamente più adatto all'architettura MVVM. L'elemento ViewModel stesso può contenere comandi, ovvero metodi che vengono eseguiti in risposta a un'attività specifica in View, ad esempio il clic su un elemento `Button`. Tra questi comandi e l'elemento `Button` vengono definiti data binding.

Per consentire un data binding tra un elemento `Button` e un elemento ViewModel, l'elemento `Button` definisce due proprietà:

- [`Command`](xref:Xamarin.Forms.Button.Command) di tipo [`System.Windows.Input.ICommand`](xref:System.Windows.Input.ICommand)
- [`CommandParameter`](xref:Xamarin.Forms.Button.CommandParameter) di tipo `Object`

Per usare l'interfaccia di comando, si definisce un data binding che ha come destinazione la proprietà `Command` dell'elemento `Button` in cui l'origine è una proprietà nell'elemento ViewModel di tipo `ICommand`. L'elemento ViewModel contiene codice associato con tale proprietà `ICommand` che viene eseguito quando si fa clic sul pulsante. È possibile impostare `CommandParameter` su dati arbitrari per distinguere tra più pulsanti, se questi sono tutti associati alla stessa proprietà `ICommand` nell'elemento ViewModel.

Vengono anche definite le proprietà `Command` e `CommandParameter` dalle classi seguenti:

- [`MenuItem`](xref:Xamarin.Forms.MenuItem) e, di conseguenza, [`ToolbarItem`](xref:Xamarin.Forms.ToolbarItem), che deriva da `MenuItem`
- [`TextCell`](xref:Xamarin.Forms.TextCell) e, di conseguenza, [`ImageCell`](xref:Xamarin.Forms.ImageCell), che deriva da `TextCell`
- [`TapGestureRecognizer`](xref:Xamarin.Forms.TapGestureRecognizer)

[`SearchBar`](xref:Xamarin.Forms.SearchBar) definisce una proprietà [`SearchCommand`](xref:Xamarin.Forms.SearchBar.SearchCommand) di tipo `ICommand` e una proprietà [`SearchCommandParameter`](xref:Xamarin.Forms.SearchBar.SearchCommandParameter). Anche la proprietà [`RefreshCommand`](xref:Xamarin.Forms.ListView.RefreshCommand) di [`ListView`](xref:Xamarin.Forms.ListView) è di tipo `ICommand`.

Tutti questi comandi possono essere gestiti all'interno di un elemento ViewModel in modo che non dipenda dall'oggetto interfaccia utente specifico nell'elemento View.

## <a name="the-icommand-interface"></a>Interfaccia ICommand

L'interfaccia [`System.Windows.Input.ICommand`](xref:System.Windows.Input.ICommand) non fa parte di Xamarin.Forms. È invece definita nello spazio dei nomi [System.Windows.Input](xref:System.Windows.Input) ed è costituita da due metodi e da un evento:

```csharp
public interface ICommand
{
    public void Execute (Object parameter);

    public bool CanExecute (Object parameter);

    public event EventHandler CanExecuteChanged;
}
```

Per consentire l'uso dell'interfaccia di comando, l'elemento ViewModel contiene proprietà di tipo `ICommand`:

```csharp
public ICommand MyCommand { private set; get; }
```

ViewModel deve anche fare riferimento a una classe che implementi l'interfaccia `ICommand`. Questa classe verrà descritta tra poco. Nell'elemento View, la proprietà `Command` di un elemento `Button` è associata a tale proprietà:

```xaml
<Button Text="Execute command"
        Command="{Binding MyCommand}" />
```

Quando l'utente preme l'elemento `Button`, l'elemento `Button` chiama il metodo `Execute` nell'oggetto `ICommand` associato alla sua proprietà `Command`. Questa è la parte più semplice dell'interfaccia di esecuzione di comandi.

Il metodo `CanExecute` è più complesso. Se il binding viene prima definito nella proprietà `Command` dell'elemento `Button`, quando il data binding cambia, l'elemento `Button` chiama il metodo `CanExecute` nell'oggetto `ICommand`. Se `CanExecute` restituisce `false`, l'elemento `Button` si disabilita automaticamente. Ciò indica che in quel momento il comando specificato non è disponibile o non è valido.

L'elemento `Button`, poi, associa un gestore all'evento `CanExecuteChanged` di `ICommand`. L'evento viene generato all'interno dell'elemento ViewModel. Quando l'evento viene generato, l'elemento `Button` chiama di nuovo `CanExecute`. L'elemento `Button` viene abilitato automaticamente se `CanExecute` torna `true` e viene disabilitato automaticamente se `CanExecute` torna `false`.

> [!IMPORTANT]
> Se si usa l'interfaccia di comando, non usare la proprietà `IsEnabled` di `Button`.  

## <a name="the-command-class"></a>La classe Command

Quando l'elemento ViewModel definisce una proprietà di tipo `ICommand`, deve anche contenere o fare riferimento a una classe che implementi l'interfaccia `ICommand`. Questa classe deve contenere o fare riferimento ai metodi `Execute` e `CanExecute` e generare l'evento `CanExecuteChanged` ogni volta che il metodo `CanExecute` può restituire un valore diverso.

È possibile scrivere una classe di questo tipo manualmente oppure è possibile usare una classe scritta da altri. Poiché l'interfaccia `ICommand` fa parte di Microsoft Windows, è stata usata per anni per applicazioni MVVM Windows. L'uso di una classe Windows che implementa l'interfaccia `ICommand` consente di condividere gli elementi ViewModel tra applicazioni Windows e applicazioni Xamarin.Forms.

Se la condivisione di ViewModel tra Windows e Xamarin.Forms non rappresenta un problema, è possibile usare la classe [`Command`](xref:Xamarin.Forms.Command) o [`Command<T>` ](xref:Xamarin.Forms.Command`1) inclusa in Xamarin.Forms per implementare l'interfaccia `ICommand`. Queste classi consentono di specificare il corpo dei metodi `Execute` e `CanExecute` nei costruttori delle classi. Usare `Command<T>` quando si usa la proprietà `CommandParameter` per distinguere tra più visualizzazioni associate alla stessa proprietà `ICommand` e la classe `Command`, più semplice, quando questo non è un requisito.

## <a name="basic-commanding"></a>Esecuzione di comandi di base

La pagina **Person Entry** (Voce persona) nel programma [**Data Binding Demos**](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) (Demo del data binding) illustra alcuni semplici comandi implementati in un elemento ViewModel.

`PersonViewModel` definisce tre proprietà, `Name`, `Age` e `Skills`, che definiscono una persona. Questa classe *non* contiene proprietà `ICommand`:

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

La classe `PersonCollectionViewModel` illustrata di seguito crea nuovi oggetti di tipo `PersonViewModel` e consente all'utente di inserire i dati. A tale scopo, la classe definisce le proprietà `IsEditing` di tipo `bool` e `PersonEdit` di tipo `PersonViewModel`. La classe, poi, definisce tre proprietà di tipo `ICommand` e una proprietà, `Persons`, di tipo `IList<PersonViewModel>`:

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

Questo elenco abbreviato non include il costruttore della classe, dove vengono definite le tre proprietà di tipo `ICommand`, che verrà illustrato a breve. Si noti che le modifiche apportate alle tre proprietà di tipo `ICommand` e alla proprietà `Persons` non comportano la generazione di eventi `PropertyChanged`. Queste proprietà vengono tutte impostate quando la classe viene inizialmente creata e in seguito non cambiano.

Prima di esaminare il costruttore della classe `PersonCollectionViewModel`, è necessario esaminare il file XAML per il programma **Person Entry** (Voce persona). Questo contiene un elemento `Grid` con la relativa proprietà `BindingContext` impostata sulla classe `PersonCollectionViewModel`. L'elemento `Grid` contiene un elemento `Button` con il testo **New** (Nuovo) e la proprietà `Command` associata alla proprietà `NewCommand` nell'elemento ViewModel, un modulo di immissione con proprietà associate alla proprietà `IsEditing`, nonché a proprietà di `PersonViewModel`, e altri due pulsanti associati alle proprietà `SubmitCommand` e `CancelCommand` dell'elemento ViewModel. L'elemento `ListView` finale visualizza la raccolta delle persone già immesse:

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

Ecco come funziona: l'utente preme prima il pulsante **New** (Nuovo). Questa operazione abilita il modulo di immissione ma disabilita il pulsante **New** (Nuovo). L'utente immette quindi un nome, l'età e le competenze. In qualsiasi momento durante il processo di modifica, l'utente può selezionare il pulsante **Cancel** (Annulla) per ricominciare da capo. Il pulsante **Submit** (Invia) viene abilitato solo dopo l'immissione di un nome e di un'età validi. La selezione del pulsante **Submit** (Invia) trasferisce la persona nella raccolta visualizzata dall'elemento `ListView`. Quando si seleziona **Cancel** (Annulla) o **Submit** (Invia), il modulo di immissione viene cancellato e il pulsante **New** (Nuovo) viene abilitato di nuovo.

La schermata iOS a sinistra visualizza il layout prima dell'immissione di un'età valida. Le schermate Android e UWP visualizzano il pulsante **Submit** (Invia) dopo l'impostazione di un'età:

[![Person Entry (Voce persona)](commanding-images/personentry-small.png "Person Entry (Voce persona)")](commanding-images/personentry-large.png#lightbox "Person Entry (Voce persona)")

Il programma non ha una funzionalità per la modifica di voci esistenti e non consente di salvare le voci quando si esce dalla pagina.

Tutta la logica per i pulsanti **New** (Nuovo), **Submit** (Invia) e **Cancel** (Annulla) viene gestita in `PersonCollectionViewModel` tramite la definizione delle proprietà `NewCommand`, `SubmitCommand` e `CancelCommand`. Il costruttore della classe `PersonCollectionViewModel` imposta queste tre proprietà su oggetti di tipo `Command`.  

Un [costruttore](xref:Xamarin.Forms.Command.%23ctor(System.Action,System.Func{System.Boolean})) della classe `Command` consente di passare argomenti di tipo `Action` e `Func<bool>` corrispondenti ai metodi `Execute` e `CanExecute`. Il modo più semplice è definire queste funzioni e queste azioni come funzioni lambda direttamente nel costruttore `Command`. Ecco la definizione dell'oggetto `Command` per la proprietà `NewCommand`:

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

Quando l'utente fa clic sul pulsante **New** (Nuovo), viene eseguita la funzione `execute` passata al costruttore `Command`. Ciò crea un nuovo oggetto `PersonViewModel`, imposta un gestore per l'evento `PropertyChanged` di tale oggetto, imposta `IsEditing` su `true` e chiama il metodo `RefreshCanExecutes` definito dopo il costruttore.

Oltre all'implementazione dell'interfaccia `ICommand`, la classe `Command` definisce anche un metodo denominato `ChangeCanExecute`. L'elemento ViewModel deve chiamare `ChangeCanExecute` per la proprietà `ICommand` ogni volta che si verifica un evento che può cambiare il valore restituito del metodo `CanExecute`. Una chiamata a `ChangeCanExecute` fa sì che la classe `Command` attivi il metodo `CanExecuteChanged`. All'elemento `Button` è associato un gestore di tale evento. Il pulsante risponde chiamando di nuovo `CanExecute` e quindi abilitandosi automaticamente in base al valore restituito dal metodo.

Quando il metodo `execute` di `NewCommand` chiama `RefreshCanExecutes`, la proprietà `NewCommand` ottiene una chiamata a `ChangeCanExecute` e l'elemento `Button` chiama il metodo `canExecute`, che ora restituisce `false` perché la proprietà `IsEditing` ora è `true`.

Il gestore di `PropertyChanged` per il nuovo oggetto `PersonViewModel` chiama il metodo `ChangeCanExecute` di `SubmitCommand`. Ecco come viene implementata tale proprietà del comando:


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

La funzione `canExecute` per `SubmitCommand` viene chiamata ogni volta che viene cambiata una proprietà nell'oggetto `PersonViewModel` in corso di modifica. Restituisce `true` solo quando la proprietà `Name` è lunga almeno un carattere e `Age` è maggiore di 0. A quel punto, il pulsante **Submit** (Invia) viene abilitato.

La funzione `execute` per **Submit** (Invia) rimuove il gestore di modifica della proprietà da `PersonViewModel`, aggiunge l'oggetto alla raccolta `Persons` e ripristina le condizioni iniziali di tutti gli elementi.

La funzione `execute` per il pulsante **Cancel** (Annulla) esegue le stesse operazioni del pulsante **Submit** (Invia), ad eccezione dell'aggiunta dell'oggetto alla raccolta:

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

Il metodo `canExecute` restituisce `true` in qualsiasi momento durante la modifica di un elemento `PersonViewModel`.

Queste tecniche possono essere adattate a scenari più complessi: è possibile associare una proprietà in `PersonCollectionViewModel` alla proprietà `SelectedItem` dell'elemento `ListView` per la modifica di elementi esistenti ed è possibile aggiungere un pulsante **Delete** (Elimina) per eliminare tali elementi.

Non è necessario definire i metodi `execute` e `canExecute` come funzioni lambda. È possibile scriverli come metodi privati normali in ViewModel e farvi riferimento nei costruttori di `Command`. Questo approccio, tuttavia, tende ad avere come risultato la presenza di molti metodi a cui si fa riferimento solo una volta nell'elemento ViewModel.

## <a name="using-command-parameters"></a>Uso di parametri di comando

È talvolta comodo condividere la stessa proprietà `ICommand` nell'elemento ViewModel per uno o più pulsanti o altri oggetti dell'interfaccia utente. In questo caso, per distinguere i pulsanti si usa la proprietà `CommandParameter`.

È possibile continuare a usare la classe `Command` per queste proprietà `ICommand` condivise. La classe definisce un [costruttore alternativo](xref:Xamarin.Forms.Command.%23ctor(System.Action{System.Object},System.Func{System.Object,System.Boolean})) che accetta i metodi `execute` e `canExecute` con parametri di tipo `Object`. Questo è il modo in cui `CommandParameter` viene passato a questi metodi.

Quando si usa `CommandParameter`, tuttavia, è più facile specificare il tipo dell'oggetto impostato su `CommandParameter` tramite la classe [`Command<T>`](xref:Xamarin.Forms.Command`1) generica. I metodi `execute` e `canExecute` specificati hanno parametri di quel tipo.

La pagina **Decimal Keyboard** (Tastiera decimale) descrive questa tecnica illustrando come implementare un tastierino per l'immissione di numeri decimali. La proprietà `BindingContext` per l'elemento `Grid` è un oggetto `DecimalKeypadViewModel`. La proprietà `Entry` di questo elemento ViewModel è associata alla proprietà `Text` di un elemento `Label`. Tutti gli oggetti `Button` sono associati a vari comandi nel ViewModel: `ClearCommand`, `BackspaceCommand` e `DigitCommand`:

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

Gli 11 pulsanti per le 10 cifre e il separatore decimale condividono un'associazione a `DigitCommand`. `CommandParameter` consente di distinguere questi pulsanti. Il valore impostato in `CommandParameter` corrisponde in genere al testo visualizzato dal pulsante, ad eccezione del separatore decimale, che, per maggiore chiarezza, viene visualizzato con un carattere punto al centro.

Ecco il programma in azione:

[![Decimal Keyboard (Tastiera decimale)](commanding-images/decimalkeyboard-small.png "Decimal Keyboard (Tastiera decimale)")](commanding-images/decimalkeyboard-large.png#lightbox "Decimal Keyboard (Tastiera decimale)")

Si noti che il pulsante per il separatore decimale nei tre screenshot è disabilitato, perché il numero immesso contiene già un separatore decimale.

`DecimalKeypadViewModel` definisce una proprietà `Entry` di tipo `string` (l'unica proprietà che attiva un evento `PropertyChanged`) e tre proprietà di tipo `ICommand`:

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

Il pulsante corrispondente a `ClearCommand` è sempre abilitato e, semplicemente, imposta l'immissione di nuovo su "0":

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

Poiché il pulsante è sempre abilitato, non è necessario specificare un argomento `canExecute` nel costruttore `Command`.

La logica per l'immissione di numeri e la funzione Backspace è un'operazione complessa, perché se non sono state immesse cifre, la proprietà `Entry` corrisponde alla stringa "0". Se l'utente digita più zeri, la proprietà `Entry` contiene comunque un solo zero. Se l'utente digita qualsiasi altra cifra, tale cifra sostituisce lo zero. Se tuttavia l'utente digita un separatore decimale prima di qualsiasi altra cifra, la proprietà `Entry` corrisponde a "0.".

Il pulsante **Backspace** viene abilitato solo se la lunghezza dell'immissione è maggiore di 1 o se `Entry` non è uguale alla stringa "0":

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

La logica della funzione `execute` per il pulsante **Backspace** assicura che `Entry` corrisponda almeno alla stringa "0".

La proprietà `DigitCommand` è associata a 11 pulsanti, ognuno dei quali identifica se stesso con la proprietà `CommandParameter`. La proprietà `DigitCommand` può essere impostata su un'istanza della classe `Command` normale, ma è più facile usare la classe generica `Command<T>`. Quando si usa l'interfaccia di esecuzione dei comandi con XAML, le proprietà `CommandParameter` sono in genere di tipo stringa, ovvero dello stesso tipo dell'argomento generico. Le funzioni `execute` e `canExecute` hanno quindi argomenti di tipo `string`:

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

Il metodo `execute` accoda l'argomento stringa alla proprietà `Entry`. Se tuttavia il risultato inizia con uno zero (ma non con uno zero e un separatore decimale), lo zero iniziale deve essere rimosso tramite la funzione `Substring`.

Il metodo `canExecute` restituisce `false` solo se l'argomento è il separatore decimale (che indica che è stato premuto il separatore decimale) ed `Entry` contiene già un separatore decimale.

Tutti i metodi `execute` chiamano `RefreshCanExecutes`, che a sua volta chiama `ChangeCanExecute` sia per `DigitCommand` che per `ClearCommand`. Ciò garantisce che i pulsanti Backspace e del separatore decimale siano abilitati o disabilitati in base alla sequenza corrente delle cifre immesse.

## <a name="adding-commands-to-existing-views"></a>Aggiunta di comandi a visualizzazioni esistenti

Se si vuole usare l'interfaccia di esecuzione dei comandi con visualizzazioni che non la supportano, è possibile usare il comportamento di Xamarin.Forms che converte un evento in un comando. Questo è descritto nell'articolo [**EventToCommandBehavior riutilizzabile**](~/xamarin-forms/app-fundamentals/behaviors/reusable/event-to-command-behavior.md).

## <a name="asynchronous-commanding-for-navigation-menus"></a>Esecuzione dei comandi asincrona per i menu di spostamento

L'esecuzione dei comandi è comoda per l'implementazione dei menu di spostamento, ad esempio per quello del programma [**Data Binding Demo**](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) (Demo del data binding) stesso. Ecco una parte di **MainPage.xaml**:

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

Quando si usa l'esecuzione dei comandi con XAML, in genere le proprietà `CommandParameter` sono impostate come stringhe. In questo caso, tuttavia, viene usata un'estensione di markup XAML in modo che la proprietà `CommandParameter` sia di tipo `System.Type`.

Ogni proprietà `Command` è associata a una proprietà denominata `NavigateCommand`. Tale proprietà è definita nel file code-behind **MainPage.xaml.cs**:

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

Il costruttore imposta la proprietà `NavigateCommand` su un metodo `execute` che crea un'istanza del parametro `System.Type` e quindi passa a questa. Poiché la chiamata di `PushAsync` richiede un operatore `await`, il metodo `execute` deve essere contrassegnato come asincrono. Questa operazione viene eseguita con la parola chiave `async` prima dell'elenco di parametri.

Il costruttore imposta anche la proprietà `BindingContext` della pagina su se stessa, in modo che i binding facciano riferimento alla proprietà `NavigateCommand` in questa classe.

L'ordine del codice in questo costruttore è importante: la chiamata a `InitializeComponent` attiva l'analisi del codice XAML, ma in quel momento il binding a una proprietà `NavigateCommand` non può essere risolto perché `BindingContext` è impostata su `null`. Se la proprietà `BindingContext` viene impostata nel costruttore *prima* che venga impostata `NavigateCommand`, il binding può essere risolto quando viene impostata la proprietà `BindingContext`, ma in quel momento `NavigateCommand` è ancora `null`. L'impostazione di `NavigateCommand` dopo `BindingContext` non ha alcun effetto sul binding, perché una modifica a `NavigateCommand` non genera un evento `PropertyChanged` e il binding non sa che la proprietà `NavigateCommand` è ora valida.

L'impostazione di entrambe le proprietà `NavigateCommand` e `BindingContext` (in qualsiasi ordine) prima della chiamata a `InitializeComponent` funzionerà, poiché quando il parser del codice XAML rileva la definizione del binding entrambi i componenti del binding sono stati impostati.

I data binding possono essere complessi in alcuni casi, ma, come si è visto in questa serie di articoli, sono potenti e versatili e contribuiscono notevolmente all'organizzazione del codice, separando la logica sottostante dall'interfaccia utente.

## <a name="related-links"></a>Collegamenti correlati

- [Data Binding Demos (esempio)](https://developer.xamarin.com/samples/xamarin-forms/DataBindingDemos/) (Demo di data binding)
- [Capitolo sul data binding della documentazione di Xamarin.Forms](~/xamarin-forms/creating-mobile-apps-xamarin-forms/summaries/chapter18.md)
