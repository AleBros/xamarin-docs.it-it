---
title: Implementazione di SiriKit in xamarin. IOS
description: Questo documento descrive i passaggi necessari per implementare il supporto di SiriKit in un'App xamarin. IOS. Illustra le estensioni di Intent e interfaccia utente Intent.
ms.prod: xamarin
ms.assetid: 20FFB981-EB10-48BA-BF79-40F37F0291EB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 05/03/2018
ms.openlocfilehash: ea037aaaac97d9f326f1a2fbcb28d97c9d8a9b45
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50110251"
---
# <a name="implementing-sirikit-in-xamarinios"></a>Implementazione di SiriKit in xamarin. IOS

_Questo articolo illustra i passaggi necessari per implementare il supporto di SiriKit in un'App xamarin. IOS._

Nuovo ai dispositivi iOS 10, SiriKit consente a un'app xamarin. IOS fornire servizi accessibili all'utente tramite Siri e all'app mappe su un dispositivo iOS. Questo articolo illustra i passaggi necessari per implementare il supporto di SiriKit nelle App xamarin. IOS aggiungendo le estensioni necessarie Intent, estensioni dell'interfaccia utente Intent e vocabolario.

Siri funziona con il concetto di **domini**, gruppi di conoscono le azioni per le attività correlate. Ogni interazione con l'app con Siri deve rientrare in uno dei domini del servizio noto come indicato di seguito:

- Audio o video chiamata.
- Una questione di prenotazione.
- La gestione di allenamenti.
- Messaggistica.
- Ricerca di foto.
- Inviare o ricevere i pagamenti.

Quando l'utente effettua una richiesta di Siri con uno dei servizi dell'estensione App, SiriKit invia l'estensione di un **finalità** oggetto che descrive la richiesta dell'utente insieme a eventuali dati di supporto. l'estensione dell'App, quindi genera appropriato **risposta** dell'oggetto per il determinato **finalità**, che riporta in dettaglio come l'estensione può gestire la richiesta.

Questa guida presenta un rapido esempio di incluso il supporto di SiriKit in un'app esistente. Ai fini di questo esempio verrà usato l'app MonkeyChat fittizio:

[![](implementing-sirikit-images/monkeychat01.png "L'icona MonkeyChat")](implementing-sirikit-images/monkeychat01.png#lightbox)

MonkeyChat mantiene il proprio contatto libro di amici dell'utente, ognuno associato a un nome della schermata (ad esempio Bobo, ad esempio) e consente all'utente di inviare testo chat a ogni amico in base al nome della schermata.

## <a name="extending-the-app-with-sirikit"></a>Estensione dell'App con SiriKit

Come illustrato nella [informazioni sui concetti di SiriKit](~/ios/platform/sirikit/understanding-sirikit.md) Guida, sono interessati a estendere un'app con SiriKit tre parti principali:

[![](implementing-sirikit-images/elements01.png "Estensione dell'App con il diagramma di SiriKit")](implementing-sirikit-images/elements01.png#lightbox)

Sono inclusi:

1. **Estensione per Intent** -verificherà le risposte degli utenti, conferma che l'app può gestire la richiesta ed esegue effettivamente l'attività per soddisfare la richiesta dell'utente.
2. **Estensione per interfaccia utente Intent** - *facoltativo*, fornisce un'interfaccia utente personalizzata per le risposte nell'ambiente di Siri e può visualizzare le App dell'interfaccia utente e della personalizzazione in Siri per arricchire l'esperienza dell'utente.
3. **App** -fornisce l'App con i vocabolari specifici utente per facilitare l'uso con esso Siri. 

Tutti questi elementi e i passaggi per includerli nell'app verrà descritto dettagliatamente nelle sezioni seguenti.


## <a name="preparing-the-app"></a>Preparare l'App

SiriKit si basa sulle estensioni, tuttavia, prima di aggiungere tutte le estensioni per l'app, esistono alcuni aspetti che lo sviluppatore deve fare per facilitare l'adozione di SiriKit.

### <a name="moving-common-shared-code"></a>Lo spostamento di codice condiviso comune 

In primo luogo, lo sviluppatore può spostare parte del codice più comune che verrà condivise tra l'app e le estensioni in uno _progetti condivisi_, _librerie di classi portabili (librerie di classi portabili)_ o _nativo Librerie_.

Le estensioni dovranno essere in grado di eseguire tutte le operazioni che esegue l'app. In termini di app di esempio MonkeyChat cose come trovare i contatti, aggiunta di nuovi contatti, inviare messaggi e recuperare la cronologia dei messaggi.

Spostando il codice comune in un progetto condiviso, libreria di classi Portabile o una raccolta nativa perché semplifica la manutenzione del codice in un'unica posizione comune e garantisce che l'estensione e l'app padre forniscono uniforme esperienze e funzionalità per l'utente.

Nel caso dell'app di esempio MonkeyChat, i modelli di dati e codice per l'elaborazione, ad esempio l'accesso di rete e il database verrà spostati in una libreria nativa.

Seguire questa procedura:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Avviare Visual Studio per Mac e aprire l'app MonkeyChat.
2. Fare clic sul nome della soluzione nel **riquadro della soluzione** e selezionare **Add** > **nuovo progetto...** : 

    [![](implementing-sirikit-images/prep01.png "Aggiungere un nuovo progetto")](implementing-sirikit-images/prep01.png#lightbox)
3. Selezionare **iOS** > **libreria** > **libreria di classi** e fare clic su di **successivo** pulsante: 

    [![](implementing-sirikit-images/prep02.png "Selezionare una libreria di classi")](implementing-sirikit-images/prep02.png#lightbox)
4. Immettere `MonkeyChatCommon` per il **Name** e fare clic sui **Create** pulsante: 

    [![](implementing-sirikit-images/prep03.png "Immettere nome MonkeyChatCommon")](implementing-sirikit-images/prep03.png#lightbox)
5. Fare clic sui **riferimenti** cartella dell'app principale nella **Esplora soluzioni** e selezionare **Modifica riferimenti...** . Verificare i **MonkeyChatCommon** sul progetto e scegliere il **OK** pulsante: 

    [![](implementing-sirikit-images/prep05.png "Archiviare il progetto MonkeyChatCommon")](implementing-sirikit-images/prep05.png#lightbox)
6. Nel **Esplora soluzioni**, trascinare il codice comune condiviso dall'app principale per la libreria nativa.
7. Nel caso di MonkeyChat, trascinare il **DataModels** e **processori** cartelle dall'app principale nella libreria nativa: 

    [![](implementing-sirikit-images/prep06.png "Le cartelle DataModels e processori in Esplora soluzioni")](implementing-sirikit-images/prep06.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Avviare Visual Studio e aprire l'app MonkeyChat.
2. Fare clic sul nome della soluzione nel **Esplora soluzioni** e selezionare **Add** > **nuovo progetto...** .
3. Selezionare **Visual C#**   >  **progetto condiviso** e scegliere il **Avanti** pulsante: 

    [![](implementing-sirikit-images/prep02.w157-sml.png "Selezionare una libreria di classi")](implementing-sirikit-images/prep02.w157.png#lightbox)
4. Immettere `MonkeyChatCommon` per il **Name** e fare clic sui **Create** pulsante.
5. Fare clic sui **riferimenti** cartella dell'app principale nella **Esplora soluzioni** e selezionare **Modifica riferimenti...** . Verificare i **MonkeyChatCommon** sul progetto e scegliere il **OK** pulsante: 

    [![](implementing-sirikit-images/prep05w.png "Archiviare il progetto MonkeyChatCommon")](implementing-sirikit-images/prep05w.png#lightbox)
6. Nel **Esplora soluzioni**, trascinare il codice comune condiviso dall'app principale nel progetto condiviso.
7. Nel caso di MonkeyChat, trascinare il **DataModels** e **processori** cartelle dall'app principale nella libreria nativa.

-----

Modificare i file che sono state spostate nella libreria nativa e lo spazio dei nomi affinché corrisponda a quello della libreria. Ad esempio, modificando `MonkeyChat` a `MonkeyChatCommon`:

```csharp
using System;
namespace MonkeyChatCommon
{
    /// <summary>
    /// A message sent from one user to another within a conversation.
    /// </summary>
    public class MonkeyMessage
    {
        public MonkeyMessage ()
        {
        }
        ...
    }
}
```

Successivamente, tornare all'app principale e aggiungere un `using` istruzione per spazio dei nomi della libreria nativa in qualsiasi punto l'app Usa una delle classi che sono state spostate:

```csharp
using System;
using System.Collections.Generic;
using UIKit;
using Foundation;
using CoreGraphics;
using MonkeyChatCommon;

namespace MonkeyChat
{
    public partial class MasterViewController : UITableViewController
    {
        public DetailViewController DetailViewController { get; set; }

        DataSource dataSource;
        ...
    }
}
```

### <a name="architecting-the-app-for-extensions"></a>Progettazione di App per le estensioni

In genere, un'app effettuerà l'iscrizione per finalità più e lo sviluppatore deve assicurarsi che l'app è progettato per il numero appropriato di estensioni di Intent.

Nel caso in cui un'app richiede più di un Intent, lo sviluppatore ha la possibilità di tutta la gestione preventivo inserimento in un'unica estensione di finalità o la creazione di un'estensione per Intent separato per ogni scopo.

Se si sceglie di creare un'estensione per Intent separato per ogni scopo, lo sviluppatore è stato possibile duplicare una grande quantità di codice boilerplate in ogni estensione e creare una grande quantità di risorse del processore e memoria.

Per consentire di scegliere tra le due opzioni, vedere se una delle finalità naturalmente formano insieme. Ad esempio, un'app che ha effettuato le chiamate audio e video potrebbe voler includere entrambi tali finalità in un'unica estensione finalità come che devono svolgere attività simili e può fornire il riutilizzo di codice la maggior parte.

Per qualsiasi scopo o il gruppo di Intent che non rientrano in un gruppo esistente, creare una nuova estensione per Intent nella soluzione dell'app per contenerli.


### <a name="setting-the-required-entitlements"></a>Impostare gli Entitlement necessari

Tutte le app xamarin. IOS che include l'integrazione di SiriKit, devono avere i diritti corretti impostato. Se lo sviluppatore non impostata correttamente questi Entitlement necessari, non saranno in grado di installare o testare le app in iOS reali 10 (o versione successiva) hardware, che è anche requisito poiché iOS 10 simulatore nepodporuje funkci SiriKit.

Seguire questa procedura:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Fare doppio clic il `Entitlements.plist` del file nei **Esplora soluzioni** per aprirlo e modificarlo.
2. Passare al **origine** scheda.
3. Aggiungere il `com.apple.developer.siri` **proprietà**, impostare il **tipo** a `Boolean` e il **valore** a `Yes`: 

    [![](implementing-sirikit-images/setup01.png "Aggiungere la proprietà com.apple.developer.siri")](implementing-sirikit-images/setup01.png#lightbox)
4. Salvare le modifiche apportate al file.
5. Fare doppio clic il **File di progetto** nel **Esplora soluzioni** per aprirlo e modificarlo.
6. Selezionare **firma del Bundle iOS** e assicurarsi che le `Entitlements.plist` Seleziona file nei **Entitlement personalizzati** campo: 

    [![](implementing-sirikit-images/setup02.png "Selezionare il file entitlements. plist nel campo Custom Entitlement")](implementing-sirikit-images/setup02.png#lightbox)
7. Fare clic su **OK** per salvare le modifiche.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Fare doppio clic il `Entitlements.plist` del file nei **Esplora soluzioni** per aprirlo e modificarlo.
3. Aggiungere il `com.apple.developer.siri` **proprietà**, impostare il **tipo** a `Boolean` e il **valore** a `Yes`: 

    [![](implementing-sirikit-images/setup01w.png "Aggiungere la proprietà com.apple.developer.siri")](implementing-sirikit-images/setup01w.png#lightbox)
4. Salvare le modifiche apportate al file.
5. Fare doppio clic il **File di progetto** nel **Esplora soluzioni** per aprirlo e modificarlo.
6. Selezionare **firma del Bundle iOS** e assicurarsi che le `Entitlements.plist` Seleziona file nei **Entitlement personalizzati** campo.

-----

Al termine, l'app `Entitlements.plist` file dovrebbe essere simile al seguente (in aperto in un editor esterno):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>com.apple.developer.siri</key>
    <true/>
</dict>
</plist>
```

### <a name="correctly-provisioning-the-app"></a>Correttamente il Provisioning dell'App

A causa di rigide normative di sicurezza che Apple ha inserito tutto il framework di SiriKit, tutte le app xamarin. IOS che implementa SiriKit _necessario_ hanno l'ID App e i diritti (vedere la sezione precedente) corretta e deve essere firmato con una corretta Profilo di provisioning.

Eseguire le operazioni seguenti nel computer Mac:

1. In un web browser, passare a [ http://developer.apple.com ](http://developer.apple.com) e accedere al proprio account.
2. Fare clic su **certificati**, **identificatori** e **profili**.
3. Selezionare **profili di Provisioning** e selezionare **App ID**, quindi fare clic sui **+** pulsante.
4. Immettere un **nome** per il nuovo profilo.
5. Immettere un **ID Bundle** seguendo Apple di denominazione del Consiglio.
6. Scorrere verso il basso il **servizi App** sezione, selezionare **SiriKit** e fare clic sui **continua** pulsante: 

    [![](implementing-sirikit-images/setup03.png "Selezionare SiriKit")](implementing-sirikit-images/setup03.png#lightbox)
7. Verificare tutte le impostazioni, quindi **Submit** ID. l'App
8. Selezionare **profili di Provisioning** > **sviluppo**, fare clic sui **+** pulsante, seleziona il **ID Apple**, quindi fare clic su **continuazione**.
9. Fare clic sul selettore **tutte**, quindi fare clic su **continua**.
10. Fare clic su **Seleziona tutto** nuovamente, quindi fare clic su **continua**.
11. Immettere un **nome profilo** usando Apple di suggerimenti sulla denominazione, quindi fare clic su **continua**.
12. Avviare Xcode.
13. Nel menu Xcode selezionare **preferenze...**
14. Selezionare **conti**, quindi fare clic su di **Visualizza i dettagli...** Pulsante: 

    [![](implementing-sirikit-images/setup04.png "Selezionare gli account")](implementing-sirikit-images/setup04.png#lightbox)
15. Scegliere il **Scarica tutti i profili** pulsante in basso a sinistra: 

    [![](implementing-sirikit-images/setup05.png "Scarica tutti i profili")](implementing-sirikit-images/setup05.png#lightbox)
16. Verificare che il **profilo di Provisioning** creato sopra è stato installato in Xcode.
17. Aprire il progetto per aggiungere il supporto di SiriKit per Visual Studio per Mac.
18. Fare doppio clic il `Info.plist` del file nei **Esplora soluzioni**.
18. Verificare che il **identificatore del Bundle** corrisponde a quello creato nel portale per sviluppatori Apple sopra: 

    [![](implementing-sirikit-images/setup06.png "L'identificatore del Bundle")](implementing-sirikit-images/setup06.png#lightbox)
18. Nel **Esplora soluzioni**, selezionare la **progetto**.
19. Fare clic sul progetto e selezionare **opzioni**.
21. Selezionare **firma del Bundle iOS**, selezionare la **identità di firma** e **profilo di Provisioning** creato in precedenza: 

    [![](implementing-sirikit-images/setup07.png "Selezionare l'identità di firma e profilo di Provisioning")](implementing-sirikit-images/setup07.png#lightbox)
22. Fare clic su **OK** per salvare le modifiche.

> [!IMPORTANT]
> Test SiriKit funziona solo su un reale iOS 10 dispositivi Hardware e non in iOS 10 simulatore. Se verificano problemi di installazione di un SiriKit abilitato app xamarin. IOS sulll'hardware effettivo, verificare che l'Entitlement necessari, l'ID App, identificatore firma e profilo di Provisioning siano state configurate correttamente in Apple Developer Portal e Visual Studio per Mac.

### <a name="requesting-siri-authorization"></a>Richiede l'autorizzazione di Siri

Prima che l'app aggiunge specifica terminologia qualsiasi utente o le estensioni di Intent si connette a Siri, è necessario richiede l'autorizzazione da parte dell'utente a Siri di accedere.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Modificare l'app `Info.plist` file, passare al **origine** consente di visualizzare e aggiungere il `NSSiriUsageDescription` chiave con un valore stringa che descrive come l'app userà Siri e quali tipi di dati verranno inviati. Ad esempio, è possibile che l'app MonkeyChat "contatti MonkeyChat verranno inviati a Siri":

[![](implementing-sirikit-images/request01.png "NSSiriUsageDescription nell'editor Info. plist")](implementing-sirikit-images/request01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Modificare l'app `Info.plist` file e aggiungere il `NSSiriUsageDescription` chiave con un valore stringa che descrive come l'app userà Siri e quali tipi di dati verranno inviati. Ad esempio, è possibile che l'app MonkeyChat "contatti MonkeyChat verranno inviati a Siri":

[![](implementing-sirikit-images/request01w.png "NSSiriUsageDescription nell'editor Info. plist")](implementing-sirikit-images/request01w.png#lightbox)

-----

Chiamare il `RequestSiriAuthorization` metodo del `INPreferences` classe al primo avvio dell'app. Modificare il `AppDelegate.cs` classi e rendere il `FinishedLaunching` metodo aspetto simile al seguente:


```csharp
using Intents;
...

public override bool FinishedLaunching (UIApplication application, NSDictionary launchOptions)
{

    // Request access to Siri
    INPreferences.RequestSiriAuthorization ((INSiriAuthorizationStatus status) => {
        // Respond to returned status
        switch (status) {
        case INSiriAuthorizationStatus.Authorized:
            break;
        case INSiriAuthorizationStatus.Denied:
            break;
        case INSiriAuthorizationStatus.NotDetermined:
            break;
        case INSiriAuthorizationStatus.Restricted:
            break;
        }
    });


    return true;
}
```

La prima volta, che questo metodo viene chiamato, viene visualizzato un avviso che richiede all'utente di consentire all'app di accedere a Siri. Il messaggio che lo sviluppatore di aggiunta il `NSSiriUsageDescription` sopra verrà visualizzato in questo avviso. Se l'utente inizialmente Nega l'accesso, possono usare la **impostazioni** app per concedere l'accesso all'app.

In qualsiasi momento, l'app può controllare possibilità dell'app di accedere a Siri chiamando il `SiriAuthorizationStatus` metodo di `INPreferences` classe.

### <a name="localization-and-siri"></a>Localizzazione e Siri

In un dispositivo iOS, l'utente è in grado di selezionare una lingua per Siri diverso, quindi l'impostazione predefinita. Quando si lavora con i dati localizzati, l'app dovrà usare il `SiriLanguageCode` metodo di `INPreferences` classe per ottenere il codice della lingua da Siri. Ad esempio:

```csharp
var language = INPreferences.SiriLanguageCode();

// Take action based on language
if (language == "en-US") {
    // Do something...
}
```

### <a name="adding-user-specific-vocabulary"></a>Aggiunta utente specifica terminologia

La specifica terminologia utente dovrà fornire parole o frasi specifiche dei singoli utenti dell'app. Questi vengono forniti in fase di esecuzione dell'App principale (non le estensioni App) come un set ordinato di termini, ordinati in una priorità di utilizzo più significativa per gli utenti, con i termini più importanti all'inizio dell'elenco.

Utente specifico vocabolario devono appartenere a una delle categorie seguenti:

- Nomi di contatti (che non sono gestiti dal framework di contatti).
- Tag della foto.
- Nomi degli Album di foto.
- Per gli allenamenti nomi.

Quando si seleziona la terminologia da registrare come vocabolario personalizzato, scegliere solo le condizioni che potrebbero essere frainteso da chiunque non abbia familiarità con l'app. Mai register termini comuni, ad esempio "My allenamento" o "My Album". Ad esempio, l'app MonkeyChat registrerà i nomi alternativi associati a ogni contatto nella rubrica dell'utente.

L'app fornisce la specifica terminologia utente chiamando il `SetVocabularyStrings` metodo per il `INVocabulary` classe e passando un `NSOrderedSet` raccolta dall'app principale. L'app deve chiamare sempre il `RemoveAllVocabularyStrings` metodo prima, per rimuovere tutte le condizioni esistenti prima di aggiungere quelli nuovi. Ad esempio:

```csharp
using System;
using System.Linq;
using System.Collections.Generic;
using Foundation;
using Intents;

namespace MonkeyChatCommon
{
    public class MonkeyAddressBook : NSObject
    {
        #region Computed Properties
        public List<MonkeyContact> Contacts { get; set; } = new List<MonkeyContact> ();
        #endregion

        #region Constructors
        public MonkeyAddressBook ()
        {
        }
        #endregion

        #region Public Methods
        public NSOrderedSet<NSString> ContactNicknames ()
        {
            var nicknames = new NSMutableOrderedSet<NSString> ();

            // Sort contacts by the last time used
            var query = Contacts.OrderBy (contact => contact.LastCalledOn);

            // Assemble ordered list of nicknames by most used to least
            foreach (MonkeyContact contact in query) {
                nicknames.Add (new NSString (contact.ScreenName));
            }

            // Return names
            return new NSOrderedSet<NSString> (nicknames.AsSet ());
        }

        // This method MUST only be called on a background thread!
        public void UpdateUserSpecificVocabulary ()
        {
            // Clear any existing vocabulary
            INVocabulary.SharedVocabulary.RemoveAllVocabularyStrings ();

            // Register new vocabulary
            INVocabulary.SharedVocabulary.SetVocabularyStrings (ContactNicknames (), INVocabularyStringType.ContactName);
        }
        #endregion
    }
}
```

Con questo codice, si potrebbe essere chiamato come segue:

```csharp
using System;
using System.Threading;
using UIKit;
using MonkeyChatCommon;
using Intents;

namespace MonkeyChat
{
    public partial class ViewController : UIViewController
    {
        #region AppDelegate Access
        public AppDelegate ThisApp {
            get { return (AppDelegate)UIApplication.SharedApplication.Delegate; }
        }
        #endregion

        #region Constructors
        protected ViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do we have access to Siri?
            if (INPreferences.SiriAuthorizationStatus == INSiriAuthorizationStatus.Authorized) {
                // Yes, update Siri's vocabulary
                new Thread (() => {
                    Thread.CurrentThread.IsBackground = true;
                    ThisApp.AddressBook.UpdateUserSpecificVocabulary ();
                }).Start ();
            }
        }

        public override void DidReceiveMemoryWarning ()
        {
            base.DidReceiveMemoryWarning ();
            // Release any cached data, images, etc that aren't in use.
        }
        #endregion
    }
}
```

> [!IMPORTANT]
> Siri vocabolario personalizzato vengono considerati gli hint e incorporerà come parte della terminologia come possibili. Tuttavia, lo spazio per il vocabolario personalizzato è limitata, pertanto è importante registrare _solo_ la terminologia che potrebbe generare confusione, pertanto ridurre il numero totale di termini registrati al minimo.

Per altre informazioni, vedere la [riferimento vocabolario specifici dell'utente](~/ios/platform/sirikit/understanding-sirikit.md) e di Apple [specifica personalizzata del vocabolario riferimento](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1).

### <a name="adding-app-specific-vocabulary"></a>Aggiunta di vocabolario specifico dell'App

La specifica terminologia App definisce le parole o frasi specifiche che saranno noto a tutti gli utenti dell'app, ad esempio per gli allenamenti nomi o tipi di veicoli. Poiché questi fanno parte dell'applicazione, vengono definiti un `AppIntentVocabulary.plist` file come parte del bundle dell'app principale. Inoltre, queste parole e frasi devono essere localizzate.

I termini del vocabolario specifico dell'App devono appartenere a una delle categorie seguenti:

- Recarsi opzioni.
- Per gli allenamenti nomi.

Il file di vocabolario specifiche App contiene due chiavi a livello di radice:

- `ParameterVocabularies` **Obbligatorio** -definisce condizioni personalizzate e parametri con finalità di si applicano a dell'app.
- `IntentPhrases` **Facoltativo** -contiene le frasi di esempio usando i termini personalizzati definiti nel `ParameterVocabularies`.

Ogni voce di `ParameterVocabularies` deve specificare una stringa di ID, termine e l'intento di cui si applica il termine. Inoltre, un singolo termine potrebbe riguardare più finalità.

Per un elenco completo dei valori accettabili e struttura dei file necessari, vedere di Apple [riferimento sul formato di File vocabolario App](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CustomVocabularyKeys.html#//apple_ref/doc/uid/TP40016875-CH10-SW1).

Per aggiungere un `AppIntentVocabulary.plist` file al progetto di app, eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Fare clic sul nome del progetto nel **Esplora soluzioni** e selezionare **Add** > **nuovo File...**   >  **iOS**:

    [![](implementing-sirikit-images/plist01.png "Aggiungere un elenco di proprietà")](implementing-sirikit-images/plist01.png#lightbox)
2. Fare doppio clic il `AppIntentVocabulary.plist` del file nei **Esplora soluzioni** per aprirlo e modificarlo.
3. Fare clic sui **+** per aggiungere una chiave, impostare il **nome** a `ParameterVocabularies` e il **tipo** a `Array`:

    [![](implementing-sirikit-images/plist02.png "Impostare il nome su ParameterVocabularies e il tipo di matrice")](implementing-sirikit-images/plist02.png#lightbox)
4. Espandere `ParameterVocabularies` e fare clic sui **+** pulsante e impostarne il **tipo** a `Dictionary`:

    [![](implementing-sirikit-images/plist03.png "Impostare il tipo di dizionario")](implementing-sirikit-images/plist03.png#lightbox)
5. Fare clic sui **+** per aggiungere una nuova chiave, impostare il **nome** a `ParameterNames` e il **tipo** a `Array`:

    [![](implementing-sirikit-images/plist04.png "Impostare il nome su ParameterNames e il tipo di matrice")](implementing-sirikit-images/plist04.png#lightbox)
6. Fare clic sui **+** per aggiungere una nuova chiave con il **tipo** di `String` e il valore come uno dei nomi dei parametri disponibili. Ad esempio, `INStartWorkoutIntent.workoutName`:

    [![](implementing-sirikit-images/plist05.png "La chiave INStartWorkoutIntent.workoutName")](implementing-sirikit-images/plist05.png#lightbox)
7. Aggiungere il `ParameterVocabulary` chiave per il `ParameterVocabularies` chiave con il **tipo** di `Array`:

    [![](implementing-sirikit-images/plist06.png "Aggiungere la chiave ParameterVocabulary alla chiave ParameterVocabularies con il tipo di matrice")](implementing-sirikit-images/plist06.png#lightbox)
8. Aggiungere una nuova chiave con il **tipo** di `Dictionary`:

    [![](implementing-sirikit-images/plist07.png "Aggiungere una nuova chiave con il tipo di dizionario")](implementing-sirikit-images/plist07.png#lightbox)
9. Aggiungere il `VocabularyItemIdentifier` chiave con il **tipo** di `String` e specificare un ID univoco per il termine:

    [![](implementing-sirikit-images/plist08.png "Aggiungere la chiave VocabularyItemIdentifier con il tipo di stringa e specificare un ID univoco")](implementing-sirikit-images/plist08.png#lightbox)
10. Aggiungere il `VocabularyItemSynonyms` chiave con il **tipo** di `Array`:

    [![](implementing-sirikit-images/plist09.png "Aggiungere la chiave VocabularyItemSynonyms con il tipo di matrice")](implementing-sirikit-images/plist09.png#lightbox)
11. Aggiungere una nuova chiave con il **tipo** di `Dictionary`:

    [![](implementing-sirikit-images/plist10.png "Aggiungere una nuova chiave con il tipo di dizionario")](implementing-sirikit-images/plist10.png#lightbox)
12. Aggiungere il `VocabularyItemPhrase` chiave con il **tipo** di `String` e il termine app definisce:

    [![](implementing-sirikit-images/plist11.png "Aggiungere la chiave VocabularyItemPhrase con il tipo di stringa e il termine sta definendo l'app")](implementing-sirikit-images/plist11.png#lightbox)
13. Aggiungere il `VocabularyItemPronunciation` chiave con il **tipo** di `String` e Pronuncia fonetica del periodo di validità:

    [![](implementing-sirikit-images/plist12.png "Aggiungere la chiave VocabularyItemPronunciation con il tipo di stringa e la pronuncia fonetica del periodo di validità")](implementing-sirikit-images/plist12.png#lightbox)
14. Aggiungere il `VocabularyItemExamples` chiave con il **tipo** di `Array`:

    [![](implementing-sirikit-images/plist13.png "Aggiungere la chiave VocabularyItemExamples con il tipo di matrice")](implementing-sirikit-images/plist13.png#lightbox)
15. Aggiungere alcuni `String` chiavi con esempi di utilizzo del termine:

    [![](implementing-sirikit-images/plist14.png "Aggiungere alcune chiavi di stringa con esempi di utilizzo del termine")](implementing-sirikit-images/plist14.png#lightbox)
16. Ripetere i passaggi precedenti per l'app è necessario definire altre condizioni personalizzate.
17. Comprimi il `ParameterVocabularies` chiave.
18. Aggiungere il `IntentPhrases` chiave con il **tipo** di `Array`:

    [![](implementing-sirikit-images/plist15.png "Aggiungere la chiave IntentPhrases con il tipo di matrice")](implementing-sirikit-images/plist15.png#lightbox)
19. Aggiungere una nuova chiave con il **tipo** di `Dictionary`:

    [![](implementing-sirikit-images/plist16.png "Aggiungere una nuova chiave con il tipo di dizionario")](implementing-sirikit-images/plist16.png#lightbox)
20. Aggiungere il `IntentName` chiave con il **tipo** di `String` e finalità dell'esempio:

    [![](implementing-sirikit-images/plist17.png "Aggiungere la chiave IntentName con il tipo di stringa e lo scopo dell'esempio")](implementing-sirikit-images/plist17.png#lightbox)
21. Aggiungere il `IntentExamples` chiave con il **tipo** di `Array`:

    [![](implementing-sirikit-images/plist18.png "Aggiungere la chiave IntentExamples con il tipo di matrice")](implementing-sirikit-images/plist18.png#lightbox)
22. Aggiungere alcuni `String` chiavi con esempi di utilizzo del termine:

    [![](implementing-sirikit-images/plist19.png "Aggiungere alcune chiavi di stringa con esempi di utilizzo del termine")](implementing-sirikit-images/plist19.png#lightbox)
23. Ripetere i passaggi precedenti per qualsiasi finalità l'app è necessario fornire esempi di utilizzo di.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Fare clic sul nome del progetto nel **Esplora soluzioni** e selezionare **Aggiungi > Nuovo elemento... > Apple > elenco proprietà > Info. plist**:

    [![](implementing-sirikit-images/plist01.w157-sml.png "Aggiungere un nuovo file Info. plist")](implementing-sirikit-images/plist01.w157.png#lightbox)

2. Fare doppio clic il `AppIntentVocabulary.plist` del file nei **Esplora soluzioni** per aprirlo e modificarlo.
3. Fare clic sui **+** per aggiungere una chiave, impostare il **nome** a `ParameterVocabularies` e il **tipo** a `Array`:

    [![](implementing-sirikit-images/plist02w.png "Impostare il nome su ParameterVocabularies e il tipo di matrice")](implementing-sirikit-images/plist02w.png#lightbox)
4. Espandere `ParameterVocabularies` e fare clic sui **+** pulsante e impostarne il **tipo** a `Dictionary`:

    [![](implementing-sirikit-images/plist03w.png "Impostare il tipo di dizionario")](implementing-sirikit-images/plist03w.png#lightbox)
5. Fare clic sui **+** per aggiungere una nuova chiave, impostare il **nome** a `ParameterNames` e il **tipo** a `Array`:

    [![](implementing-sirikit-images/plist04w.png "Impostare il nome su ParameterNames e il tipo di matrice")](implementing-sirikit-images/plist04w.png#lightbox)
6. Fare clic sui **+** per aggiungere una nuova chiave con il **tipo** di `String` e il valore come uno dei nomi dei parametri disponibili. Ad esempio, `INStartWorkoutIntent.workoutName`:

    [![](implementing-sirikit-images/plist05w.png "La chiave INStartWorkoutIntent.workoutName")](implementing-sirikit-images/plist05w.png#lightbox)
7. Aggiungere il `ParameterVocabulary` chiave per il `ParameterVocabularies` chiave con il **tipo** di `Array`:

    [![](implementing-sirikit-images/plist06w.png "Aggiungere la chiave ParameterVocabulary alla chiave ParameterVocabularies con il tipo di matrice")](implementing-sirikit-images/plist06w.png#lightbox)
8. Aggiungere una nuova chiave con il **tipo** di `Dictionary`:

    [![](implementing-sirikit-images/plist07w.png "Aggiungere una nuova chiave con il tipo di dizionario")](implementing-sirikit-images/plist07w.png#lightbox)
9. Aggiungere il `VocabularyItemIdentifier` chiave con il **tipo** di `String` e specificare un ID univoco per il termine:

    [![](implementing-sirikit-images/plist08w.png "Aggiungere la chiave VocabularyItemIdentifier con il tipo di stringa e specificare un ID univoco per il termine")](implementing-sirikit-images/plist08w.png#lightbox)
10. Aggiungere il `VocabularyItemSynonyms` chiave con il **tipo** di `Array`:

    [![](implementing-sirikit-images/plist09w.png "Aggiungere la chiave VocabularyItemSynonyms con il tipo di matrice")](implementing-sirikit-images/plist09w.png#lightbox)
11. Aggiungere una nuova chiave con il **tipo** di `Dictionary`:

    [![](implementing-sirikit-images/plist10w.png "Aggiungere una nuova chiave con il tipo di dizionario")](implementing-sirikit-images/plist10w.png#lightbox)
12. Aggiungere il `VocabularyItemPhrase` chiave con il **tipo** di `String` e il termine app definisce:

    [![](implementing-sirikit-images/plist11w.png "Aggiungere la chiave VocabularyItemPhrase con il tipo di stringa e il termine sta definendo l'app")](implementing-sirikit-images/plist11w.png#lightbox)
13. Aggiungere il `VocabularyItemPronunciation` chiave con il **tipo** di `String` e Pronuncia fonetica del periodo di validità:

    [![](implementing-sirikit-images/plist12w.png "Aggiungere la chiave VocabularyItemPronunciation con il tipo di stringa e la pronuncia fonetica del periodo di validità")](implementing-sirikit-images/plist12w.png#lightbox)
14. Aggiungere il `VocabularyItemExamples` chiave con il **tipo** di `Array`:

    [![](implementing-sirikit-images/plist13w.png "Aggiungere la chiave VocabularyItemExamples con il tipo di matrice")](implementing-sirikit-images/plist13w.png#lightbox)
15. Aggiungere alcuni `String` chiavi con esempi di utilizzo del termine:

    [![](implementing-sirikit-images/plist14w.png "Aggiungere alcune chiavi di stringa con esempi di utilizzo del termine")](implementing-sirikit-images/plist14w.png#lightbox)
16. Ripetere i passaggi precedenti per l'app è necessario definire altre condizioni personalizzate.
17. Comprimi il `ParameterVocabularies` chiave.
18. Aggiungere il `IntentPhrases` chiave con il **tipo** di `Array`:

    [![](implementing-sirikit-images/plist15w.png "Aggiungere la chiave IntentPhrases con il tipo di matrice")](implementing-sirikit-images/plist15w.png#lightbox)
19. Aggiungere una nuova chiave con il **tipo** di `Dictionary`:

    [![](implementing-sirikit-images/plist16w.png "Aggiungere una nuova chiave con il tipo di dizionario")](implementing-sirikit-images/plist16w.png#lightbox)
20. Aggiungere il `IntentName` chiave con il **tipo** di `String` e finalità dell'esempio:

    [![](implementing-sirikit-images/plist17w.png "Aggiungere la chiave IntentName con il tipo di stringa e lo scopo dell'esempio")](implementing-sirikit-images/plist17w.png#lightbox)
21. Aggiungere il `IntentExamples` chiave con il **tipo** di `Array`:

    [![](implementing-sirikit-images/plist18w.png "Aggiungere la chiave IntentExamples con il tipo di matrice")](implementing-sirikit-images/plist18w.png#lightbox)
22. Aggiungere alcuni `String` chiavi con esempi di utilizzo del termine:

    [![](implementing-sirikit-images/plist19w.png "Aggiungere alcune chiavi di stringa con esempi di utilizzo del termine")](implementing-sirikit-images/plist19w.png#lightbox)
23. Ripetere i passaggi precedenti per qualsiasi finalità l'app è necessario fornire esempi di utilizzo di.

-----

> [!IMPORTANT]
> Il `AppIntentVocabulary.plist` verrà registrato con Siri sul test dispositivi durante lo sviluppo e potrebbero richiedere tempo per Siri incorporare il vocabolario personalizzato. Di conseguenza, il tester dovrà attendere qualche minuto prima di provare a testare l'App specifica terminologia quando è stato aggiornato.

Per altre informazioni, vedere la [riferimento vocabolario specifico dell'App](~/ios/platform/sirikit/understanding-sirikit.md) e di Apple [specifica personalizzata del vocabolario riferimento](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1).

## <a name="adding-an-intents-extension"></a>Aggiunta di un'estensione per Intent

Ora che l'app è stata preparata ad adottare SiriKit, lo sviluppatore dovrà aggiungere estensioni Intent uno (o più) alla soluzione per gestire i tipi necessari per l'integrazione di Siri.

Per ogni estensione per Intent necessari, procedere come segue:

- Aggiungere un progetto di estensione per Intent per la soluzione per app xamarin. IOS.
- Configurare l'estensione per Intent `Info.plist` file.
- Modificare la classe principale di estensione per Intent.

Per altre informazioni, vedere la [il riferimento all'estensione per Intent](~/ios/platform/sirikit/understanding-sirikit.md) e di Apple [la creazione del riferimento di estensione per Intent](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CreatingtheIntentsExtension.html#//apple_ref/doc/uid/TP40016875-CH4-SW1).

### <a name="creating-the-extension"></a>Creazione dell'estensione

Per aggiungere un'estensione per Intent alla soluzione, eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Fare clic sui **Nome soluzione** nel **riquadro della soluzione** e selezionare **Add** > **Aggiungi nuovo progetto...** .
2. Nella finestra di dialogo selezionare **iOS** > **estensioni** > **estensione per Intent** e fare clic su di **successivo** pulsante: 

    [![](implementing-sirikit-images/intents05.png "Selezionare l'estensione per Intent")](implementing-sirikit-images/intents05.png#lightbox)
3. Immettere quindi una **Name** per l'estensione per Intent e fare clic sul **successivo** pulsante: 

    [![](implementing-sirikit-images/intents06.png "Immettere un nome per l'estensione per Intent")](implementing-sirikit-images/intents06.png#lightbox)
4. Infine, fare clic sui **Create** pulsante per aggiungere l'estensione per Intent per la soluzione App: 

    [![](implementing-sirikit-images/intents07.png "Aggiungere l'estensione per Intent alla soluzione di App")](implementing-sirikit-images/intents07.png#lightbox)
5. Nel **Esplora soluzioni**, fare clic sui **riferimenti** cartella dell'estensione per Intent appena creato. Controllare il nome del progetto di libreria il codice condiviso comune (che l'app creata in precedenza) e scegliere il **OK** pulsante: 

    [![](implementing-sirikit-images/intents08.png "Selezionare il nome del progetto di libreria di codice condiviso comune")](implementing-sirikit-images/intents08.png#lightbox)
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Fare clic sui **Nome soluzione** nel **Esplora soluzioni** e selezionare **Add** > **Aggiungi nuovo progetto...** .
2. Nella finestra di dialogo selezionare **Visual C# > estensioni iOS > estensione per Intent** e fare clic sui **successiva** pulsante:

    [![](implementing-sirikit-images/intents05.w157-sml.png "Selezionare l'estensione per Intent")](implementing-sirikit-images/intents05.w157.png#lightbox)
3. Immettere quindi un' **Name** per l'estensione per Intent e fare clic sulla **OK** pulsante.
1. Nel **Esplora soluzioni**, fare clic sui **riferimenti** cartella dell'estensione per Intent appena creato e scegliere **Aggiungi > riferimento**. Controllare il nome del progetto di libreria il codice condiviso comune (che l'app creata in precedenza) e scegliere il **OK** pulsante:

    [![](implementing-sirikit-images/intents08w.png "Selezionare il nome del progetto di libreria di codice condiviso comune")](implementing-sirikit-images/intents08w.png#lightbox)
    
-----

Ripetere questi passaggi per il numero di estensioni di Intent (basato su [architettura dell'App per le estensioni](#Architecting-the-App-for-Extensions) sezione precedente) che l'app richiederà.

### <a name="configuring-the-infoplist"></a>Configurazione di Info. plist

Per ognuna delle estensioni di Intent che hanno aggiunto alla soluzione dell'app, devono essere configurate nel `Info.plist` file per utilizzarli con l'app.

Proprio come qualsiasi tipica estensione dell'App, l'app avrà le chiavi esistenti di `NSExtension` e `NSExtensionAttributes`. Per un'estensione per Intent sono disponibili due nuovi attributi che devono essere configurati:

[![](implementing-sirikit-images/intents01.png "I due nuovi attributi che devono essere configurati")](implementing-sirikit-images/intents01.png#lightbox)

- **IntentsSupported** : È obbligatorio ed è costituito da una matrice di nomi di classe Intent che l'app deve supportare dall'estensione per Intent.
- **IntentsRestrictedWhileLocked** -è una chiave facoltativa per l'app specificare il comportamento dello schermo di blocco dell'estensione. È costituito da una matrice di nomi di classe Intent che l'app deve richiedere all'utente di avere effettuato l'accesso da utilizzare dall'estensione per l'intento.

Per configurare l'estensione di finalità `Info.plist` file, fare doppio clic su esso nel **Esplora soluzioni** per aprirlo e modificarlo. Successivamente, passare al **origine** consente di visualizzare, quindi espandere il `NSExtension` e `NSExtensionAttributes` chiavi nell'editor:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](implementing-sirikit-images/intents02.png "Le chiavi NSExtension e NSExtensionAttributes nell'editor")](implementing-sirikit-images/intents02.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents02w.png "Le chiavi NSExtension e NSExtensionAttributes nell'editor")](implementing-sirikit-images/intents02w.png#lightbox)

-----

Espandere il `IntentsSupported` della chiave e aggiungere il nome di una classe con finalità di questa estensione supporta. Per l'app MonkeyChat di esempio, supporta il `INSendMessageIntent`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](implementing-sirikit-images/intents09.png "La chiave INSendMessageIntent")](implementing-sirikit-images/intents09.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents09w.png "La chiave INSendMessageIntent")](implementing-sirikit-images/intents09w.png#lightbox)

-----

Se l'app, facoltativamente, richiede che l'utente essere connesso al dispositivo per usare uno scopo specifico, espandere il `IntentRestrictedWhileLocked` della chiave e aggiungere i nomi delle classi di Intent che hanno accesso limitato. Per l'app MonkeyChat di esempio, l'utente deve essere connesso per inviare un messaggio di chat, in modo che sono state aggiunte `INSendMessageIntent`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](implementing-sirikit-images/intents10.png "La chiave INSendMessageIntent aggiunta")](implementing-sirikit-images/intents10.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents10w.png "La chiave INSendMessageIntent aggiunta")](implementing-sirikit-images/intents10w.png#lightbox)

-----


Per un elenco completo di domini con finalità di disponibili, vedere di Apple [finalità domini riferimento](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2).

### <a name="configuring-the-main-class"></a>Configurare la classe principale

Successivamente, lo sviluppatore sarà necessario configurare la classe principale che funge da punto di ingresso principale per l'estensione per Intent in Siri. Deve essere una sottoclasse di `INExtension` cui è conforme al `IINIntentHandler` delegare. Ad esempio:

```csharp
using System;
using System.Collections.Generic;

using Foundation;
using Intents;

namespace MonkeyChatIntents
{
    [Register ("IntentHandler")]
    public class IntentHandler : INExtension, IINSendMessageIntentHandling, IINSearchForMessagesIntentHandling, IINSetMessageAttributeIntentHandling
    {
        #region Constructors
        protected IntentHandler (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override NSObject GetHandler (INIntent intent)
        {
            // This is the default implementation.  If you want different objects to handle different intents,
            // you can override this and return the handler you want for that particular intent.

            return this;
        }
        #endregion
        ...
    }
}
```

È presente un metodo solitari che l'app deve implementare sulla classe principale di estensione per Intent, il `GetHandler` (metodo). Questo metodo viene passato un Intent per SiriKit e l'app deve restituire un **finalità gestore** che corrisponde al tipo dello scopo specifico.

Poiché l'app di esempio MonkeyChat gestisce solo un solo intent, restituisce se stesso nel `GetHandler` (metodo). Se l'estensione gestita più di un intent, lo sviluppatore potrebbe aggiungere una classe per ogni tipo intent e restituire un'istanza qui in base il `Intent` passato al metodo.

### <a name="handling-the-resolve-stage"></a>Gestisce la fase di risoluzione

La fase di risoluzione è in cui l'estensione per Intent verranno chiarire e convalidare i parametri passati da Siri e sono state impostate tramite conversazioni dell'utente.

Per ogni parametro che vengono inviato da Siri, sussiste un `Resolve` (metodo). L'app sarà necessario implementare questo metodo per ogni parametro che l'app potrebbe essere necessario della Guida di Siri per ottenere la risposta corretta da parte dell'utente.

Nel caso dell'app MonkeyChat di esempio, l'estensione per Intent richiederanno un uno o più destinatari a cui inviare il messaggio. Per ogni destinatario nell'elenco, l'estensione dovrà eseguire una ricerca di contatto che può avere il risultato seguente:

- Esattamente un solo contatto corrispondente è stato trovato.
- Sono disponibili due o più contatti corrispondenti.
- Viene trovato alcun contatto corrisponda.

Inoltre, MonkeyChat richiede il contenuto per il corpo del messaggio. Se l'utente non ha fornito questo, Siri deve richiedere all'utente per il contenuto.

L'estensione per Intent dovrà gestire correttamente a ognuno di questi casi.


```csharp
[Export ("resolveRecipientsForSearchForMessages:withCompletion:")]
public void ResolveRecipients (INSendMessageIntent intent, Action<INPersonResolutionResult []> completion)
{
    var recipients = intent.Recipients;
    // If no recipients were provided we'll need to prompt for a value.
    if (recipients.Length == 0) {
        completion (new INPersonResolutionResult [] { (INPersonResolutionResult)INPersonResolutionResult.NeedsValue });
        return;
    }

    var resolutionResults = new List<INPersonResolutionResult> ();

    foreach (var recipient in recipients) {
        var matchingContacts = new INPerson [] { recipient }; // Implement your contact matching logic here to create an array of matching contacts
        if (matchingContacts.Length > 1) {
            // We need Siri's help to ask user to pick one from the matches.
            resolutionResults.Add (INPersonResolutionResult.GetDisambiguation (matchingContacts));
        } else if (matchingContacts.Length == 1) {
            // We have exactly one matching contact
            resolutionResults.Add (INPersonResolutionResult.GetSuccess (recipient));
        } else {
            // We have no contacts matching the description provided
            resolutionResults.Add ((INPersonResolutionResult)INPersonResolutionResult.Unsupported);
        }
    }

    completion (resolutionResults.ToArray ());
}

[Export ("resolveContentForSendMessage:withCompletion:")]
public void ResolveContent (INSendMessageIntent intent, Action<INStringResolutionResult> completion)
{
    var text = intent.Content;
    if (!string.IsNullOrEmpty (text))
        completion (INStringResolutionResult.GetSuccess (text));
    else
        completion ((INStringResolutionResult)INStringResolutionResult.NeedsValue);
}
```

Per altre informazioni, vedere la [il riferimento fase risolvere](~/ios/platform/sirikit/understanding-sirikit.md) e di Apple [Resolving e gestisce gli Intent riferimento](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ResolvingandHandlingIntents.html#//apple_ref/doc/uid/TP40016875-CH5-SW1).

### <a name="handling-the-confirm-stage"></a>Gestisce la fase di conferma

La fase di conferma è dove l'estensione per Intent controlla per verificare che dispone di tutte le informazioni per soddisfare la richiesta dell'utente. L'app desidera inviare conferma lungo verrà tutti i dettagli di supporto di ciò che sta per verificarsi a Siri in modo che può essere confermata con l'utente che si tratta dell'azione desiderata.

```csharp
[Export ("confirmSendMessage:completion:")]
public void ConfirmSendMessage (INSendMessageIntent intent, Action<INSendMessageIntentResponse> completion)
{
    // Verify user is authenticated and the app is ready to send a message.
    ...

    var userActivity = new NSUserActivity (nameof (INSendMessageIntent));
    var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Ready, userActivity);
    completion (response);
}
```

Per altre informazioni, vedere la [The confermare fase riferimento](~/ios/platform/sirikit/understanding-sirikit.md).

### <a name="processing-the-intent"></a>La finalità di elaborazione

Questo è il punto in cui l'estensione per Intent esegue effettivamente l'attività per soddisfare la richiesta dell'utente e passare i risultati a Siri in modo che l'utente può essere informato.


```csharp
public void HandleSendMessage (INSendMessageIntent intent, Action<INSendMessageIntentResponse> completion)
{
    // Implement the application logic to send a message here.
    ...

    var userActivity = new NSUserActivity (nameof (INSendMessageIntent));
    var response = new INSendMessageIntentResponse (INSendMessageIntentResponseCode.Success, userActivity);
    completion (response);
}

public void HandleSearchForMessages (INSearchForMessagesIntent intent, Action<INSearchForMessagesIntentResponse> completion)
{
    // Implement the application logic to find a message that matches the information in the intent.
    ...

    var userActivity = new NSUserActivity (nameof (INSearchForMessagesIntent));
    var response = new INSearchForMessagesIntentResponse (INSearchForMessagesIntentResponseCode.Success, userActivity);

    // Initialize with found message's attributes
    var sender = new INPerson (new INPersonHandle ("sarah@example.com", INPersonHandleType.EmailAddress), null, "Sarah", null, null, null);
    var recipient = new INPerson (new INPersonHandle ("+1-415-555-5555", INPersonHandleType.PhoneNumber), null, "John", null, null, null);
    var message = new INMessage ("identifier", "I am so excited about SiriKit!", NSDate.Now, sender, new INPerson [] { recipient });
    response.Messages = new INMessage [] { message };
    completion (response);
}

public void HandleSetMessageAttribute (INSetMessageAttributeIntent intent, Action<INSetMessageAttributeIntentResponse> completion)
{
    // Implement the application logic to set the message attribute here.
    ...

    var userActivity = new NSUserActivity (nameof (INSetMessageAttributeIntent));
    var response = new INSetMessageAttributeIntentResponse (INSetMessageAttributeIntentResponseCode.Success, userActivity);
    completion (response);
}
```

Per altre informazioni, vedere la [The gestire fase riferimento](~/ios/platform/sirikit/understanding-sirikit.md).

## <a name="adding-an-intents-ui-extension"></a>Aggiunta di un'estensione per interfaccia utente Intent

L'estensione dell'interfaccia utente Intent facoltativa presenta la possibilità di portare l'interfaccia utente dell'app e informazioni personalizzate distintive nell'esperienza Siri e rendere gli utenti è connessa all'app. Con questa estensione per l'app può portare il marchio, nonché informazioni visual e altri nella trascrizione.

[![](implementing-sirikit-images/intentsui01.png "Un esempio di output di estensione per interfaccia utente Intent")](implementing-sirikit-images/intentsui01.png#lightbox)

Proprio come l'estensione per Intent, lo sviluppatore eseguirà il passaggio seguente per l'estensione dell'interfaccia utente Intent:

- Aggiungere un progetto di estensione per interfaccia utente Intent per la soluzione per app xamarin. IOS.
- Configurare l'estensione dell'interfaccia utente Intent `Info.plist` file.
- Modificare la classe principale di estensione per interfaccia utente Intent.

Per altre informazioni, vedere la [il riferimento dell'estensione dell'interfaccia utente Intent](~/ios/platform/sirikit/understanding-sirikit.md) e di Apple [fornendo un riferimento all'interfaccia personalizzata](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ProvidingaCustomInterface.html#//apple_ref/doc/uid/TP40016875-CH7-SW1).

### <a name="creating-the-extension"></a>Creazione dell'estensione

Per aggiungere un'estensione per interfaccia utente Intent per la soluzione, eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Fare clic sui **Nome soluzione** nel **riquadro della soluzione** e selezionare **Add** > **Aggiungi nuovo progetto...** .
2. Nella finestra di dialogo selezionare **iOS** > **estensioni** > **estensione dell'interfaccia utente Intent** e fare clic su di **successivo** pulsante: 

    [![](implementing-sirikit-images/intents11.png "Selezionare l'estensione dell'interfaccia utente Intent")](implementing-sirikit-images/intents11.png#lightbox)
3. Immettere quindi una **Name** per l'estensione per Intent e fare clic sul **successivo** pulsante: 

    [![](implementing-sirikit-images/intents12.png "Immettere un nome per l'estensione per Intent")](implementing-sirikit-images/intents12.png#lightbox)
4. Infine, fare clic sui **Create** pulsante per aggiungere l'estensione per Intent per la soluzione App: 

    [![](implementing-sirikit-images/intents13.png "Aggiungere l'estensione per Intent alla soluzione di App")](implementing-sirikit-images/intents13.png#lightbox)
5. Nel **Esplora soluzioni**, fare clic sui **riferimenti** cartella dell'estensione per Intent appena creato. Controllare il nome del progetto di libreria il codice condiviso comune (che l'app creata in precedenza) e scegliere il **OK** pulsante: 

    [![](implementing-sirikit-images/intents14.png "Selezionare il nome del progetto di libreria di codice condiviso comune")](implementing-sirikit-images/intents14.png#lightbox)
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Fare clic sui **Nome soluzione** nel **Esplora soluzioni** e selezionare **Add** > **Aggiungi nuovo progetto...**
2. Nella finestra di dialogo selezionare **iOS** > **estensioni** > **estensione dell'interfaccia utente Intent** e fare clic su di **successivo** pulsante.
3. Immettere quindi un' **Name** per l'estensione per Intent e fare clic sulla **OK** pulsante.
5. Nel **Esplora soluzioni**, fare clic sui **riferimenti** cartella dell'estensione per Intent appena creato. Controllare il nome del progetto di libreria il codice condiviso comune (che l'app creata in precedenza) e scegliere il **OK** pulsante.
    
-----

### <a name="configuring-the-infoplist"></a>Configurazione di Info. plist

Configurare l'estensione dell'interfaccia utente Intent `Info.plist` possa funzionare con l'app.

Proprio come qualsiasi tipica estensione dell'App, l'app avrà le chiavi esistenti di `NSExtension` e `NSExtensionAttributes`. Per un'estensione per Intent sussiste un nuovo attributo che deve essere configurato:

[![](implementing-sirikit-images/intents03.png "Un nuovo attributo che deve essere configurato")](implementing-sirikit-images/intents03.png#lightbox)

**IntentsSupported** è obbligatorio ed è costituito da una matrice di nomi di classe Intent che l'app desidera supportare dall'estensione per l'intento.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Per configurare l'estensione dell'interfaccia utente Intent `Info.plist` file, fare doppio clic su esso nel **Esplora soluzioni** per aprirlo e modificarlo. Successivamente, passare al **origine** consente di visualizzare, quindi espandere il `NSExtension` e `NSExtensionAttributes` chiavi nell'editor:

[![](implementing-sirikit-images/intents04.png "Le chiavi NSExtension e NSExtensionAttributes nell'editor")](implementing-sirikit-images/intents04.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Per configurare l'estensione dell'interfaccia utente Intent `Info.plist` file, fare doppio clic su esso nel **Esplora soluzioni** per aprirlo e modificarlo. Espandere la `NSExtension` e `NSExtensionAttributes` chiavi nell'editor:

[![](implementing-sirikit-images/intents04w.png "Chiavi Tthe NSExtension e NSExtensionAttributes nell'editor")](implementing-sirikit-images/intents04w.png#lightbox)

-----

Espandere il `IntentsSupported` della chiave e aggiungere il nome di una classe con finalità di questa estensione supporta. Per l'app MonkeyChat di esempio, supporta il `INSendMessageIntent`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](implementing-sirikit-images/intents15.png "La chiave INSendMessageIntent")](implementing-sirikit-images/intents15.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents15w.png "La chiave INSendMessageIntent")](implementing-sirikit-images/intents15w.png#lightbox)

-----

Per un elenco completo di domini con finalità di disponibili, vedere di Apple [finalità domini riferimento](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2).

### <a name="configuring-the-main-class"></a>Configurare la classe principale

Configurare la classe principale che funge da punto di ingresso principale per l'estensione dell'interfaccia utente Intent in Siri. Deve essere una sottoclasse `UIViewController` che è conforme al `IINUIHostedViewController` interfaccia. Ad esempio:

```csharp
using System;
using Foundation;
using CoreGraphics;
using Intents;
using IntentsUI;
using UIKit;

namespace MonkeyChatIntentsUI
{
    public partial class IntentViewController : UIViewController, IINUIHostedViewControlling
    {
        #region Constructors
        protected IntentViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }
        #endregion

        #region Override Methods
        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Do any required interface initialization here.
        }

        public override void DidReceiveMemoryWarning ()
        {
            // Releases the view if it doesn't have a superview.
            base.DidReceiveMemoryWarning ();

            // Release any cached data, images, etc that aren't in use.
        }
        #endregion

        #region Public Methods
        [Export ("configureWithInteraction:context:completion:")]
        public void Configure (INInteraction interaction, INUIHostedViewContext context, Action<CGSize> completion)
        {
            // Do configuration here, including preparing views and calculating a desired size for presentation.

            if (completion != null)
                completion (DesiredSize ());
        }

        [Export ("desiredSize:")]
        public CGSize DesiredSize ()
        {
            return ExtensionContext.GetHostedViewMaximumAllowedSize ();
        }
        #endregion
    }
}
```

Siri passerà una `INInteraction` istanza della classe per il `Configure` metodo del `UIViewController` istanza all'interno di estensione per interfaccia utente Intent.

Il `INInteraction` oggetto fornisce tre tipi principali di informazioni per l'estensione:

1. L'oggetto preventivo in fase di elaborazione.
2. L'oggetto risposta finalità dal `Confirm` e `Handle` metodi di estensione con finalità.
3. Lo stato di interazione che definisce lo stato dell'interazione tra l'app e Siri.

Il `UIViewController` istanza è la classe di entità per l'interazione con Siri e perché eredita da `UIViewController`, dispone dell'accesso a tutte le funzionalità di UIKit.

Quando Siri chiama il `Configure` metodo del `UIViewController` passa in un contesto di visualizzazione che informa che il Controller di visualizzazione verrà sia ospitato in un Siri Snippit o mappe carta.

Siri passerà anche in un gestore di completamento che l'app necessario per restituire le dimensioni desiderate della vista, una volta terminata la configurazione dell'app.

### <a name="design-the-ui-in-ios-designer"></a>Progettare l'interfaccia utente in iOS Designer

Layout dell'interfaccia utente dell'estensione dell'interfaccia utente Intent in iOS Designer. Fare doppio clic dell'estensione `MainInterface.storyboard` del file nei **Esplora soluzioni** per aprirlo e modificarlo. Trascinare in tutti gli elementi dell'interfaccia utente necessari per compilare l'interfaccia utente e salvare le modifiche.

> [!IMPORTANT]
> Sebbene sia possibile aggiungere elementi interattivi, ad esempio `UIButtons` oppure `UITextFields` per l'estensione dell'interfaccia utente Intent `UIViewController`, questi vengono rigorosamente non consentiti come l'interfaccia utente Intent in modalità interattiva e l'utente non sarà in grado di interagire con essi.

### <a name="wire-up-the-user-interface"></a>L'interfaccia utente trasmissione-Up

Interfaccia utente dell'estensione dell'interfaccia utente Intent creato in iOS Designer, modificare il `UIViewController` sottoclasse ed eseguire l'override di `Configure` metodo come indicato di seguito:

```csharp
[Export ("configureWithInteraction:context:completion:")]
public void Configure (INInteraction interaction, INUIHostedViewContext context, Action<CGSize> completion)
{
    // Do configuration here, including preparing views and calculating a desired size for presentation.
    ...

    // Return desired size
    if (completion != null)
        completion (DesiredSize ());
}

[Export ("desiredSize:")]
public CGSize DesiredSize ()
{
    return ExtensionContext.GetHostedViewMaximumAllowedSize ();
}
```

### <a name="overriding-the-default-siri-ui"></a>L'interfaccia utente di Siri predefinito viene sottoposto a override

L'estensione per interfaccia utente Intent verrà sempre visualizzato insieme ad altro contenuto Siri, ad esempio l'icona dell'app e il nome nella parte superiore dell'interfaccia utente o, in base all'intento, pulsanti, come trasmissione o Annulla, potrebbero essere visualizzati nella parte inferiore.

Sono presenti poche istanze in cui l'app può sostituire le informazioni cui Siri viene visualizzato all'utente per impostazione predefinita, come messaggistica o mappe in cui l'app può sostituire l'utilizzo predefinito con uno su misura per l'app.

Se l'estensione per interfaccia utente Intent deve eseguire l'override di elementi dell'oggetto default Siri UI, il `UIViewController` sottoclasse dovrà implementare il `IINUIHostedViewSiriProviding` interfaccia e acconsentire esplicitamente alla visualizzazione di un elemento di interfaccia specifica.

Aggiungere il codice seguente per il `UIViewController` sottoclasse per indicare a Siri di che l'estensione dell'interfaccia utente Intent è già visualizzato il contenuto del messaggio:

```csharp
public bool DisplaysMessage {
    get {return true;}
}
```

### <a name="considerations"></a>Considerazioni

Che lo sviluppatore di tenere presente quanto segue account quando si progetta e implementa le estensioni dell'interfaccia utente Intent suggerite da Apple:

- **Essere consapevoli dell'utilizzo della memoria** : le estensioni dell'interfaccia utente Intent perché sono temporanee e visualizzato solo per un breve periodo di tempo, il sistema impone limitazioni di memoria maggiore rispetto a quelle usate con un'app completa.
- **Prendere in considerazione minimo e massimo delle dimensioni di visualizzazione** -assicurarsi che le estensioni dell'interfaccia utente Intent sembra corretto in ogni tipo di dispositivo iOS, dimensioni e orientamento. Inoltre, le dimensioni desiderate che l'app invia al Siri non potrebbero essere in grado di essere concesso.
- **Usare, flessibile e modelli di Layout adattivo** , ancora una volta, per garantire che l'interfaccia utente sembra eccezionale su tutti i dispositivi.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato SiriKit e illustrato come può essere aggiunto all'App xamarin. IOS per fornire servizi accessibili all'utente tramite Siri e all'app mappe su un dispositivo iOS.




## <a name="related-links"></a>Collegamenti correlati

- [Esempio ElizaChat](https://developer.xamarin.com/samples/monotouch/ios10/ElizaChat/)
- [Guida per programmatori di SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Riferimento a Framework Intent](https://developer.apple.com/reference/intents)
- [Riferimento a Framework dell'interfaccia utente Intent](https://developer.apple.com/reference/intentsui)
- [Riferimento a domini preventivo](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)
