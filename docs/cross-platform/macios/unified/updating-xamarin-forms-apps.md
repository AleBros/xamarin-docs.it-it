---
title: L'aggiornamento di App xamarin. Forms esistente
description: Seguire questi passaggi per aggiornare un'app xamarin. Forms esistente per usare l'API unificata e l'aggiornamento alla versione 1.3.1
ms.topic: article
ms.prod: xamarin
ms.assetid: C2F0D1D1-256D-44A4-AAC9-B06A0CB41E70
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 48b8d1cf8e6242fde632bceec5d482f53037a954
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="updating-existing-xamarinforms-apps"></a>L'aggiornamento di App xamarin. Forms esistente

_Seguire questi passaggi per aggiornare un'app xamarin. Forms esistente per usare l'API unificata e l'aggiornamento alla versione 1.3.1_

> [!IMPORTANT]
> Poiché xamarin. Forms 1.3.1 è la prima versione che supporta l'API unificata, l'intera soluzione deve essere aggiornato per utilizzare la versione più recente contemporaneamente la migrazione dell'app iOS a unificato. Ciò significa che oltre ad aggiornare il progetto iOS per il supporto di Unified, è inoltre necessario modificare il codice in _tutti_ i progetti nella soluzione.

L'aggiornamento viene eseguito in due passaggi:

1. Eseguire la migrazione dell'app iOS con Visual Studio per la generazione del Mac nello strumento di migrazione Unified API.

    - Utilizzare lo strumento di migrazione per aggiornare automaticamente il progetto.

    - Aggiornamento iOS nativo API come descritto nelle istruzioni per [aggiornare le app iOS](~/cross-platform/macios/unified/updating-ios-apps.md) (in particolare nel codice del servizio renderer o dipendenza personalizzato).

2. Aggiornare l'intera soluzione xamarin. Forms versione 1.3.

    1. Installare il pacchetto NuGet di xamarin. Forms 1.3.1.

    2. Aggiornamento di `App` classe nel codice condiviso.

    3. Aggiornamento di `AppDelegate` nel progetto iOS.

    4. Aggiornamento di `MainActivity` nel progetto Android.

    5. Aggiornamento di `MainPage` nel progetto Windows Phone.

## <a name="1-ios-app-unified-migration"></a>1. un'App iOS (Unified migrazione)

Parte della migrazione è necessario l'aggiornamento alla versione 1.3, che supporta l'API unificata di xamarin. Forms. Affinché i riferimenti di assembly corretto da creare, è innanzitutto necessario aggiornare il progetto iOS per utilizzare l'API unificata.

### <a name="migration-tool"></a>Strumento di migrazione

Fare clic sul progetto iOS in modo che questa opzione è selezionata, quindi scegliere **progetto > eseguire la migrazione a xamarin. IOS Unified API...**  e accettare il messaggio di avviso che viene visualizzato.

![](updating-xamarin-forms-apps-images/beta-tool1.png "Scegliere progetto > eseguire la migrazione all'API unificata di xamarin. IOS... e accettare il messaggio di avviso che viene visualizzato")

Questo verrà automaticamente:

 - Modificare il tipo di progetto per supportare l'API unificata 64 bit.
 - Modificare il riferimento framework **xamarin** (sostituendo il vecchio **monotouch** riferimento).
 - Modificare i riferimenti di spazio dei nomi nel codice per rimuovere il `MonoTouch` prefisso.
 - Aggiornamento di **csproj** file da utilizzare le destinazioni di compilazione corretta per l'API unificata.

**Pulisci** e **compilare** il progetto per assicurarsi che non siano presenti altri errori da correggere. Nessuna ulteriore azione deve essere obbligatoria. Questi passaggi sono illustrati in dettaglio nel [documenti Unified API](~/cross-platform/macios/unified/updating-ios-apps.md).

### <a name="update-native-ios-apis-if-required"></a>Aggiornare le API native per iOS (se richiesto)

Se è stato aggiunto il codice nativo di iOS aggiuntive (ad esempio un renderer personalizzato o servizi di dipendenza) occorre eseguire le correzioni del codice manuale aggiuntive. Ricompilare l'app e fare riferimento al [aggiornamento esistenti iOS istruzioni app](~/cross-platform/macios/unified/updating-ios-apps.md) per ulteriori informazioni sulle modifiche che potrebbero essere necessari. [Questi suggerimenti](~/cross-platform/macios/unified/updating-tips.md) consentirà inoltre di identificare le modifiche necessarie.

## <a name="2-xamarinforms-131-update"></a>2. Xamarin.Forms 1.3.1 Update

Una volta l'app per iOS è stata aggiornata a Unified API, il resto della soluzione deve essere aggiornato a una versione 1.3.1 di xamarin. Forms. vale a dire:

 - Aggiornamento del pacchetto NuGet di xamarin. Forms in ogni progetto.
 - Modifica del codice per utilizzare il nuovo xamarin. Forms `Application`, `FormsApplicationDelegate` (iOS), `FormsApplicationActivity` (Android), e `FormsApplicationPage` classi (Windows Phone).

Questi passaggi sono illustrati di seguito:

### <a name="21-update-nuget-in-all-projects"></a>2.1 aggiornare NuGet in tutti i progetti

Aggiornare xamarin. Forms per 1.3.1 versioni non definitive usando Gestione pacchetti NuGet per tutti i progetti nella soluzione: libreria di classi Portabile (se presente), iOS, Android e Windows Phone. È consigliabile che si **eliminare e aggiungere nuovamente** il pacchetto NuGet di xamarin. Forms per l'aggiornamento alla versione 1.3.

**Nota:** è attualmente in xamarin. Forms versione 1.3.1 *definitive*. Ciò significa che è necessario selezionare il **definitive** opzione NuGet tramite (un segno di graduazione-box in Visual Studio per Mac) o un-elenco a discesa in Visual Studio per visualizzare la versione più recente di pre-release.

> [!IMPORTANT]
> Se si utilizza Visual Studio, verificare che la versione più recente di gestione pacchetti NuGet è installata. Le versioni precedenti di NuGet in Visual Studio non installerà correttamente la versione unificato di xamarin. Forms 1.3.1. Passare a **strumenti > estensioni e aggiornamenti...**  e fare clic su di **installato** elenco per verificare che il **Gestione pacchetti NuGet per Visual Studio** almeno versione 2.8.5. Se è meno recente, fare clic su di **aggiornamenti** elenco per scaricare la versione più recente.

Dopo avere aggiornato il pacchetto NuGet di xamarin. Forms 1.3.1, apportare le modifiche seguenti in ogni progetto per eseguire l'aggiornamento al nuovo `Xamarin.Forms.Application` classe.

### <a name="22-portable-class-library-or-shared-project"></a>2.2 libreria di classi portabile (o progetto condiviso)

Modifica il **app. cs** file in modo che:

 - Il `App` ora eredita da `Application`.
 - Il `MainPage` proprietà è impostata per la prima pagina di contenuto che si desidera visualizzare.

```csharp
public class App : Application // superclass new in 1.3
{
    public App ()
    {
        // The root page of your application
        MainPage = new ContentPage {...}; // property new in 1.3
    }
```

È stata rimossa completamente il `GetMainPage` (metodo) e impostare invece il `MainPage` *proprietà* sul `Application` sottoclasse.

Questa nuova `Application` supporta anche la classe di base di `OnStart`, `OnSleep`, e `OnResume` sostituzioni per la gestione del ciclo di vita dell'applicazione.

Il `App` classe viene quindi passata a un nuovo `LoadApplication` metodo in ogni progetto di app, come descritto di seguito:

### <a name="23-ios-app"></a>App 2.3 iOS

Modifica il **appdelegate. cs** file in modo che:

 - La classe eredita da `FormsApplicationDelegate` (anziché `UIApplicationDelegate` in precedenza).
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

Modifica il **Mainactivity** file in modo che:

 - La classe eredita da `FormsApplicationActivity` (anziché `FormsActivity` in precedenza).
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

È necessario aggiornare il **MainPage** -sia il codice XAML e il codebehind.

Modifica il **MainPage. XAML** file in modo che:

 - Elemento radice XAML deve essere `winPhone:FormsApplicationPage`.
 - Il `xmlns:phone` attributo deve essere *modificato* per `xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"`

Seguito è riportato un esempio aggiornato: è necessario modificare questi elementi (il resto degli attributi deve rimanere invariate):

```xml
<winPhone:FormsApplicationPage
   ...
   xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"
    ...>
</winPhone:FormsApplicationPage>
```

Modifica il **MainPage.xaml.cs** file in modo che:

 - La classe eredita da `FormsApplicationPage` (anziché `PhoneApplicationPage` in precedenza).
 - `LoadApplication` viene chiamato con una nuova istanza di xamarin. Forms `App` classe. Potrebbe essere necessario qualificare completamente il riferimento perché Windows Phone è proprio `App` classe già definito.

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

In alcuni casi si verrà visualizzato un errore simile al seguente dopo l'aggiornamento del pacchetto NuGet di xamarin. Forms. Si verifica quando il programma di aggiornamento di NuGet non elimina completamente i riferimenti alle versioni meno recenti dal **csproj** file.

>IL\_PROJECT.csproj: errore: il progetto fa riferimento a pacchetti NuGet che non sono presenti in questo computer. Abilitare il ripristino del pacchetto NuGet per scaricarli.  Per ulteriori informazioni, vedere http://go.microsoft.com/fwlink/?LinkID=322105. Il file mancante è... /.. /Packages/Xamarin.Forms.1.2.3.6257/build/Portable-Win+net45+wp80+MonoAndroid10+MonoTouch10/Xamarin.Forms.targets. (IL\_PROGETTO)

Per risolvere questi errori, aprire il **csproj** file in un editor di testo e cercare `<Target` elementi che fanno riferimento alle versioni precedenti di xamarin. Forms, ad esempio l'elemento riportato di seguito. È necessario eliminare manualmente l'intero elemento dal **csproj** e salvare le modifiche.

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

Le considerazioni seguenti devono tener conto durante la conversione di un progetto xamarin. Forms esistente dall'API classica alla nuova API unificata che app si basa su uno o più componenti o pacchetto NuGet.

### <a name="components"></a>Componenti

Qualsiasi componente che sono stati inclusi nell'applicazione sarà necessario aggiornare a Unified API o verrà generato un conflitto quando si tenta di compilare. Per qualsiasi componente incluso, sostituire la versione corrente con una nuova versione dall'archivio di componenti di Xamarin che supporta l'API unificata ed eseguire una compilazione pulita. Qualsiasi componente che non è ancora stato convertito dall'autore, verrà visualizzato un a 32 bit solo avviso nell'archivio del componente.

### <a name="nuget-support"></a>Supporto NuGet

Mentre si hanno contribuito modifiche a NuGet per funzionare con il supporto di Unified API, non è stata una nuova versione di NuGet, pertanto ci stiamo la valutazione come ottenere NuGet di riconoscere le nuove API.

Fino a quel momento, come i componenti, è necessario passare tutti i pacchetti NuGet sono stati inclusi nel progetto per una versione che supporta le API unificata ed eseguire una compilazione pulita in un secondo momento.

> [!IMPORTANT]
> **Nota:** se si dispone di un errore nel modulo _"errore 3 non può includere sia 'monotouch.dll' e 'Xamarin.iOS.dll' nello stesso progetto xamarin. IOS - 'Xamarin.iOS.dll' viene fatto riferimento in modo esplicito, mentre 'monotouch.dll' fa riferimento ' xxx, Versione = 0.0.000, Culture = neutral, PublicKeyToken = null'"_ dopo la conversione dell'applicazione per le API unificata, è in genere a causa del componente o un pacchetto NuGet nel progetto che non è stato aggiornato per l'API unificata. È necessario rimuovere il componente/NuGet esistenti, aggiornare a una versione che supporta le API unificata ed eseguire una compilazione pulita.

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Abilitazione a 64 Bit build di App xamarin

Per un'applicazione per dispositivi mobili di xamarin che è stata convertita in Unified API, lo sviluppatore deve comunque consentono di creare l'applicazione per computer a 64 bit delle opzioni dell'applicazione. Vedere il **abilitazione Bit 64 compilazioni di App xamarin** del [considerazioni relative alla piattaforma a 32/64 bit](~/cross-platform/macios/32-and-64/index.md#enable-64) documenti per istruzioni dettagliate sull'abilitazione di a 64 bit.

## <a name="summary"></a>Riepilogo

L'applicazione di xamarin. Forms dovrebbe ora essere aggiornata alla versione 1.3.1 e l'app iOS eseguita la migrazione all'API unificata (che supporta le architetture a 64 bit nella piattaforma iOS).

Come indicato in precedenza, se l'app xamarin. Forms include codice nativo, ad esempio un renderer personalizzato o servizi di dipendenza, quindi può essere anche necessario aggiornare per utilizzare i nuovi tipi [introdotti nell'API unificata](~/cross-platform/macios/index.md).

## <a name="related-links"></a>Collegamenti correlati

- [Aggiornamento delle App iOS](~/cross-platform/macios/unified/updating-apps.md)
- [Aggiornamento delle App iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Utilizzo di tipi nativi nelle app multipiattaforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Aggiornamento dei suggerimenti](~/cross-platform/macios/unified/updating-tips.md)
- [Differenze tra API unificata di vs classico](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
