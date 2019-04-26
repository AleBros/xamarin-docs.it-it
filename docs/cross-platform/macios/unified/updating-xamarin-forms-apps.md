---
title: L'aggiornamento di App xamarin. Forms esistente
description: Questo documento vengono descritti i passaggi da seguire per aggiornare un'app xamarin. Forms dall'API classica all'API unificata.
ms.prod: xamarin
ms.assetid: C2F0D1D1-256D-44A4-AAC9-B06A0CB41E70
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: d5c16b034b07d3e9875412f041c16b293557438a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61211854"
---
# <a name="updating-existing-xamarinforms-apps"></a>L'aggiornamento di App xamarin. Forms esistente

_Seguire questi passaggi per aggiornare un'app xamarin. Forms esistente per usare l'API unificata e l'aggiornamento alla versione 1.3.1_

> [!IMPORTANT]
> Poiché xamarin. Forms 1.3.1 è la prima versione che supporta l'API unificata, l'intera soluzione deve essere aggiornato per usare la versione più recente nello stesso momento come la migrazione di app per iOS a unificato. Ciò significa che oltre ad aggiornare il progetto iOS per il supporto di Unified, è anche necessario modificare il codice nel _tutti_ i progetti nella soluzione.

L'aggiornamento viene eseguito in due passaggi:

1. Eseguire la migrazione di app per iOS per l'API unificata usando Visual Studio per la compilazione del Mac nello strumento di migrazione.

    - Utilizzare lo strumento di migrazione per aggiornare automaticamente il progetto.

    - Aggiornamento iOS nativo API come descritto nelle istruzioni per [aggiornare le app iOS](~/cross-platform/macios/unified/updating-ios-apps.md) (in particolar modo nel codice del servizio renderer o delle dipendenze personalizzato).

2. Aggiornare l'intera soluzione per la versione 1.3 di xamarin. Forms.

    1. Installare il pacchetto NuGet di xamarin. Forms 1.3.1.

    2. Aggiornamento di `App` classe nel codice condiviso.

    3. Aggiornamento di `AppDelegate` nel progetto iOS.

    4. Aggiornamento di `MainActivity` nel progetto Android.

    5. Aggiornamento di `MainPage` nel progetto Windows Phone.

## <a name="1-ios-app-unified-migration"></a>1. iOS App (Unified migrazione)

Parte della migrazione richiede l'aggiornamento alla versione 1.3, che supporta l'API unificata di xamarin. Forms. Affinché i riferimenti all'assembly corretto deve essere creato, è necessario innanzitutto aggiornare il progetto iOS per usare l'API unificata.

### <a name="migration-tool"></a>Strumento di migrazione

Fare clic sul progetto iOS in modo che è selezionata, quindi scegliere **progetto > eseguire la migrazione all'API unificata di xamarin. IOS...**  e accettare il messaggio di avviso che viene visualizzato.

![](updating-xamarin-forms-apps-images/beta-tool1.png "Scegli progetto > eseguire la migrazione all'API unificata di xamarin. IOS... e di accettare il messaggio di avviso che viene visualizzato")

Questo verrà automaticamente:

 - Modificare il tipo di progetto per supportare l'API unificata 64 bit.
 - Modificare il riferimento framework **xamarin. IOS** (sostituendo il vecchio **monotouch** riferimento).
 - Modificare i riferimenti di spazio dei nomi nel codice per rimuovere il `MonoTouch` prefisso.
 - Aggiorna il **csproj** file da utilizzare gli obiettivi di build corretta per l'API unificata.

**Clean** e **compilare** il progetto per assicurarsi che non siano presenti altri errori da correggere. Nessuna ulteriore azione deve essere obbligatoria. Questi passaggi sono illustrati più dettagliatamente la [documentazione API unificata](~/cross-platform/macios/unified/updating-ios-apps.md).

### <a name="update-native-ios-apis-if-required"></a>Aggiornare le API native per iOS (se richiesto)

Se è stato aggiunto il codice nativo di iOS aggiuntive (ad esempio renderer personalizzati o servizi di dipendenza) devi eseguire correzioni del codice manuale aggiuntive. Ricompilare l'app e fare riferimento al [istruzioni di App per iOS l'aggiornamento esistenti](~/cross-platform/macios/unified/updating-ios-apps.md) per altre informazioni sulle modifiche che potrebbero essere necessari. [Questi suggerimenti](~/cross-platform/macios/unified/updating-tips.md) consente anche di identificare le modifiche necessarie.

## <a name="2-xamarinforms-131-update"></a>2. Xamarin.Forms 1.3.1 Update

Una volta che l'app per iOS è stato aggiornato all'API unificata di, il resto della soluzione deve essere aggiornato a xamarin. Forms versione 1.3.1. vale a dire:

 - Aggiornamento del pacchetto NuGet di xamarin. Forms in ogni progetto.
 - La modifica del codice per usare il nuovo xamarin. Forms `Application`, `FormsApplicationDelegate` (iOS), `FormsApplicationActivity` (Android), e `FormsApplicationPage` classi (Windows Phone).

Questi passaggi sono illustrati di seguito:

### <a name="21-update-nuget-in-all-projects"></a>2.1 aggiornare NuGet in tutti i progetti

Aggiornamento di xamarin. Forms alla 1.3.1 non definitive usando Gestione pacchetti NuGet per tutti i progetti nella soluzione: Libreria di classi Portabile (se presente), iOS, Android e Windows Phone. È consigliabile che si **eliminare e aggiungere nuovamente** il pacchetto NuGet di xamarin. Forms per l'aggiornamento alla versione 1.3.

**NOTA:** È attualmente disponibile in versione di xamarin. Forms 1.3.1 *definitive*. Ciò significa che è necessario selezionare la **definitive** opzione NuGet tramite (un tick-finestra in Visual Studio per Mac) o dall'elenco a discesa in Visual Studio per visualizzare la versione più recente di versioni non definitive.

> [!IMPORTANT]
> Se si usa Visual Studio, assicurarsi che sia installata la versione più recente di gestione pacchetti NuGet. Le versioni precedenti di NuGet in Visual Studio non installerà correttamente la versione unificata di xamarin. Forms 1.3.1. Passare a **strumenti > estensioni e aggiornamenti...**  e fare clic sul **Installed** elenco per verificare che il **Gestione pacchetti NuGet per Visual Studio** almeno versione 2.8.5. Se è meno recente, fare clic sui **aggiornamenti** elenco per scaricare la versione più recente.

Dopo aver aggiornato il pacchetto NuGet per xamarin. Forms 1.3.1, apportare le modifiche seguenti in ogni progetto per eseguire l'aggiornamento al nuovo `Xamarin.Forms.Application` classe.

### <a name="22-portable-class-library-or-shared-project"></a>2.2 libreria di classi portabile (o progetto condiviso)

Modifica il **App.cs** file in modo che:

 - Il `App` classe ora eredita da `Application`.
 - Il `MainPage` viene impostata per la prima pagina di contenuto che si desidera visualizzare.

```csharp
public class App : Application // superclass new in 1.3
{
    public App ()
    {
        // The root page of your application
        MainPage = new ContentPage {...}; // property new in 1.3
    }
```

È stata completamente rimossa la `GetMainPage` metodo e invece impostare il `MainPage` *proprietà* sul `Application` sottoclasse.

Questa nuova `Application` supporta anche la classe di base di `OnStart`, `OnSleep`, e `OnResume` gli override per semplificare la gestione del ciclo di vita dell'applicazione.

Il `App` classe viene quindi passata a un nuovo `LoadApplication` metodo in ogni progetto di app, come descritto di seguito:

### <a name="23-ios-app"></a>App 2.3 iOS

Modifica il **AppDelegate.cs** file in modo che:

 - La classe eredita da `FormsApplicationDelegate` (invece di `UIApplicationDelegate` in precedenza).
 - `LoadApplication` viene chiamato con una nuova istanza della `App`.

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate :
    global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate // superclass new in 1.3
{
    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        global::Xamarin.Forms.Forms.Init ();

        LoadApplication (new App ());  // method is new in 1.3

        return base.FinishedLaunching (app, options);
    }
}
```

### <a name="23-android-app"></a>2.3 App android

Modifica il **MainActivity.cs** file in modo che:

 - La classe eredita da `FormsApplicationActivity` (invece di `FormsActivity` in precedenza).
 - `LoadApplication` viene chiamato con una nuova istanza di `App`

```csharp
[Activity (Label = "YOURAPPNAM", Icon = "@drawable/icon", MainLauncher = true,
ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
public class MainActivity :
    global::Xamarin.Forms.Platform.Android.FormsApplicationActivity // superclass new in 1.3
{
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        global::Xamarin.Forms.Forms.Init (this, bundle);

        LoadApplication (new App ()); // method is new in 1.3
    }
}
```

### <a name="24-windows-phone-app"></a>2.4 App di Windows Phone

È necessario aggiornare il **MainPage** -sia il XAML e code-behind.

Modifica il **MainPage. XAML** file in modo che:

 - Elemento radice XAML dovrebbe essere `winPhone:FormsApplicationPage`.
 - Il `xmlns:phone` attributo deve essere *modificato* a `xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"`

Seguito è riportato un esempio aggiornato, è necessario solo modificare questi elementi (il resto degli attributi deve rimanere invariato):

```xml
<winPhone:FormsApplicationPage
   ...
   xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"
    ...>
</winPhone:FormsApplicationPage>
```

Modifica il **MainPage.xaml.cs** file in modo che:

 - La classe eredita da `FormsApplicationPage` (invece di `PhoneApplicationPage` in precedenza).
 - `LoadApplication` viene chiamato con una nuova istanza di xamarin. Forms `App` classe. Potrebbe essere necessario qualificare completamente il riferimento poiché Windows Phone ha la propria `App` classe già definito.

```csharp
public partial class MainPage : global::Xamarin.Forms.Platform.WinPhone.FormsApplicationPage // superclass new in 1.3
{
    public MainPage()
    {
        InitializeComponent();
        SupportedOrientations = SupportedPageOrientation.PortraitOrLandscape;

        global::Xamarin.Forms.Forms.Init();
        LoadApplication(new YOUR_APP_NAMESPACE.App()); // new in 1.3
    }
 }
```

### <a name="troubleshooting"></a>Risoluzione dei problemi

Si trova talvolta un errore simile al seguente dopo aver aggiornato il pacchetto NuGet di xamarin. Forms. Si verifica quando lo strumento di aggiornamento di NuGet non abbia rimosso completamente i riferimenti alle versioni precedenti dalle **csproj** file.

>IL\_PROJECT.csproj: Errore: Questo progetto fa riferimento a pacchetti NuGet che non sono presenti in questo computer. Abilita ripristino dei pacchetti NuGet per scaricarli.  Per altre informazioni, vedere http://go.microsoft.com/fwlink/?LinkID=322105. Il file mancante è... /.. /Packages/Xamarin.Forms.1.2.3.6257/build/Portable-Win+net45+wp80+MonoAndroid10+MonoTouch10/Xamarin.Forms.targets. (IL\_PROGETTO)

Per risolvere questi errori, aprire il **csproj** del file in un editor di testo e cercare `<Target` gli elementi che fanno riferimento a versioni precedenti di xamarin. Forms, ad esempio l'elemento mostrato di seguito. È consigliabile eliminare manualmente l'intero elemento dal **csproj** file e salvare le modifiche.

```csharp
  <Target Name="EnsureNuGetPackageBuildImports" BeforeTargets="PrepareForBuild">
    <PropertyGroup>
      <ErrorText>This project references NuGet package(s) that are missing on this computer. Enable NuGet Package Restore to download them.  For more information, see http://go.microsoft.com/fwlink/?LinkID=322105. The missing file is {0}.</ErrorText>
    </PropertyGroup>
    <Error Condition="!Exists('..\..\packages\Xamarin.Forms.1.2.3.6257\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10\Xamarin.Forms.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\packages\Xamarin.Forms.1.2.3.6257\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10\Xamarin.Forms.targets'))" />
  </Target>
```

Il progetto dovrebbe essere compilato correttamente dopo aver rimosso questi riferimenti precedenti.

## <a name="considerations"></a>Considerazioni

Le considerazioni seguenti devono tener conto durante la conversione di un progetto xamarin. Forms esistente dall'API classica alla nuova API unificata che l'app si basa su uno o più componenti o un pacchetto NuGet.

### <a name="components"></a>Componenti

Qualsiasi componente che sono stati inclusi nell'applicazione dovrà anche essere aggiornata all'API unificata di caso, verrà generato un conflitto quando si prova a compilare. Per qualsiasi componente incluso, sostituire la versione corrente con una nuova versione da Store il componente Xamarin che supporta l'API unificata ed eseguire una compilazione pulita. Qualsiasi componente che non è ancora stato convertito dall'autore, verrà visualizzato un a 32 bit solo avviso nell'archivio del componente.

### <a name="nuget-support"></a>Supporto NuGet

Anche se si ha contribuito con le modifiche apportate a NuGet per lavorare con il supporto per l'API unificata, non è stata una nuova versione di NuGet, in modo che si sta la valutazione di come ottenere NuGet per riconoscere le nuove API.

Fino a quel momento, proprio come i componenti, è necessario passare i pacchetti NuGet sono stati inclusi nel progetto per una versione che supporta le API unificata ed eseguire una compilazione pulita in un secondo momento.

> [!IMPORTANT]
> Se si dispone di un errore nel modulo _"errore 3 non può includere sia 'monotouch' e 'Dll' nello stesso progetto xamarin. ios: 'Dll' viene fatto riferimento in modo esplicito, mentre 'monotouch' fa riferimento ' xxx ', versione = 0.0.000, Culture = neutral, PublicKeyToken = null'"_ dopo avere convertito l'applicazione per le API unificata, si tratta in genere con un componente o pacchetto NuGet nel progetto che non è stato aggiornato all'API unificata. È necessario rimuovere il componente/NuGet esistenti, aggiornare a una versione che supporta le API unificata ed eseguire una compilazione pulita.

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Abilitazione a 64 Bit le compilazioni di App xamarin. IOS

Per un'applicazione per dispositivi mobili xamarin. IOS che è stata convertita per l'API unificata, lo sviluppatore deve comunque abilitare la compilazione dell'applicazione per i computer a 64 bit delle opzioni dell'app. Vedere la **abilitazione Bit 64 build dell'App xamarin. IOS** delle [considerazioni sulle piattaforme a 32 o 64 bit](~/cross-platform/macios/32-and-64/index.md#enable-64) compilazioni di documento per istruzioni dettagliate sull'abilitazione di a 64 bit.

## <a name="summary"></a>Riepilogo

L'applicazione xamarin. Forms ora deve essere aggiornata alla versione 1.3.1 e l'app per iOS eseguita la migrazione all'API unificata (che supporta le architetture a 64 bit nella piattaforma iOS).

Come indicato in precedenza, se l'app xamarin. Forms include codice nativo, ad esempio renderer personalizzati o delle dipendenze dei servizi quindi si potrebbe essere necessario anche aggiornare per usare i nuovi tipi [introdotto in API unificata di](~/cross-platform/macios/index.md).

## <a name="related-links"></a>Collegamenti correlati

- [L'aggiornamento delle App iOS](~/cross-platform/macios/unified/updating-apps.md)
- [L'aggiornamento delle App iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Utilizzo di tipi nativi nelle app multipiattaforma](~/cross-platform/macios/native-types-cross-platform.md)
- [L'aggiornamento di suggerimenti](~/cross-platform/macios/unified/updating-tips.md)
- [Visual Studio classico differenze API unificata](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
