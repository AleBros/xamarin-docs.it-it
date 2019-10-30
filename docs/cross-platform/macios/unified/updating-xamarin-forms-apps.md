---
title: Aggiornamento delle app Novell. Forms esistenti
description: Questo documento descrive i passaggi da seguire per aggiornare un'app Novell. Forms dal API classica al API unificata.
ms.prod: xamarin
ms.assetid: C2F0D1D1-256D-44A4-AAC9-B06A0CB41E70
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: dad1b7173e302931455887fdaa4730347f0e5e55
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015007"
---
# <a name="updating-existing-xamarinforms-apps"></a>Aggiornamento delle app Novell. Forms esistenti

_Seguire questa procedura per aggiornare un'app Novell. Forms esistente per usare il API unificata e l'aggiornamento alla versione 1.3.1_

> [!IMPORTANT]
> Poiché Novell. Forms 1.3.1 è la prima versione che supporta il API unificata, è necessario aggiornare l'intera soluzione in modo da usare la versione più recente allo stesso tempo della migrazione dell'app iOS a Unified. Questo significa che, oltre ad aggiornare il progetto iOS per il supporto unificato, sarà necessario modificare anche il codice in _tutti_ i progetti della soluzione.

L'aggiornamento viene eseguito in due passaggi:

1. Eseguire la migrazione dell'app iOS al API unificata usando lo strumento di migrazione della compilazione di Visual Studio per Mac.

    - Utilizzare lo strumento di migrazione per aggiornare automaticamente il progetto.

    - Aggiornare le API native iOS come descritto nelle istruzioni per l' [aggiornamento di app iOS](~/cross-platform/macios/unified/updating-ios-apps.md) (in particolare nel renderer personalizzato o nel codice del servizio di dipendenza).

2. Aggiornare l'intera soluzione a Novell. Forms versione 1,3.

    1. Installare il pacchetto NuGet Novell. Forms 1.3.1.

    2. Aggiornare la classe `App` nel codice condiviso.

    3. Aggiornare il `AppDelegate` nel progetto iOS.

    4. Aggiornare il `MainActivity` nel progetto Android.

    5. Aggiornare il `MainPage` nel progetto Windows Phone.

## <a name="1-ios-app-unified-migration"></a>1. app iOS (migrazione unificata)

Parte della migrazione richiede l'aggiornamento di Novell. Forms alla versione 1,3, che supporta il API unificata. Per poter creare i riferimenti corretti all'assembly, è necessario prima aggiornare il progetto iOS per l'uso del API unificata.

### <a name="migration-tool"></a>Strumento di migrazione

Fare clic sul progetto iOS in modo che sia selezionato, quindi scegliere **progetto > Esegui migrazione a Novell. iOS API unificata...** e accettare il messaggio di avviso visualizzato.

![](updating-xamarin-forms-apps-images/beta-tool1.png "Choose Project > Migrate to Xamarin.iOS Unified API... and agree to the warning message that appears")

Questa operazione verrà automaticamente:

- Modificare il tipo di progetto per supportare l'API Unified a 64 bit.
- Modificare il riferimento al Framework in **Novell. iOS** (sostituendo il vecchio riferimento **MonoTouch** ).
- Modificare i riferimenti allo spazio dei nomi nel codice per rimuovere il prefisso `MonoTouch`.
- Aggiornare il file **csproj** per usare le destinazioni di compilazione corrette per la API unificata.

**Pulire** e **compilare** il progetto per assicurarsi che non ci siano altri errori da correggere. Non è necessaria alcuna azione aggiuntiva. Questi passaggi sono illustrati più dettagliatamente nel [API unificata docs](~/cross-platform/macios/unified/updating-ios-apps.md).

### <a name="update-native-ios-apis-if-required"></a>Aggiornare le API iOS native (se necessario)

Se è stato aggiunto codice nativo iOS aggiuntivo, ad esempio renderer personalizzati o servizi di dipendenza, potrebbe essere necessario eseguire correzioni aggiuntive del codice manuale. Ricompilare l'applicazione e fare riferimento alle istruzioni per l' [aggiornamento di app iOS esistenti](~/cross-platform/macios/unified/updating-ios-apps.md) per ulteriori informazioni sulle modifiche che potrebbero essere necessarie. [Questi suggerimenti](~/cross-platform/macios/unified/updating-tips.md) aiuteranno anche a identificare le modifiche necessarie.

## <a name="2-xamarinforms-131-update"></a>2. Novell. Forms 1.3.1 Update

Dopo che l'app iOS è stata aggiornata alla API unificata, è necessario aggiornare il resto della soluzione a Novell. Forms versione 1.3.1. vale a dire:

- Aggiornamento del pacchetto NuGet Novell. Forms in ogni progetto.
- Modifica del codice per l'uso delle nuove classi Novell. Forms `Application`, `FormsApplicationDelegate` (iOS), `FormsApplicationActivity` (Android) e `FormsApplicationPage` (Windows Phone).

Questi passaggi sono illustrati di seguito:

### <a name="21-update-nuget-in-all-projects"></a>2,1 aggiornare NuGet in tutti i progetti

Aggiornare Novell. Forms a 1.3.1 versione preliminare usando Gestione pacchetti NuGet per tutti i progetti nella soluzione: PCL (se presente), iOS, Android e Windows Phone. Si consiglia di **eliminare e aggiungere nuovamente** il pacchetto NuGet Novell. Forms per eseguire l'aggiornamento alla versione 1,3.

> [!NOTE]
> Novell. Forms versione 1.3.1 è attualmente in versione non *definitiva*. Ciò significa che è necessario selezionare l'opzione di **versione non definitiva** in NuGet (tramite una casella di controllo in Visual Studio per Mac o in un elenco a discesa in Visual Studio) per visualizzare la versione provvisoria più recente.

> [!IMPORTANT]
> Se si usa Visual Studio, verificare che sia installata la versione più recente di gestione pacchetti NuGet. Nelle versioni precedenti di NuGet in Visual Studio la versione unificata di Novell. Forms 1.3.1 non viene installata correttamente. Passare a **strumenti > estensioni e aggiornamenti...** e fare clic sull'elenco **installato** per verificare che **Gestione pacchetti NuGet per Visual Studio** sia almeno la versione 2.8.5. Se è precedente, fare clic sull'elenco **aggiornamenti** per scaricare la versione più recente.

Dopo aver aggiornato il pacchetto NuGet a Novell. Forms 1.3.1, apportare le modifiche seguenti in ogni progetto per eseguire l'aggiornamento alla nuova classe `Xamarin.Forms.Application`.

### <a name="22-portable-class-library-or-shared-project"></a>2,2 libreria di classi portabile (o progetto condiviso)

Modificare il file **app.cs** in modo che:

- La classe `App` ora eredita da `Application`.
- La proprietà `MainPage` è impostata sulla prima pagina di contenuto che si desidera visualizzare.

```csharp
public class App : Application // superclass new in 1.3
{
    public App ()
    {
        // The root page of your application
        MainPage = new ContentPage {...}; // property new in 1.3
    }
```

Il metodo `GetMainPage` è stato rimosso completamente, quindi è stata impostata la *proprietà* `MainPage` nella sottoclasse `Application`.

Questa nuova classe di base `Application` supporta anche le sostituzioni `OnStart`, `OnSleep`e `OnResume` per semplificare la gestione del ciclo di vita dell'applicazione.

La classe `App` viene quindi passata a un nuovo metodo di `LoadApplication` in ogni progetto di app, come descritto di seguito:

### <a name="23-ios-app"></a>App iOS 2,3

Modificare il file **AppDelegate.cs** in modo che:

- La classe eredita da `FormsApplicationDelegate`, anziché `UIApplicationDelegate` precedentemente.
- `LoadApplication` viene chiamato con una nuova istanza di `App`.

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

### <a name="23-android-app"></a>App Android 2,3

Modificare il file **MainActivity.cs** in modo che:

- La classe eredita da `FormsApplicationActivity`, anziché `FormsActivity` precedentemente.
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

### <a name="24-windows-phone-app"></a>2,4 app Windows Phone

È necessario aggiornare il file **MainPage** , sia il codice XAML che quello sottostante.

Modificare il file **MainPage. XAML** in modo che:

- L'elemento XAML radice deve essere `winPhone:FormsApplicationPage`.
- È necessario *modificare* l'attributo `xmlns:phone` in `xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"`

Di seguito è riportato un esempio aggiornato: è necessario modificare solo questi elementi (gli altri attributi devono rimanere invariati):

```xml
<winPhone:FormsApplicationPage
   ...
   xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"
    ...>
</winPhone:FormsApplicationPage>
```

Modificare il file **MainPage.XAML.cs** in modo che:

- La classe eredita da `FormsApplicationPage`, anziché `PhoneApplicationPage` precedentemente.
- `LoadApplication` viene chiamato con una nuova istanza della classe Novell. Forms `App`. Potrebbe essere necessario qualificare il riferimento in modo completo poiché Windows Phone dispone di una propria classe `App` già definita.

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

### <a name="troubleshooting"></a>Troubleshooting

In alcuni casi verrà visualizzato un errore simile al seguente dopo l'aggiornamento del pacchetto NuGet Novell. Forms. Si verifica quando NuGet Updater non rimuove completamente i riferimenti alle versioni precedenti dai file **csproj** .

>IL\_PROJECT. csproj: errore: questo progetto fa riferimento ai pacchetti NuGet mancanti nel computer. Abilitare il ripristino del pacchetto NuGet per scaricarli.  Per ulteriori informazioni, vedere http://go.microsoft.com/fwlink/?LinkID=322105. Il file mancante è.. /.. /packages/Xamarin.Forms.1.2.3.6257/build/portable-win + Net45 + WP80 + MonoAndroid10 + MonoTouch10/Novell. Forms. targets. (Progetto\_)

Per correggere questi errori, aprire il file **csproj** in un editor di testo e cercare gli elementi `<Target` che fanno riferimento a versioni precedenti di Novell. Forms, ad esempio l'elemento mostrato di seguito. È necessario eliminare manualmente questo intero elemento dal file **csproj** e salvare le modifiche.

```csharp
  <Target Name="EnsureNuGetPackageBuildImports" BeforeTargets="PrepareForBuild">
    <PropertyGroup>
      <ErrorText>This project references NuGet package(s) that are missing on this computer. Enable NuGet Package Restore to download them.  For more information, see http://go.microsoft.com/fwlink/?LinkID=322105. The missing file is {0}.</ErrorText>
    </PropertyGroup>
    <Error Condition="!Exists('..\..\packages\Xamarin.Forms.1.2.3.6257\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10\Xamarin.Forms.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\packages\Xamarin.Forms.1.2.3.6257\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10\Xamarin.Forms.targets'))" />
  </Target>
```

Il progetto deve essere compilato correttamente una volta rimossi i riferimenti precedenti.

## <a name="considerations"></a>Considerazioni

Quando si converte un progetto Novell. Forms esistente dal API classica al nuovo API unificata se tale app si basa su uno o più componenti o pacchetti NuGet, tenere presente le considerazioni seguenti.

### <a name="components"></a>Componenti

Tutti i componenti inclusi nell'applicazione dovranno anche essere aggiornati al API unificata o si verifica un conflitto quando si tenta di eseguire la compilazione. Per qualsiasi componente incluso, sostituire la versione corrente con una nuova versione dall'archivio componenti di Novell che supporta il API unificata ed eseguire una compilazione pulita. Per tutti i componenti che non sono stati ancora convertiti dall'autore, verrà visualizzato un avviso solo 32 bit nell'archivio componenti.

### <a name="nuget-support"></a>Supporto NuGet

Anche se sono state apportate modifiche a NuGet per lavorare con il supporto API unificata, non è stata rilasciata una nuova versione di NuGet, quindi si sta valutando come ottenere NuGet per riconoscere le nuove API.

Fino a quel momento, come per i componenti, è necessario cambiare il pacchetto NuGet incluso nel progetto in una versione che supporta le API unificate ed eseguire una compilazione pulita in seguito.

> [!IMPORTANT]
> Se è presente un errore nel formato _"errore 3: non è possibile includere sia ' MonoTouch. dll ' che ' Novell. iOS. dll ' nello stesso progetto Novell. iOS. viene fatto riferimento in modo esplicito a' Novell. iOS. dll ', mentre a' MonoTouch. dll ' fa riferimento ' xxx, Version = 0.0.000, culture = neutral, PublicKeyToken = null ' "_ dopo la conversione dell'applicazione nelle API unificate, è in genere dovuto alla presenza di un componente o di un pacchetto NuGet nel progetto che non è stato aggiornato al API unificata. È necessario rimuovere il componente/NuGet esistente, eseguire l'aggiornamento a una versione che supporta le API unificate ed eseguire una compilazione pulita.

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Abilitazione delle build a 64 bit delle app Novell. iOS

Per un'applicazione per dispositivi mobili Novell. iOS che è stata convertita nel API unificata, lo sviluppatore deve comunque abilitare la compilazione dell'applicazione per i computer a 64 bit dalle opzioni dell'app. Per istruzioni dettagliate sull'abilitazione di compilazioni a 64 bit, vedere la pagina relativa all' **Abilitazione delle build a 64 bit delle app Novell. iOS** del documento sulle [considerazioni sulla piattaforma di bit 32/64](~/cross-platform/macios/32-and-64/index.md#enable-64)

## <a name="summary"></a>Riepilogo

L'applicazione Novell. Forms deve ora essere aggiornata alla versione 1.3.1 e all'app iOS migrata al API unificata (che supporta le architetture a 64 bit sulla piattaforma iOS).

Come indicato in precedenza, se l'app Novell. Forms include codice nativo, ad esempio renderer personalizzati o servizi di dipendenza, potrebbe essere necessario aggiornare anche per usare i nuovi tipi [introdotti nella API unificata](~/cross-platform/macios/index.md).

## <a name="related-links"></a>Collegamenti correlati

- [Aggiornamento di app iOS](~/cross-platform/macios/unified/updating-apps.md)
- [Aggiornamento di app iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Utilizzo di tipi nativi nelle app multipiattaforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Aggiornamento suggerimenti](~/cross-platform/macios/unified/updating-tips.md)
- [Differenze rispetto a API unificata classiche](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
