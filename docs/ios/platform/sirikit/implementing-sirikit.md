---
title: Implementazione di SiriKit in xamarin. IOS
description: Questo documento descrive i passaggi necessari per implementare il supporto SiriKit in un'App xamarin. IOS. Vengono illustrate le estensioni di tipi e i tipi dell'interfaccia utente.
ms.prod: xamarin
ms.assetid: 20FFB981-EB10-48BA-BF79-40F37F0291EB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 05/03/2018
ms.openlocfilehash: f0e5e05828305bd3656d70105b6e2ad06f9fdc81
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34788849"
---
# <a name="implementing-sirikit-in-xamarinios"></a>Implementazione di SiriKit in xamarin. IOS

_In questo articolo vengono illustrati i passaggi necessari per implementare il supporto di SiriKit in un'App xamarin. IOS._

Nuovo in iOS 10, SiriKit consente un'app xamarin di fornire servizi sono accessibili all'utente mediante Siri e l'app esegue il mapping in un dispositivo iOS. In questo articolo vengono illustrati i passaggi necessari per implementare il supporto di SiriKit nelle App xamarin mediante l'aggiunta di estensioni di tipi di estensioni di tipi di interfaccia utente e vocabolario.

Siri funziona con il concetto di **domini**, sapere di gruppi di azioni per attività correlate. Ogni interazione con l'app con Siri deve assumere uno dei domini servizio noto come indicato di seguito:

- Audio o video chiamata.
- Prenotazione di un interscambio.
- Gestione allenamenti.
- Messaggistica.
- Ricerca foto.
- Inviare o ricevere i pagamenti.

Quando l'utente effettua una richiesta di Siri che include uno dei servizi dell'estensione App, SiriKit invia l'estensione un **finalità** oggetto che descrive la richiesta dell'utente insieme a eventuali dati di supporto. L'estensione dell'App, quindi genera appropriata **risposta** dell'oggetto per il determinato **finalità**, che riporta in dettaglio come l'estensione può gestire la richiesta.

Questa guida verranno presentate riportato un esempio di incluso il supporto di SiriKit in un'app di esistente. Ai fini di questo esempio, verrà usato l'app MonkeyChat fittizio:

[![](implementing-sirikit-images/monkeychat01.png "L'icona MonkeyChat")](implementing-sirikit-images/monkeychat01.png#lightbox)

MonkeyChat mantiene il proprio contatto della Rubrica di amici dell'utente, ognuno associato a un nome di schermata (ad esempio Bobo ad esempio) e consente all'utente di inviare ogni amico chat di testo in base al nome della schermata.

## <a name="extending-the-app-with-sirikit"></a>Estensione dell'App con SiriKit

Come illustrato nel [SiriKit informazioni sui concetti](~/ios/platform/sirikit/understanding-sirikit.md) Guida, sono coinvolti in un'app con SiriKit estensione tre parti principali:

[![](implementing-sirikit-images/elements01.png "Estensione dell'App con SiriKit diagramma")](implementing-sirikit-images/elements01.png#lightbox)

Sono inclusi:

1. **Estensione intenti** -verifica le risposte degli utenti, conferma che l'app in grado di gestire la richiesta ed esegue effettivamente l'attività per soddisfare la richiesta dell'utente.
2. **Estensione dell'interfaccia utente intenti** - *facoltativo*, fornisce un'interfaccia utente personalizzata per le risposte nell'ambiente di Siri e poter connettere le App dell'interfaccia utente e la personalizzazione in Siri per migliorare l'esperienza dell'utente.
3. **App** -fornisce l'App con vocabolari specifici di utente per facilitare la si utilizza Siri. 

Tutti questi elementi e i passaggi da includere nell'app verrà descritta in dettaglio nelle sezioni riportate di seguito.


## <a name="preparing-the-app"></a>Preparare l'App

Tuttavia, prima di aggiungere tutte le estensioni per l'app, esistono alcuni aspetti che lo sviluppatore deve eseguire per l'adozione di SiriKit SiriKit si basa sulle estensioni.

### <a name="moving-common-shared-code"></a>Lo spostamento di codice condiviso comune 

Innanzitutto, lo sviluppatore può spostare parte del codice comune che verrà condivisi tra l'app e le estensioni in uno _progetti condivisi_, _librerie di classi portabili (PCLs)_ o _nativo Librerie_.

Le estensioni dovranno essere in grado di eseguire tutte le operazioni che esegue l'app. In termini di applicazione di esempio MonkeyChat, ad esempio la ricerca di contatti, aggiunta di nuovi contatti, inviare messaggi e recuperare la cronologia dei messaggi.

Spostando il codice comune in un progetto condiviso, libreria di classi Portabile o libreria nativa semplifica la manutenzione del codice in un'unica posizione comune e assicura che l'estensione e l'app padre forniscono uniforme esperienze e funzionalità per l'utente.

Nel caso dell'applicazione di esempio MonkeyChat, i modelli di dati e un codice di elaborazione, ad esempio l'accesso di rete e del database verranno spostati in una libreria nativa.

Seguire questa procedura:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Avviare Visual Studio per Mac e aprire l'app MonkeyChat.
2. Fare clic sul nome della soluzione nel **soluzione riempimento** e selezionare **Aggiungi** > **nuovo progetto...** : 

    [![](implementing-sirikit-images/prep01.png "Aggiungere un nuovo progetto")](implementing-sirikit-images/prep01.png#lightbox)
3. Selezionare **iOS** > **libreria** > **libreria di classi** e fare clic su di **Avanti** pulsante: 

    [![](implementing-sirikit-images/prep02.png "Selezionare una libreria di classi")](implementing-sirikit-images/prep02.png#lightbox)
4. Immettere `MonkeyChatCommon` per il **nome** e fare clic su di **crea** pulsante: 

    [![](implementing-sirikit-images/prep03.png "Immettere il nome MonkeyChatCommon")](implementing-sirikit-images/prep03.png#lightbox)
5. Fare clic su di **riferimenti** cartella dell'applicazione principale nel **Esplora** e selezionare **Modifica riferimenti...** . Controllare il **MonkeyChatCommon** sul progetto e scegliere il **OK** pulsante: 

    [![](implementing-sirikit-images/prep05.png "Archiviare il progetto MonkeyChatCommon")](implementing-sirikit-images/prep05.png#lightbox)
6. Nel **Esplora**, trascinare il codice condiviso comune dell'applicazione principale per la libreria nativa.
7. Nel caso di MonkeyChat, trascinare il **DataModels** e **processori** cartelle dall'app principale nella libreria nativa: 

    [![](implementing-sirikit-images/prep06.png "Le cartelle DataModels e processori in Esplora soluzioni")](implementing-sirikit-images/prep06.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Avviare Visual Studio e aprire l'app MonkeyChat.
2. Fare clic sul nome della soluzione nel **Esplora** e selezionare **Aggiungi** > **nuovo progetto...** .
3. Selezionare **Visual c#** > **progetto condiviso** e fare clic su di **Avanti** pulsante: 

    [![](implementing-sirikit-images/prep02.w157-sml.png "Selezionare una libreria di classi")](implementing-sirikit-images/prep02.w157.png#lightbox)
4. Immettere `MonkeyChatCommon` per il **nome** e fare clic su di **crea** pulsante.
5. Fare clic su di **riferimenti** cartella dell'applicazione principale nel **Esplora** e selezionare **Modifica riferimenti...** . Controllare il **MonkeyChatCommon** sul progetto e scegliere il **OK** pulsante: 

    [![](implementing-sirikit-images/prep05w.png "Archiviare il progetto MonkeyChatCommon")](implementing-sirikit-images/prep05w.png#lightbox)
6. Nel **Esplora**, trascinare il codice condiviso comune dell'applicazione principale per il progetto condiviso.
7. Nel caso di MonkeyChat, trascinare il **DataModels** e **processori** cartelle dall'app principale nella libreria nativa.

-----

Modificare i file che sono state spostate nella libreria nativa e lo spazio dei nomi affinché corrisponda a quello della libreria. Ad esempio la modifica `MonkeyChat` a `MonkeyChatCommon`:

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

Successivamente, tornare all'app principale e aggiungere un `using` istruzione per spazio dei nomi della libreria nativa in un punto qualsiasi dell'app utilizza una delle classi che sono state spostate:

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

### <a name="architecting-the-app-for-extensions"></a>Architettura dell'App per le estensioni

In genere, un'app verrà iscriversi a più tipi e lo sviluppatore deve assicurarsi che l'app è progettato per il numero appropriato di estensioni finalità.

Nel caso in cui un'applicazione richiede più finalità, lo sviluppatore ha la possibilità di tutta la gestione preventivo inserendo in un'unica estensione finalità o creazione di un'estensione di finalità separato per ogni scopo.

Se si sceglie di creare un'estensione di finalità separato per ogni scopo, lo sviluppatore può finire la duplicazione di una grande quantità di codice boilerplate in ogni estensione e creare una grande quantità di risorse del processore e memoria.

Per informazioni su come scegliere tra le due opzioni, vedere l'argomento se uno qualsiasi degli scopi naturalmente appartengono. Ad esempio, un'applicazione che ha effettuato chiamate audio e video potrebbe voler includere entrambi i tipi in un'unica estensione finalità come che devono svolgere attività simili e potrebbe fornire il maggior parte dei riutilizzo del codice.

Per qualsiasi scopo o il gruppo di tipi che non rientrano in un gruppo esistente, creare una nuova estensione con finalità di soluzione dell'app per contenerli.


### <a name="setting-the-required-entitlements"></a>Impostare i diritti necessari

Tutte le app xamarin che include l'integrazione di SiriKit, devono avere i diritti corretti impostato. Se lo sviluppatore non impostato correttamente assegnati tali diritti necessari, non saranno in grado di installare o testare l'app in iOS reale 10 (o versione successiva) hardware, che è anche richiesta dopo 10 iOS SiriKit non supporta il simulatore.

Seguire questa procedura:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Fare doppio clic su di `Entitlements.plist` file nel **Esplora** per aprirlo e modificarlo.
2. Passare il **origine** scheda.
3. Aggiungere il `com.apple.developer.siri` **proprietà**, impostare il **tipo** a `Boolean` e **valore** a `Yes`: 

    [![](implementing-sirikit-images/setup01.png "Aggiungere la proprietà com.apple.developer.siri")](implementing-sirikit-images/setup01.png#lightbox)
4. Salvare le modifiche apportate al file.
5. Fare doppio clic su di **File di progetto** nel **Esplora** per aprirlo e modificarlo.
6. Selezionare **firma Bundle iOS** e assicurarsi che il `Entitlements.plist` è selezionato un file nel **i diritti personalizzati** campo: 

    [![](implementing-sirikit-images/setup02.png "Selezionare il file Entitlements.plist nel campo dei diritti personalizzato")](implementing-sirikit-images/setup02.png#lightbox)
7. Fare clic su **OK** per salvare le modifiche.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Fare doppio clic su di `Entitlements.plist` file nel **Esplora** per aprirlo e modificarlo.
3. Aggiungere il `com.apple.developer.siri` **proprietà**, impostare il **tipo** a `Boolean` e **valore** a `Yes`: 

    [![](implementing-sirikit-images/setup01w.png "Aggiungere la proprietà com.apple.developer.siri")](implementing-sirikit-images/setup01w.png#lightbox)
4. Salvare le modifiche apportate al file.
5. Fare doppio clic su di **File di progetto** nel **Esplora** per aprirlo e modificarlo.
6. Selezionare **firma Bundle iOS** e assicurarsi che il `Entitlements.plist` è selezionato un file nel **i diritti personalizzati** campo.

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

### <a name="correctly-provisioning-the-app"></a>Provisioning correttamente l'App

A causa della sicurezza strict che Apple è posizionata su framework SiriKit, tutte le app xamarin che implementa SiriKit _deve_ il corretto ID App e i diritti (vedere la sezione precedente) e devono essere firmate con una corretta Profilo di provisioning.

Eseguire le operazioni seguenti nel Mac:

1. In un web browser, passare a [ http://developer.apple.com ](http://developer.apple.com) e accedere al tuo account.
2. Fare clic su **certificati**, **identificatori** e **profili**.
3. Selezionare **i profili di Provisioning** e selezionare **ID App**, quindi fare clic su di **+** pulsante.
4. Immettere un **nome** per il nuovo profilo.
5. Immettere un **ID Bundle** seguente Apple denominazione raccomandazione.
6. Scorrere verso il basso il **servizi App** selezionare **SiriKit** e fare clic su di **continua** pulsante: 

    [![](implementing-sirikit-images/setup03.png "Selezionare SiriKit")](implementing-sirikit-images/setup03.png#lightbox)
7. Verificare tutte le impostazioni, quindi **Invia** ID. l'App
8. Selezionare **i profili di Provisioning** > **sviluppo**, fare clic su di **+** pulsante, selezionare il **ID Apple**, quindi fare clic su **continua**.
9. Fare clic su Seleziona **tutti**, quindi fare clic su **continua**.
10. Fare clic su **Seleziona tutto** nuovamente, quindi fare clic su **continua**.
11. Immettere un **nome profilo** utilizzando Apple denominazione suggerimenti, quindi fare clic su **continua**.
12. Avviare Xcode.
13. Selezionare il menu di Xcode **preferenze...**
14. Selezionare **account**, quindi fare clic su di **visualizzare i dettagli...** pulsante: 

    [![](implementing-sirikit-images/setup04.png "Selezionare gli account")](implementing-sirikit-images/setup04.png#lightbox)
15. Fare clic su di **scaricare tutti i profili** pulsante nell'angolo inferiore sinistro: 

    [![](implementing-sirikit-images/setup05.png "Scaricare tutti i profili")](implementing-sirikit-images/setup05.png#lightbox)
16. Verificare che il **profilo di Provisioning** creato precedentemente è stato installato in Xcode.
17. Aprire il progetto per aggiungere il supporto di SiriKit per Visual Studio per Mac.
18. Fare doppio clic su di `Info.plist` file nel **Esplora**.
18. Verificare che il **identificatore Bundle** corrisponde a quello creato nel portale per sviluppatori di Apple precedente: 

    [![](implementing-sirikit-images/setup06.png "L'identificatore Bundle")](implementing-sirikit-images/setup06.png#lightbox)
18. Nel **Esplora**, selezionare il **progetto**.
19. Fare clic sul progetto e selezionare **opzioni**.
21. Selezionare **firma Bundle iOS**, selezionare il **identità di firma** e **profilo di Provisioning** creato in precedenza: 

    [![](implementing-sirikit-images/setup07.png "Selezionare le identità di firma e profilo di Provisioning")](implementing-sirikit-images/setup07.png#lightbox)
22. Fare clic su **OK** per salvare le modifiche.

> [!IMPORTANT]
> Testing SiriKit funziona solo su un iOS reale 10 dispositivo Hardware e non in iOS 10 simulatore. Se la presenza di problemi di installazione di un SiriKit abilitata app xamarin sull'hardware effettivo, verificare che i diritti necessari, ID di App, identificatore di firma e profilo di Provisioning siano state configurate correttamente in Apple Developer Portal sia Visual Studio per Mac.

### <a name="requesting-siri-authorization"></a>La richiesta di autorizzazione Siri

Prima che l'applicazione aggiunge i termini specifici di utente o le estensioni intenti si connette a Siri, richiede autorizzazioni all'utente di accedere a Siri.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Modifica dell'app `Info.plist` file, passare al **origine** consente di visualizzare e aggiungere il `NSSiriUsageDescription` chiave con un valore stringa che descrive l'utilizzo dell'app Siri e quali tipi di dati verranno inviati. Ad esempio, è possibile che l'app MonkeyChat "contatti MonkeyChat verranno inviati a Siri":

[![](implementing-sirikit-images/request01.png "NSSiriUsageDescription nell'editor di Info. plist")](implementing-sirikit-images/request01.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Modifica dell'app `Info.plist` file e aggiungere il `NSSiriUsageDescription` chiave con un valore stringa che descrive l'utilizzo dell'app Siri e quali tipi di dati verranno inviati. Ad esempio, è possibile che l'app MonkeyChat "contatti MonkeyChat verranno inviati a Siri":

[![](implementing-sirikit-images/request01w.png "NSSiriUsageDescription nell'editor di Info. plist")](implementing-sirikit-images/request01w.png#lightbox)

-----

Chiamare il `RequestSiriAuthorization` metodo la `INPreferences` classe al primo avvio dell'app. Modificare il `AppDelegate.cs` classe e rendere il `FinishedLaunching` metodo aspetto simile al seguente:


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

La prima volta, che questo metodo viene chiamato, viene visualizzato un avviso che richiede all'utente di consentire all'app di accedere a Siri. Il messaggio che lo sviluppatore di aggiunta il `NSSiriUsageDescription` sopra verrà visualizzato in questo avviso. Se l'utente inizialmente Nega l'accesso, è possibile utilizzare il **impostazioni** app per concedere l'accesso all'app.

In qualsiasi momento, l'app è possibile controllare il possibilità dell'app di accedere a Siri chiamando il `SiriAuthorizationStatus` metodo la `INPreferences` classe.

### <a name="localization-and-siri"></a>Localizzazione e Siri

In un dispositivo iOS, l'utente è in grado di selezionare una lingua per Siri diverso, quindi il valore predefinito di sistema. Quando si lavora con i dati localizzati, sarà necessario utilizzare l'app di `SiriLanguageCode` metodo la `INPreferences` classe per ottenere il codice della lingua da Siri. Ad esempio:

```csharp
var language = INPreferences.SiriLanguageCode();

// Take action based on language
if (language == "en-US") {
    // Do something...
}
```

### <a name="adding-user-specific-vocabulary"></a>Aggiunta utente specifico vocabolario

Il vocabolario specifico utente verrà fornito parole o frasi che sono univoche a singoli utenti dell'app. Questi verrà forniti in fase di esecuzione dall'app principale (non le estensioni App) come un set ordinato di termini, una priorità di utilizzo più significativi per gli utenti, le condizioni più importanti all'inizio dell'elenco ordinato.

Utente specifico vocabolario devono appartenere a una delle categorie seguenti:

- Nomi di contatti (che non sono gestiti dal framework contatti).
- Tag di foto.
- Nomi degli Album foto.
- Nomi allenamenti.

Quando si seleziona la terminologia per registrare come vocabolario personalizzato, selezionare solo i termini che potrebbero essere capiti da un utente non hanno familiarità con l'app. Mai register termini comuni, ad esempio "My allenamenti" o "My Album". Ad esempio, l'app MonkeyChat registrerà i nomi alternativi associati a ogni contatto nella rubrica dell'utente.

L'applicazione fornisce il vocabolario specifico utente chiamando il `SetVocabularyStrings` metodo il `INVocabulary` classe e passando un `NSOrderedSet` insieme dall'applicazione principale. L'app è necessario chiamare sempre il `RemoveAllVocabularyStrings` metodo innanzitutto rimuovere tutte le condizioni esistenti prima di aggiungerne nuovi. Ad esempio:

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

Con questo codice, potrebbe essere chiamato come segue:

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
> Siri considera vocabolario personalizzato come hint e verranno incluse come parte della terminologia come possibili. Tuttavia, lo spazio per il vocabolario personalizzato è limitata, pertanto è importante registrare _solo_ la terminologia che potrebbe risultare poco chiara, pertanto riducendo al minimo il numero totale di termini registrati.

Per ulteriori informazioni, vedere il nostro [utente specifico vocabolario riferimento](~/ios/platform/sirikit/understanding-sirikit.md) e Apple [specificando riferimenti vocabolario personalizzata](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1).

### <a name="adding-app-specific-vocabulary"></a>Aggiunta di App specifiche vocabolario

Il vocabolario specifico App definisce le parole e frasi specifiche che saranno noto a tutti gli utenti dell'applicazione, ad esempio tipi di veicoli o nomi allenamenti. Poiché questi elementi fanno parte dell'applicazione, vengono definiti un `AppIntentVocabulary.plist` file come parte del pacchetto di applicazione principale. Inoltre, queste parole e frasi devono essere localizzate.

I termini del vocabolario App specifiche devono appartenere a una delle categorie seguenti:

- Sostituire le opzioni.
- Nomi allenamenti.

Il file App specifiche vocabolario contiene due chiavi di livello principale:

- `ParameterVocabularies` **Richiesto** -definisce i parametri con finalità di si applicano a termini e condizioni personalizzati dell'app.
- `IntentPhrases` **Parametro facoltativo** -contiene frasi di esempio tramite le condizioni personalizzate definite nel `ParameterVocabularies`.

Ogni voce di `ParameterVocabularies` deve specificare una stringa di ID, termine e la finalità di cui si applica il termine. Inoltre, un singolo termine potrebbe riguardare più scopi.

Per un elenco completo dei valori accettabili e struttura dei file necessari, vedere Apple [riferimento al formato di File vocabolario App](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CustomVocabularyKeys.html#//apple_ref/doc/uid/TP40016875-CH10-SW1).

Per aggiungere un `AppIntentVocabulary.plist` file al progetto di app, eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Fare doppio clic sul nome del progetto nel **Esplora** e selezionare **Aggiungi** > **nuovo File...**   >  **iOS**:

    [![](implementing-sirikit-images/plist01.png "Aggiungere un elenco di proprietà")](implementing-sirikit-images/plist01.png#lightbox)
2. Fare doppio clic su di `AppIntentVocabulary.plist` file nel **Esplora** per aprirlo e modificarlo.
3. Fare clic su di **+** per aggiungere una chiave, impostare il **nome** a `ParameterVocabularies` e **tipo** per `Array`:

    [![](implementing-sirikit-images/plist02.png "Impostare il nome su ParameterVocabularies e il tipo di matrice")](implementing-sirikit-images/plist02.png#lightbox)
4. Espandere `ParameterVocabularies` e fare clic su di **+** pulsante e impostarne il **tipo** a `Dictionary`:

    [![](implementing-sirikit-images/plist03.png "Impostare il tipo di dizionario")](implementing-sirikit-images/plist03.png#lightbox)
5. Fare clic su di **+** per aggiungere una nuova chiave, impostare il **nome** a `ParameterNames` e **tipo** per `Array`:

    [![](implementing-sirikit-images/plist04.png "Impostare il nome su ParameterNames e il tipo di matrice")](implementing-sirikit-images/plist04.png#lightbox)
6. Fare clic su di **+** per aggiungere una nuova chiave con il **tipo** di `String` e il valore come uno dei nomi di parametro disponibili. Ad esempio, `INStartWorkoutIntent.workoutName`:

    [![](implementing-sirikit-images/plist05.png "La chiave INStartWorkoutIntent.workoutName")](implementing-sirikit-images/plist05.png#lightbox)
7. Aggiungere il `ParameterVocabulary` chiave per la `ParameterVocabularies` chiave con il **tipo** di `Array`:

    [![](implementing-sirikit-images/plist06.png "Aggiungere la chiave ParameterVocabulary alla chiave ParameterVocabularies con il tipo di matrice")](implementing-sirikit-images/plist06.png#lightbox)
8. Aggiungere una nuova chiave con il **tipo** di `Dictionary`:

    [![](implementing-sirikit-images/plist07.png "Aggiungere una nuova chiave con il tipo di dizionario")](implementing-sirikit-images/plist07.png#lightbox)
9. Aggiungere il `VocabularyItemIdentifier` chiave con il **tipo** di `String` e specificare un ID univoco per il termine:

    [![](implementing-sirikit-images/plist08.png "Aggiungere la chiave VocabularyItemIdentifier con il tipo di stringa e specificare un ID univoco")](implementing-sirikit-images/plist08.png#lightbox)
10. Aggiungere il `VocabularyItemSynonyms` chiave con il **tipo** di `Array`:

    [![](implementing-sirikit-images/plist09.png "Aggiungere la chiave VocabularyItemSynonyms con il tipo di matrice")](implementing-sirikit-images/plist09.png#lightbox)
11. Aggiungere una nuova chiave con il **tipo** di `Dictionary`:

    [![](implementing-sirikit-images/plist10.png "Aggiungere una nuova chiave con il tipo di dizionario")](implementing-sirikit-images/plist10.png#lightbox)
12. Aggiungere il `VocabularyItemPhrase` chiave con il **tipo** di `String` e il termine sta definendo l'app:

    [![](implementing-sirikit-images/plist11.png "Aggiungere la chiave VocabularyItemPhrase con il tipo di stringa e il termine sta definendo l'app")](implementing-sirikit-images/plist11.png#lightbox)
13. Aggiungere il `VocabularyItemPronunciation` chiave con il **tipo** di `String` e la pronuncia fonetica del termine:

    [![](implementing-sirikit-images/plist12.png "Aggiungere la chiave VocabularyItemPronunciation con il tipo di stringa e la pronuncia fonetica del termine")](implementing-sirikit-images/plist12.png#lightbox)
14. Aggiungere il `VocabularyItemExamples` chiave con il **tipo** di `Array`:

    [![](implementing-sirikit-images/plist13.png "Aggiungere la chiave VocabularyItemExamples con il tipo di matrice")](implementing-sirikit-images/plist13.png#lightbox)
15. Aggiungere alcuni `String` chiavi con esempi di utilizzo del termine:

    [![](implementing-sirikit-images/plist14.png "Aggiungere alcune chiavi di stringa con esempi di utilizzo del termine")](implementing-sirikit-images/plist14.png#lightbox)
16. Ripetere i passaggi precedenti per altri termini personalizzati, è necessario definire l'app.
17. Comprimere il `ParameterVocabularies` chiave.
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
23. Ripetere i passaggi precedenti per qualsiasi intenti l'app dovrà fornire l'esempio di utilizzo di.

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Destro del mouse sul nome del progetto nel **Esplora soluzioni** e selezionare **Aggiungi > Nuovo elemento... > Apple > elenco di proprietà > Info. plist**:

    [![](implementing-sirikit-images/plist01.w157-sml.png "Aggiungere un nuovo file Info. plist")](implementing-sirikit-images/plist01.w157.png#lightbox)

2. Fare doppio clic su di `AppIntentVocabulary.plist` file nel **Esplora** per aprirlo e modificarlo.
3. Fare clic su di **+** per aggiungere una chiave, impostare il **nome** a `ParameterVocabularies` e **tipo** per `Array`:

    [![](implementing-sirikit-images/plist02w.png "Impostare il nome su ParameterVocabularies e il tipo di matrice")](implementing-sirikit-images/plist02w.png#lightbox)
4. Espandere `ParameterVocabularies` e fare clic su di **+** pulsante e impostarne il **tipo** a `Dictionary`:

    [![](implementing-sirikit-images/plist03w.png "Impostare il tipo di dizionario")](implementing-sirikit-images/plist03w.png#lightbox)
5. Fare clic su di **+** per aggiungere una nuova chiave, impostare il **nome** a `ParameterNames` e **tipo** per `Array`:

    [![](implementing-sirikit-images/plist04w.png "Impostare il nome su ParameterNames e il tipo di matrice")](implementing-sirikit-images/plist04w.png#lightbox)
6. Fare clic su di **+** per aggiungere una nuova chiave con il **tipo** di `String` e il valore come uno dei nomi di parametro disponibili. Ad esempio, `INStartWorkoutIntent.workoutName`:

    [![](implementing-sirikit-images/plist05w.png "La chiave INStartWorkoutIntent.workoutName")](implementing-sirikit-images/plist05w.png#lightbox)
7. Aggiungere il `ParameterVocabulary` chiave per la `ParameterVocabularies` chiave con il **tipo** di `Array`:

    [![](implementing-sirikit-images/plist06w.png "Aggiungere la chiave ParameterVocabulary alla chiave ParameterVocabularies con il tipo di matrice")](implementing-sirikit-images/plist06w.png#lightbox)
8. Aggiungere una nuova chiave con il **tipo** di `Dictionary`:

    [![](implementing-sirikit-images/plist07w.png "Aggiungere una nuova chiave con il tipo di dizionario")](implementing-sirikit-images/plist07w.png#lightbox)
9. Aggiungere il `VocabularyItemIdentifier` chiave con il **tipo** di `String` e specificare un ID univoco per il termine:

    [![](implementing-sirikit-images/plist08w.png "Aggiungere la chiave VocabularyItemIdentifier con il tipo di stringa e specificare un ID univoco per il termine")](implementing-sirikit-images/plist08w.png#lightbox)
10. Aggiungere il `VocabularyItemSynonyms` chiave con il **tipo** di `Array`:

    [![](implementing-sirikit-images/plist09w.png "Aggiungere la chiave VocabularyItemSynonyms con il tipo di matrice")](implementing-sirikit-images/plist09w.png#lightbox)
11. Aggiungere una nuova chiave con il **tipo** di `Dictionary`:

    [![](implementing-sirikit-images/plist10w.png "Aggiungere una nuova chiave con il tipo di dizionario")](implementing-sirikit-images/plist10w.png#lightbox)
12. Aggiungere il `VocabularyItemPhrase` chiave con il **tipo** di `String` e il termine sta definendo l'app:

    [![](implementing-sirikit-images/plist11w.png "Aggiungere la chiave VocabularyItemPhrase con il tipo di stringa e il termine sta definendo l'app")](implementing-sirikit-images/plist11w.png#lightbox)
13. Aggiungere il `VocabularyItemPronunciation` chiave con il **tipo** di `String` e la pronuncia fonetica del termine:

    [![](implementing-sirikit-images/plist12w.png "Aggiungere la chiave VocabularyItemPronunciation con il tipo di stringa e la pronuncia fonetica del termine")](implementing-sirikit-images/plist12w.png#lightbox)
14. Aggiungere il `VocabularyItemExamples` chiave con il **tipo** di `Array`:

    [![](implementing-sirikit-images/plist13w.png "Aggiungere la chiave VocabularyItemExamples con il tipo di matrice")](implementing-sirikit-images/plist13w.png#lightbox)
15. Aggiungere alcuni `String` chiavi con esempi di utilizzo del termine:

    [![](implementing-sirikit-images/plist14w.png "Aggiungere alcune chiavi di stringa con esempi di utilizzo del termine")](implementing-sirikit-images/plist14w.png#lightbox)
16. Ripetere i passaggi precedenti per altri termini personalizzati, è necessario definire l'app.
17. Comprimere il `ParameterVocabularies` chiave.
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
23. Ripetere i passaggi precedenti per qualsiasi intenti l'app dovrà fornire l'esempio di utilizzo di.

-----

> [!IMPORTANT]
> Il `AppIntentVocabulary.plist` verrà registrato con Siri sui risultati dei test dispositivi durante lo sviluppo e si potrebbero richiedere del tempo per Siri incorporare il vocabolario personalizzato. Di conseguenza, il tester dovrà attendere qualche minuto prima di testare App specifiche vocabolario quando è stato aggiornato.

Per ulteriori informazioni, vedere il nostro [riferimento vocabolario specifico App](~/ios/platform/sirikit/understanding-sirikit.md) e Apple [specificando riferimenti vocabolario personalizzata](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1).

## <a name="adding-an-intents-extension"></a>Aggiunta di un'estensione di tipi

Ora che l'app è stata preparata adottare SiriKit, lo sviluppatore dovrà aggiungere estensioni di tipi (più) alla soluzione per gestire i tipi necessari per l'integrazione di Siri.

Per ogni estensione intenti necessari, eseguire le operazioni seguenti:

- Aggiungere un progetto di estensione di tipi per la soluzione dell'app xamarin. IOS.
- Configurare l'estensione intenti `Info.plist` file.
- Modificare la classe principale di estensione di tipi.

Per altre informazioni, vedere il nostro [il riferimento all'estensione di tipi](~/ios/platform/sirikit/understanding-sirikit.md) e Apple [la creazione del riferimento estensione intenti](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CreatingtheIntentsExtension.html#//apple_ref/doc/uid/TP40016875-CH4-SW1).

### <a name="creating-the-extension"></a>Creare l'estensione

Per aggiungere un'estensione di tipi per la soluzione, eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Fare clic su di **Nome soluzione** nel **soluzione riempimento** e selezionare **Aggiungi** > **Aggiungi nuovo progetto...** .
2. Nella finestra di dialogo selezionare **iOS** > **estensioni** > **estensione finalità** e fare clic su di **Avanti** pulsante: 

    [![](implementing-sirikit-images/intents05.png "Selezionare l'estensione preventivo")](implementing-sirikit-images/intents05.png#lightbox)
3. Quindi immettere un **nome** la finalità dell'estensione e fare clic su di **Avanti** pulsante: 

    [![](implementing-sirikit-images/intents06.png "Immettere un nome per l'estensione preventivo")](implementing-sirikit-images/intents06.png#lightbox)
4. Infine, fare clic su di **crea** pulsante per aggiungere l'estensione finalità per la soluzione di App: 

    [![](implementing-sirikit-images/intents07.png "Aggiungere l'estensione finalità per la soluzione di App")](implementing-sirikit-images/intents07.png#lightbox)
5. Nel **Esplora**, fare clic su di **riferimenti** cartella dell'estensione finalità appena creato. Verificare il nome del progetto di libreria codice condiviso comune (che l'app è stato creato in precedenza) e fare clic su di **OK** pulsante: 

    [![](implementing-sirikit-images/intents08.png "Selezionare il nome del progetto di libreria di codice condiviso comune")](implementing-sirikit-images/intents08.png#lightbox)
    
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Fare clic su di **Nome soluzione** nel **Esplora** e selezionare **Aggiungi** > **Aggiungi nuovo progetto...** .
2. Nella finestra di dialogo selezionare **Visual c# > iOS estensioni > estensione finalità** e fare clic sul **successivo** pulsante:

    [![](implementing-sirikit-images/intents05.w157-sml.png "Selezionare l'estensione preventivo")](implementing-sirikit-images/intents05.w157.png#lightbox)
3. Quindi immettere un **nome** la finalità dell'estensione e fare clic su di **OK** pulsante.
1. Nel **Esplora soluzioni**, fare clic sui **riferimenti** cartella dell'estensione intenti appena creato e scegliere **Aggiungi > riferimento**. Verificare il nome del progetto di libreria codice condiviso comune (che l'app è stato creato in precedenza) e fare clic su di **OK** pulsante:

    [![](implementing-sirikit-images/intents08w.png "Selezionare il nome del progetto di libreria di codice condiviso comune")](implementing-sirikit-images/intents08w.png#lightbox)
    
-----

Ripetere questi passaggi per il numero di estensioni con finalità di (in base a [architettura dell'App per le estensioni](#Architecting-the-App-for-Extensions) sezione precedente) che richiederà l'app.

### <a name="configuring-the-infoplist"></a>Configurazione di Info. plist.

Per ognuna delle estensioni di tipi aggiunti alla soluzione dell'app, deve essere configurato nel `Info.plist` file da utilizzare con l'app.

Analogamente a qualsiasi estensione App tipica, l'app avrà le chiavi esistenti di `NSExtension` e `NSExtensionAttributes`. Per un'estensione di tipi sono disponibili due nuovi attributi che devono essere configurati:

[![](implementing-sirikit-images/intents01.png "I due nuovi attributi che devono essere configurati")](implementing-sirikit-images/intents01.png#lightbox)

- **IntentsSupported** : È obbligatorio e che è costituito da una matrice di nomi di classe finalità che intende supportare dall'estensione finalità dell'applicazione.
- **IntentsRestrictedWhileLocked** -è una chiave facoltativa per l'app specificare il comportamento di schermata di blocco dell'estensione. È costituito da una matrice di nomi di classe finalità che l'app desidera richiedere all'utente di eseguire l'accesso da utilizzare dall'estensione finalità.

Per configurare l'estensione di finalità `Info.plist` file, fare doppio clic su esso nel **Esplora** per aprirlo e modificarlo. Successivamente, passare al **origine** consente di visualizzare, quindi espandere il `NSExtension` e `NSExtensionAttributes` chiavi nell'editor:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![](implementing-sirikit-images/intents02.png "Le chiavi NSExtension e NSExtensionAttributes nell'editor")](implementing-sirikit-images/intents02.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](implementing-sirikit-images/intents02w.png "Le chiavi NSExtension e NSExtensionAttributes nell'editor")](implementing-sirikit-images/intents02w.png#lightbox)

-----

Espandere il `IntentsSupported` chiave e aggiungere il nome di una classe con finalità di questa estensione supporta. Per l'app MonkeyChat di esempio, supporta il `INSendMessageIntent`:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![](implementing-sirikit-images/intents09.png "La chiave INSendMessageIntent")](implementing-sirikit-images/intents09.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](implementing-sirikit-images/intents09w.png "La chiave INSendMessageIntent")](implementing-sirikit-images/intents09w.png#lightbox)

-----

Se l'app, facoltativamente, richiede che l'utente è essere connesso al dispositivo per utilizzare un determinato finalità, espandere il `IntentRestrictedWhileLocked` chiave e aggiungere i nomi delle classi di tipi con accesso limitato. Per app di esempio MonkeyChat, l'utente deve essere connesso per inviare un messaggio di chat in modo sono state aggiunte `INSendMessageIntent`:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![](implementing-sirikit-images/intents10.png "La chiave INSendMessageIntent aggiunta")](implementing-sirikit-images/intents10.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](implementing-sirikit-images/intents10w.png "La chiave INSendMessageIntent aggiunta")](implementing-sirikit-images/intents10w.png#lightbox)

-----


Per un elenco completo dei domini con finalità di disponibili, vedere Apple [con finalità di riferimento di domini](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2).

### <a name="configuring-the-main-class"></a>Configurare la classe principale

Successivamente, sarà necessario configurare la classe principale che funge da punto di ingresso principale per l'estensione finalità in Siri lo sviluppatore. Deve essere una sottoclasse di `INExtension` che è conforme al `IINIntentHandler` delegato. Ad esempio:

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

Vi è un metodo unico metodo che l'applicazione deve implementare per la classe principale finalità dell'estensione di `GetHandler` metodo. Questo metodo viene passato un intento per SiriKit e l'app deve restituire un **finalità gestore** che corrisponde al tipo dello scopo specifico.

Poiché l'applicazione di esempio MonkeyChat gestisce solo uno scopo, restituisce se stessa nella `GetHandler` metodo. Se l'estensione gestita con più finalità, lo sviluppatore potrebbe aggiungere una classe per ogni tipo preventivo e restituire un'istanza qui in base il `Intent` passato al metodo.

### <a name="handling-the-resolve-stage"></a>Gestisce la fase di risoluzione

La fase di risoluzione è dove l'intento di estensione verrà chiarire e convalidare i parametri passati da Siri e sono state impostate tramite conversazioni dell'utente.

Per ogni parametro che viene inviato da Siri, è disponibile un `Resolve` metodo. L'app sarà necessario implementare questo metodo per ogni parametro che l'applicazione potrebbe essere necessario della Guida di Siri per ottenere la risposta corretta da parte dell'utente.

Nel caso dell'applicazione MonkeyChat di esempio, l'estensione finalità richiederà un uno o più destinatari a cui inviare il messaggio. Per ogni destinatario nell'elenco, l'estensione sarà necessario eseguire una ricerca di contatti che può avere il seguente risultato:

- È stato trovato un solo contatto corrispondente.
- Sono disponibili due o più contatti corrispondenti.
- Sono disponibili i contatti non corrispondenti.

Inoltre, MonkeyChat richiede il contenuto del corpo del messaggio. Se l'utente non ha fornito questo, Siri deve richiedere all'utente per il contenuto.

L'estensione finalità dovranno gestire correttamente a ognuno di questi casi.


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

Per ulteriori informazioni, vedere il nostro [al riferimento fase risolvere](~/ios/platform/sirikit/understanding-sirikit.md) e Apple [risoluzione e la gestione dei tipi di riferimento](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ResolvingandHandlingIntents.html#//apple_ref/doc/uid/TP40016875-CH5-SW1).

### <a name="handling-the-confirm-stage"></a>Gestisce la fase di conferma

La fase di conferma è dove l'intento di estensione controlla per verificare che dispone di tutte le informazioni per soddisfare la richiesta dell'utente. L'app desidera inviare conferma lungo verrà tutti i dettagli di supporto di ciò che sta per verificarsi a Siri in modo che può essere verificata con l'utente che si tratta dell'azione desiderata.

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

Per ulteriori informazioni, vedere il nostro [al riferimento fase confermare](~/ios/platform/sirikit/understanding-sirikit.md).

### <a name="processing-the-intent"></a>Lo scopo di elaborazione

Questo è il punto in cui l'estensione finalità esegue effettivamente l'attività per soddisfare la richiesta dell'utente e passare i risultati a Siri, pertanto l'utente possa ricevere.


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

Per ulteriori informazioni, vedere il nostro [al riferimento fase gestire](~/ios/platform/sirikit/understanding-sirikit.md).

## <a name="adding-an-intents-ui-extension"></a>Aggiunta di un'estensione dell'interfaccia utente di tipi

L'estensione dell'interfaccia utente intenti facoltativa l'opportunità di portare l'interfaccia utente dell'applicazione e personalizzazione di un'esperienza di Siri e rendere gli utenti a cui è connesso all'app. Con questa estensione per l'app può portare al marchio, nonché informazioni visual e altri nella trascrizione.

[![](implementing-sirikit-images/intentsui01.png "Un esempio di estensione dell'interfaccia utente dei tipi di output")](implementing-sirikit-images/intentsui01.png#lightbox)

Analogamente all'estensione intenti, lo sviluppatore eseguirà il passaggio seguente per l'estensione dell'interfaccia utente per scopi:

- Aggiungere un progetto di estensione dell'interfaccia utente di tipi per la soluzione dell'app xamarin. IOS.
- Configurare l'estensione dell'interfaccia utente intenti `Info.plist` file.
- Modificare la classe di estensione dell'interfaccia utente intenti principale.

Per ulteriori informazioni, vedere il nostro [il riferimento dell'estensione dell'interfaccia utente intenti](~/ios/platform/sirikit/understanding-sirikit.md) e Apple [fornendo un riferimento all'interfaccia personalizzata](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ProvidingaCustomInterface.html#//apple_ref/doc/uid/TP40016875-CH7-SW1).

### <a name="creating-the-extension"></a>Creare l'estensione

Per aggiungere un'estensione di tipi di interfaccia utente alla soluzione, eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

1. Fare clic su di **Nome soluzione** nel **soluzione riempimento** e selezionare **Aggiungi** > **Aggiungi nuovo progetto...** .
2. Nella finestra di dialogo selezionare **iOS** > **estensioni** > **estensione dell'interfaccia utente con finalità di** e fare clic su di **Avanti** pulsante: 

    [![](implementing-sirikit-images/intents11.png "Selezionare l'estensione dell'interfaccia utente preventivo")](implementing-sirikit-images/intents11.png#lightbox)
3. Quindi immettere un **nome** la finalità dell'estensione e fare clic su di **Avanti** pulsante: 

    [![](implementing-sirikit-images/intents12.png "Immettere un nome per l'estensione preventivo")](implementing-sirikit-images/intents12.png#lightbox)
4. Infine, fare clic su di **crea** pulsante per aggiungere l'estensione finalità per la soluzione di App: 

    [![](implementing-sirikit-images/intents13.png "Aggiungere l'estensione finalità per la soluzione di App")](implementing-sirikit-images/intents13.png#lightbox)
5. Nel **Esplora**, fare clic su di **riferimenti** cartella dell'estensione finalità appena creato. Verificare il nome del progetto di libreria codice condiviso comune (che l'app è stato creato in precedenza) e fare clic su di **OK** pulsante: 

    [![](implementing-sirikit-images/intents14.png "Selezionare il nome del progetto di libreria di codice condiviso comune")](implementing-sirikit-images/intents14.png#lightbox)
    
# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Fare clic su di **Nome soluzione** nel **Esplora** e selezionare **Aggiungi** > **Aggiungi nuovo progetto...**
2. Nella finestra di dialogo selezionare **iOS** > **estensioni** > **estensione dell'interfaccia utente con finalità di** e fare clic su di **Avanti** pulsante.
3. Quindi immettere un **nome** la finalità dell'estensione e fare clic su di **OK** pulsante.
5. Nel **Esplora**, fare clic su di **riferimenti** cartella dell'estensione finalità appena creato. Verificare il nome del progetto di libreria codice condiviso comune (che l'app è stato creato in precedenza) e fare clic su di **OK** pulsante.
    
-----

### <a name="configuring-the-infoplist"></a>Configurazione di Info. plist.

Configurare l'estensione dell'interfaccia utente intenti `Info.plist` file da utilizzare con l'app.

Analogamente a qualsiasi estensione App tipica, l'app avrà le chiavi esistenti di `NSExtension` e `NSExtensionAttributes`. Per un'estensione di tipi è un nuovo attributo che deve essere configurato:

[![](implementing-sirikit-images/intents03.png "Un nuovo attributo che deve essere configurato")](implementing-sirikit-images/intents03.png#lightbox)

**IntentsSupported** è obbligatorio ed è costituito da una matrice di nomi di classe finalità che desidera supportare dall'estensione finalità dell'applicazione.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

Per configurare l'estensione dell'interfaccia utente con finalità di `Info.plist` file, fare doppio clic su esso nel **Esplora** per aprirlo e modificarlo. Successivamente, passare al **origine** consente di visualizzare, quindi espandere il `NSExtension` e `NSExtensionAttributes` chiavi nell'editor:

[![](implementing-sirikit-images/intents04.png "Le chiavi NSExtension e NSExtensionAttributes nell'editor")](implementing-sirikit-images/intents04.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

Per configurare l'estensione dell'interfaccia utente con finalità di `Info.plist` file, fare doppio clic su esso nel **Esplora** per aprirlo e modificarlo. Espandere il `NSExtension` e `NSExtensionAttributes` chiavi nell'editor:

[![](implementing-sirikit-images/intents04w.png "Chiavi Tthe NSExtension e NSExtensionAttributes nell'editor")](implementing-sirikit-images/intents04w.png#lightbox)

-----

Espandere il `IntentsSupported` chiave e aggiungere il nome di una classe con finalità di questa estensione supporta. Per l'app MonkeyChat di esempio, supporta il `INSendMessageIntent`:

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio per Mac](#tab/vsmac)

[![](implementing-sirikit-images/intents15.png "La chiave INSendMessageIntent")](implementing-sirikit-images/intents15.png#lightbox)

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

[![](implementing-sirikit-images/intents15w.png "La chiave INSendMessageIntent")](implementing-sirikit-images/intents15w.png#lightbox)

-----

Per un elenco completo dei domini con finalità di disponibili, vedere Apple [con finalità di riferimento di domini](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2).

### <a name="configuring-the-main-class"></a>Configurare la classe principale

Configurare la classe principale che funge da punto di ingresso principale per l'estensione dell'interfaccia utente finalità in Siri. Deve essere una sottoclasse di `UIViewController` che è conforme al `IINUIHostedViewController` interfaccia. Ad esempio:

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

Siri passerà un `INInteraction` istanza della classe di `Configure` metodo il `UIViewController` istanza all'interno di estensione dell'interfaccia utente con finalità.

Il `INInteraction` oggetto fornisce tre tipi principali di informazioni per l'estensione:

1. L'oggetto intento in fase di elaborazione.
2. L'oggetto risposta finalità dal `Confirm` e `Handle` metodi di estensione finalità.
3. Lo stato di interazione che definisce lo stato dell'interazione tra l'applicazione e Siri.

Il `UIViewController` istanza è la classe di entità per l'interazione con Siri e perché eredita da `UIViewController`, dispone dell'accesso a tutte le funzionalità di UIKit.

Quando chiama il metodo Siri di `Configure` metodo il `UIViewController` passa in un contesto di visualizzazione che informa che il Controller di visualizzazione verrà sia ospitato in una scheda di mappe o di Siri Snippit.

Siri passerà anche in un gestore di completamento che l'app deve necessariamente restituire le dimensioni desiderate della vista, una volta terminata la configurazione dell'applicazione.

### <a name="design-the-ui-in-ios-designer"></a>Progettare l'interfaccia utente nella finestra di progettazione iOS

Layout dell'interfaccia utente dell'estensione dell'interfaccia utente intenti nella finestra di progettazione iOS. Fare doppio clic sull'estensione `MainInterface.storyboard` file nel **Esplora** per aprirlo e modificarlo. Trascinare in tutti gli elementi dell'interfaccia utente necessari per creare l'interfaccia utente e salvare le modifiche.

> [!IMPORTANT]
> Sebbene sia possibile aggiungere elementi interattivi, ad esempio `UIButtons` oppure `UITextFields` per l'estensione dell'interfaccia utente finalità `UIViewController`, questi non sono strettamente consentiti come interfaccia utente della finalità in modalità interattiva e l'utente non sarà in grado di interagire con essi.

### <a name="wire-up-the-user-interface"></a>Rete per l'interfaccia utente

Con interfaccia utente dell'estensione dell'interfaccia utente intenti creato nella finestra di progettazione iOS, è possibile modificare il `UIViewController` sottoclasse ed eseguire l'override di `Configure` metodo come indicato di seguito:

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

### <a name="overriding-the-default-siri-ui"></a>Si esegue l'override dell'interfaccia utente Siri predefinito

L'estensione dell'interfaccia utente intenti verrà sempre visualizzato insieme ad altro contenuto Siri, ad esempio l'icona dell'app e il nome nella parte superiore dell'interfaccia utente o, in base all'intento, pulsanti (ad esempio Send o Annulla) possono essere visualizzati nella parte inferiore.

Esistono alcuni casi in cui l'app è possibile sostituire le informazioni che Siri viene visualizzato all'utente per impostazione predefinita, ad esempio messaggistica o mappe in cui l'app può sostituire l'utilizzo predefinito con uno personalizzato per l'app.

Se è necessario eseguire l'override di elementi dell'oggetto default Siri UI, l'estensione dell'interfaccia utente di tipi di `UIViewController` sottoclasse sarà necessario implementare la `IINUIHostedViewSiriProviding` interfaccia e registrarsi per la visualizzazione di un elemento specifico dell'interfaccia.

Aggiungere il codice seguente per il `UIViewController` sottoclasse per indicare che l'estensione dell'interfaccia utente finalità è già visualizzato il contenuto del messaggio a Siri:

```csharp
public bool DisplaysMessage {
    get {return true;}
}
```

### <a name="considerations"></a>Considerazioni

Apple suggerisce che lo sviluppatore di eseguire le seguenti considerazioni in considerazione durante la progettazione e implementazione dell'interfaccia utente con finalità di estensioni:

- **Utilizzo della memoria consapevole di essere** : poiché le estensioni dell'interfaccia utente finalità sono temporanee e visualizzato solo per un breve periodo di tempo, il sistema impone limitazioni di memoria maggiore rispetto a vengono utilizzati con un'app completa.
- **Prendere in considerazione minimo e massimo delle dimensioni di visualizzazione** -verificare che le estensioni dell'interfaccia utente con finalità di esito positivo su ogni tipo di dispositivo iOS, dimensioni e orientamento. Inoltre, le dimensioni desiderate che l'app Invia a Siri non potrebbero essere in grado di essere concesso.
- **Utilizzare flessibile e modelli di Layout adattivo** , ancora una volta, per garantire che l'interfaccia utente sembra eccezionale su ogni dispositivo.

## <a name="summary"></a>Riepilogo

In questo articolo è coperto SiriKit e illustrato come può essere aggiunto per le app xamarin di fornire servizi sono accessibili all'utente mediante Siri e l'app esegue il mapping in un dispositivo iOS.




## <a name="related-links"></a>Collegamenti correlati

- [Esempio ElizaChat](https://developer.xamarin.com/samples/monotouch/ios10/ElizaChat/)
- [Guida per programmatori SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Tipi di riferimento di Framework](https://developer.apple.com/reference/intents)
- [Tipi di riferimento di Framework dell'interfaccia utente](https://developer.apple.com/reference/intentsui)
- [Riferimento a domini preventivo](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)
