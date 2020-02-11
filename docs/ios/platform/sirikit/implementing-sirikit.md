---
title: Implementazione di SiriKit in Xamarin.iOS
description: Questo documento descrive i passaggi necessari per implementare il supporto SiriKit in un'app Xamarin.iOS. Vengono illustrate le estensioni per gli Intent e le estensioni dell'interfaccia utente.
ms.prod: xamarin
ms.assetid: 20FFB981-EB10-48BA-BF79-40F37F0291EB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 05/03/2018
ms.openlocfilehash: a80caca0b8c8c48a468b20f63467357300bd6de1
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73031636"
---
# <a name="implementing-sirikit-in-xamarinios"></a>Implementazione di SiriKit in Xamarin.iOS

_Questo articolo illustra i passaggi necessari per implementare il supporto SiriKit in un'app Xamarin.iOS._

Una novità di iOS 10, SiriKit consente a un'app Xamarin.iOS di fornire servizi accessibili all'utente tramite Siri e l'app Maps in un dispositivo iOS. Questo articolo illustra i passaggi necessari per implementare il supporto di SiriKit nelle app Xamarin.iOS aggiungendo le estensioni per Intent, le estensioni dell'interfaccia utente e il vocabolario richiesti.

Siri funziona con il concetto di **domini**, gruppi di azioni di conoscenza per le attività correlate. Ogni interazione eseguita dall'app con Siri deve rientrare in uno dei relativi domini di servizio noti, come indicato di seguito:

- Chiamata audio o video.
- Prenotare una corsa.
- Gestione degli allenamenti.
- Messaggistica.
- Ricerca di foto.
- Invio o ricezione dei pagamenti.

Quando l'utente effettua una richiesta di Siri che interessa uno dei servizi dell'estensione dell'app, SiriKit invia all'estensione un oggetto **preventivo** che descrive la richiesta dell'utente insieme a tutti i dati di supporto. L'estensione dell'app genera quindi l'oggetto **Response** appropriato per la **finalità**specificata, in cui viene illustrato in dettaglio il modo in cui l'estensione può gestire la richiesta.

Questa guida presenta un rapido esempio di inclusione del supporto SiriKit in un'app esistente. Ai fini di questo esempio, si userà l'app Fake MonkeyChat:

[![](implementing-sirikit-images/monkeychat01.png "The MonkeyChat icon")](implementing-sirikit-images/monkeychat01.png#lightbox)

MonkeyChat mantiene il proprio libro di contatto degli amici dell'utente, ognuno associato a un nome di schermata (ad esempio, Bobo) e consente all'utente di inviare chat di testo a ogni amico con il nome dello schermo.

## <a name="extending-the-app-with-sirikit"></a>Estensione dell'app con SiriKit

Come illustrato nella Guida [Understanding SiriKit Concepts](~/ios/platform/sirikit/understanding-sirikit.md) , sono disponibili tre parti principali per l'estensione di un'app con SiriKit:

[![](implementing-sirikit-images/elements01.png "Extending the App with SiriKit diagram")](implementing-sirikit-images/elements01.png#lightbox)

tra cui:

1. **Estensione per Intent** : verifica le risposte degli utenti, conferma che l'app è in grado di gestire la richiesta ed esegue effettivamente l'attività per soddisfare la richiesta dell'utente.
2. **Estensione per interfaccia utente Intent** - *facoltativo*, fornisce un'interfaccia utente personalizzata alle risposte nell'ambiente Siri e può portare l'interfaccia utente e la personalizzazione delle app in Siri per arricchire l'esperienza dell'utente.
3. **App** : fornisce l'app con vocabolari specifici dell'utente per facilitare l'uso di Siri. 

Tutti questi elementi e i passaggi per includerli nell'app verranno descritti in dettaglio nelle sezioni riportate di seguito.

## <a name="preparing-the-app"></a>Preparazione dell'app

SiriKit si basa sulle estensioni, tuttavia, prima di aggiungere estensioni all'app, è necessario che lo sviluppatore richieda l'adozione di SiriKit.

### <a name="moving-common-shared-code"></a>Trasferimento di codice condiviso comune 

In primo luogo, lo sviluppatore può spostare parte del codice comune che verrà condiviso tra l'app e le estensioni in _progetti condivisi_, librerie di classi portabili _(classi portabili)_ o _librerie native_.

Le estensioni dovranno essere in grado di eseguire tutte le operazioni eseguite dall'app. Nei termini dell'app MonkeyChat di esempio, come trovare contatti, aggiungere nuovi contatti, inviare messaggi e recuperare la cronologia dei messaggi.

Spostando questo codice comune in un progetto condiviso, in una libreria di classi portabile o in una libreria nativa, semplifica la gestione di tale codice in un'unica posizione e garantisce che l'estensione e l'app padre forniscano esperienze e funzionalità uniformi per l'utente.

Nel caso dell'app di esempio MonkeyChat, i modelli di dati e il codice di elaborazione, ad esempio l'accesso alla rete e al database, verranno spostati in una libreria nativa.

Procedere come descritto di seguito:

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Avviare Visual Studio per Mac e aprire l'app MonkeyChat.
2. Fare clic con il pulsante destro del mouse sul nome della soluzione nel **riquadro della soluzione** e scegliere **Aggiungi** > **nuovo progetto...** : 

    [![](implementing-sirikit-images/prep01.png "Add a new project")](implementing-sirikit-images/prep01.png#lightbox)
3. Selezionare **libreria > ** **iOS** > **libreria di classi** e fare clic sul pulsante **Avanti** : 

    [![](implementing-sirikit-images/prep02.png "Select Class Library")](implementing-sirikit-images/prep02.png#lightbox)
4. Immettere `MonkeyChatCommon` per il **nome** e fare clic sul pulsante **Crea** : 

    [![](implementing-sirikit-images/prep03.png "Enter MonkeyChatCommon for the Name")](implementing-sirikit-images/prep03.png#lightbox)
5. Fare clic con il pulsante destro del mouse sulla cartella **riferimenti** dell'app principale nel **Esplora soluzioni** e scegliere **modifica riferimenti.** Controllare il progetto **MonkeyChatCommon** e fare clic sul pulsante **OK** : 

    [![](implementing-sirikit-images/prep05.png "Check the MonkeyChatCommon project")](implementing-sirikit-images/prep05.png#lightbox)
6. Nel **Esplora soluzioni**trascinare il codice condiviso comune dall'app principale alla libreria nativa.
7. Nel caso di MonkeyChat, trascinare le cartelle **Datamodes** e **Processor** dall'app principale nella libreria nativa: 

    [![](implementing-sirikit-images/prep06.png "The DataModels and Processors folders in the Solution Explorer")](implementing-sirikit-images/prep06.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Avviare Visual Studio e aprire l'app MonkeyChat.
2. Fare clic con il pulsante destro del mouse sul nome della soluzione nel **Esplora soluzioni** e scegliere **Aggiungi** > **nuovo progetto.**
3. Selezionare **Visual C#**  > **progetto condiviso** e fare clic sul pulsante **Avanti** : 

    [![](implementing-sirikit-images/prep02.w157-sml.png "Select Class Library")](implementing-sirikit-images/prep02.w157.png#lightbox)
4. Immettere `MonkeyChatCommon` come **nome** e fare clic sul pulsante **Crea** .
5. Fare clic con il pulsante destro del mouse sulla cartella **riferimenti** dell'app principale nel **Esplora soluzioni** e scegliere **modifica riferimenti.** Controllare il progetto **MonkeyChatCommon** e fare clic sul pulsante **OK** : 

    [![](implementing-sirikit-images/prep05w.png "Check the MonkeyChatCommon project")](implementing-sirikit-images/prep05w.png#lightbox)
6. Nel **Esplora soluzioni**trascinare il codice condiviso comune dall'app principale al progetto condiviso.
7. Nel caso di MonkeyChat, trascinare le cartelle **Datamodes** e **Processor** dall'app principale nella libreria nativa.

-----

Modificare i file spostati nella libreria nativa e modificare lo spazio dei nomi in modo che corrisponda a quello della libreria. Ad esempio, modificando `MonkeyChat` in `MonkeyChatCommon`:

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

Tornare quindi all'app principale e aggiungere un'istruzione `using` per lo spazio dei nomi della libreria nativa ovunque l'app usi una delle classi che sono state spostate:

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

### <a name="architecting-the-app-for-extensions"></a>Architettura dell'app per le estensioni

In genere, un'app si iscrive a più Intent e lo sviluppatore deve assicurarsi che l'applicazione sia progettata per il numero appropriato di estensioni per finalità.

Nel caso in cui un'app richieda più di un Intent, lo sviluppatore può scegliere di inserire tutte le finalità di gestione in un'estensione per finalità o di creare un'estensione per finalità separate per ogni finalità.

Se si sceglie di creare un'estensione per finalità separate per ogni finalità, lo sviluppatore potrebbe duplicare una grande quantità di codice standard in ogni estensione e creare una grande quantità di sovraccarico del processore e della memoria.

Per facilitare la scelta tra le due opzioni, vedere se una qualsiasi delle finalità appartiene naturalmente insieme. Ad esempio, un'app che ha eseguito chiamate audio e video potrebbe voler includere entrambi gli Intent in una singola estensione per finalità che gestiscono attività simili e potrebbe fornire il riutilizzo del codice.

Per finalità o gruppi di Intent che non rientrano in un gruppo esistente, creare una nuova estensione per le finalità nella soluzione dell'app per contenerli.

### <a name="setting-the-required-entitlements"></a>Impostazione dei diritti obbligatori

Per tutte le app Xamarin.iOS che includono l'integrazione con SiriKit è necessario impostare i diritti corretti. Se lo sviluppatore non imposta correttamente questi diritti obbligatori, non sarà in grado di installare o testare l'app in un hardware reale iOS 10 (o versione successiva), che è anche un requisito poiché il simulatore iOS 10 non supporta SiriKit.

Procedere come descritto di seguito:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Fare doppio clic sul file `Entitlements.plist` nel **Esplora soluzioni** per aprirlo per la modifica.
2. Passare alla scheda **origine** .
3. Aggiungere la **proprietà**`com.apple.developer.siri`, impostare il **tipo** su `Boolean` e il **valore** da `Yes`: 

    [![](implementing-sirikit-images/setup01.png "Add the com.apple.developer.siri Property")](implementing-sirikit-images/setup01.png#lightbox)
4. Salvare le modifiche apportate al file.
5. Fare doppio clic sul **file di progetto** nella **Esplora soluzioni** per aprirlo per la modifica.
6. Selezionare **firma del bundle iOS** e assicurarsi che il file `Entitlements.plist` sia selezionato nel campo **diritti personalizzati** : 

    [![](implementing-sirikit-images/setup02.png "Select the Entitlements.plist file in the Custom Entitlements field")](implementing-sirikit-images/setup02.png#lightbox)
7. Fare clic su **OK** per salvare le modifiche.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Fare doppio clic sul file `Entitlements.plist` nel **Esplora soluzioni** per aprirlo per la modifica.
2. Aggiungere la **proprietà**`com.apple.developer.siri`, impostare il **tipo** su `Boolean` e il **valore** da `Yes`: 

    [![](implementing-sirikit-images/setup01w.png "Add the com.apple.developer.siri Property")](implementing-sirikit-images/setup01w.png#lightbox)
3. Salvare le modifiche apportate al file.
4. Fare doppio clic sul **file di progetto** nella **Esplora soluzioni** per aprirlo per la modifica.
5. Selezionare **firma del bundle iOS** e assicurarsi che il file `Entitlements.plist` sia selezionato nel campo **diritti personalizzati** .

-----

Al termine, il file di `Entitlements.plist` dell'applicazione dovrebbe essere simile al seguente (in aperto in un editor esterno):

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

### <a name="correctly-provisioning-the-app"></a>Provisioning corretto dell'app

A causa della rigorosa sicurezza che Apple ha inserito in SiriKit Framework, qualsiasi app Xamarin.iOS che implementi SiriKit _deve_ avere i diritti e l'ID app corretti (vedere la sezione precedente) e deve essere firmata con un profilo di provisioning appropriato.

Nel Mac eseguire le operazioni seguenti:

1. In un Web browser passare a [https://developer.apple.com](https://developer.apple.com) e accedere all'account.
2. Fare clic su **certificati**, **identificatori** e **profili**.
3. Selezionare **profili di provisioning** e selezionare **ID app**, quindi fare clic sul pulsante **+** .
4. Immettere un **nome** per il nuovo profilo.
5. Immettere un **ID bundle** che segue la raccomandazione di denominazione di Apple.
6. Scorrere verso il basso fino alla sezione **Servizi app** , selezionare **SiriKit** e fare clic sul pulsante **continue (continua** ): 

    [![](implementing-sirikit-images/setup03.png "Select SiriKit")](implementing-sirikit-images/setup03.png#lightbox)
7. Verificare tutte le impostazioni, quindi **inviare** l'ID app.
8. Selezionare **profili di provisioning** > **sviluppo**, fare clic sul pulsante **+** , selezionare l' **ID Apple**, quindi fare clic su **continua**.
9. Fare clic su Seleziona **tutto**e quindi su **continua**.
10. Fare di nuovo clic su **Seleziona tutto** e quindi su **continua**.
11. Immettere un **nome di profilo** usando i suggerimenti per la denominazione di Apple, quindi fare clic su **continua**.
12. Avviare Xcode.
13. Dal menu Xcode selezionare **Preferenze...**
14. Selezionare **account**, quindi fare clic su **Visualizza dettagli...** pulsante 

    [![](implementing-sirikit-images/setup04.png "Select Accounts")](implementing-sirikit-images/setup04.png#lightbox)
15. Fare clic sul pulsante **Scarica tutti i profili** nell'angolo in basso a sinistra: 

    [![](implementing-sirikit-images/setup05.png "Download All Profiles")](implementing-sirikit-images/setup05.png#lightbox)
16. Verificare che il **profilo di provisioning** creato in precedenza sia stato installato in Xcode.
17. Aprire il progetto per aggiungere il supporto SiriKit a in Visual Studio per Mac.
18. Fare doppio clic sul file `Info.plist` nel **Esplora soluzioni**.
19. Verificare che l' **identificatore del bundle** corrisponda a quello creato nel portale per sviluppatori di Apple sopra: 

    [![](implementing-sirikit-images/setup06.png "The Bundle Identifier")](implementing-sirikit-images/setup06.png#lightbox)
20. Nella **Esplora soluzioni**selezionare il **progetto**.
21. Fare clic con il pulsante destro del mouse sul progetto e scegliere **Opzioni**.
22. Selezionare **firma del bundle iOS**, selezionare l' **identità di firma** e il profilo di **provisioning** creato in precedenza: 

    [![](implementing-sirikit-images/setup07.png "Select the Signing Identity and Provisioning Profile")](implementing-sirikit-images/setup07.png#lightbox)
23. Fare clic su **OK** per salvare le modifiche.

> [!IMPORTANT]
> Il test di SiriKit funziona solo in un dispositivo hardware iOS 10 reale e non nel simulatore iOS 10. Se si verificano problemi durante l'installazione di un'app Xamarin.iOS abilitata per SiriKit in un hardware reale, assicurarsi che i diritti obbligatori, l'ID app, l'identificatore di firma e il profilo di provisioning siano stati configurati correttamente nel portale per sviluppatori di Apple e Visual Studio per Mac.

### <a name="requesting-siri-authorization"></a>Richiesta dell'autorizzazione Siri

Prima che l'app aggiunga un vocabolario specifico dell'utente o le estensioni per Intent si connette a Siri, deve richiedere l'autorizzazione all'utente per accedere a Siri.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Modificare il file di `Info.plist` dell'app, passare alla visualizzazione **origine** e aggiungere la chiave `NSSiriUsageDescription` con un valore stringa che descrive il modo in cui l'app userà Siri e i tipi di dati che verranno inviati. Ad esempio, l'app MonkeyChat potrebbe indicare che "i contatti MonkeyChat verranno inviati a Siri":

[![](implementing-sirikit-images/request01.png "The NSSiriUsageDescription in the Info.plist editor")](implementing-sirikit-images/request01.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Modificare il file di `Info.plist` dell'app e aggiungere la chiave `NSSiriUsageDescription` con un valore stringa che descrive il modo in cui l'app userà Siri e i tipi di dati che verranno inviati. Ad esempio, l'app MonkeyChat potrebbe indicare che "i contatti MonkeyChat verranno inviati a Siri":

[![](implementing-sirikit-images/request01w.png "The NSSiriUsageDescription in the Info.plist editor")](implementing-sirikit-images/request01w.png#lightbox)

-----

Chiamare il metodo `RequestSiriAuthorization` della classe `INPreferences` quando l'app viene avviata per la prima volta. Modificare la classe `AppDelegate.cs` e fare in modo che il metodo `FinishedLaunching` sia simile al seguente:

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

La prima volta che viene chiamato questo metodo, viene visualizzato un avviso in cui viene richiesto all'utente di consentire all'app di accedere a Siri. Il messaggio che lo sviluppatore ha aggiunto al `NSSiriUsageDescription` precedente verrà visualizzato in questo avviso. Se inizialmente l'utente nega l'accesso, può usare l'app **Impostazioni** per concedere l'accesso all'app.

In qualsiasi momento, l'app può controllare la capacità dell'app di accedere a Siri chiamando il metodo `SiriAuthorizationStatus` della classe `INPreferences`.

### <a name="localization-and-siri"></a>Localizzazione e Siri

In un dispositivo iOS, l'utente è in grado di selezionare una lingua per Siri diversa da quella predefinita del sistema. Quando si lavora con i dati localizzati, l'app deve usare il metodo `SiriLanguageCode` della classe `INPreferences` per ottenere il codice lingua da Siri. Esempio:

```csharp
var language = INPreferences.SiriLanguageCode();

// Take action based on language
if (language == "en-US") {
    // Do something...
}
```

### <a name="adding-user-specific-vocabulary"></a>Aggiunta del vocabolario specifico dell'utente

Il vocabolario specifico dell'utente fornirà parole o frasi univoche per i singoli utenti dell'app. Questi verranno forniti in fase di esecuzione dall'app principale (non dalle estensioni dell'app) come set ordinato di termini, ordinati in base alla priorità di utilizzo più significativa per gli utenti, con le condizioni più importanti all'inizio dell'elenco.

Il vocabolario specifico dell'utente deve appartenere a una delle categorie seguenti:

- Nomi dei contatti (non gestiti dal Framework contacts).
- Tag foto.
- Nomi degli album di foto.
- Nomi degli allenamenti.

Quando si seleziona la terminologia da registrare come vocabolario personalizzato, scegliere solo i termini che potrebbero essere fraintesi da un utente che non ha familiarità con l'app. Non registrare mai termini comuni, ad esempio "My workout" o "My album". Ad esempio, l'app MonkeyChat registrerà i nomi alternativi associati a ogni contatto nella rubrica dell'utente.

L'app fornisce il vocabolario specifico dell'utente chiamando il metodo `SetVocabularyStrings` della classe `INVocabulary` e passando una raccolta di `NSOrderedSet` dall'app principale. L'app deve sempre chiamare prima il metodo `RemoveAllVocabularyStrings` per rimuovere eventuali termini esistenti prima di aggiungerne di nuovi. Esempio:

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
> Siri considera il vocabolario personalizzato come hint e incorpora la maggior parte della terminologia possibile. Tuttavia, lo spazio per il vocabolario personalizzato è limitato rendendo importante la registrazione _solo_ della terminologia che potrebbe creare confusione, mantenendo al minimo il numero totale di condizioni registrate.

Per altre informazioni, vedere il [riferimento al vocabolario specifico dell'utente](~/ios/platform/sirikit/understanding-sirikit.md) e Apple [specificando il riferimento al vocabolario personalizzato](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1).

### <a name="adding-app-specific-vocabulary"></a>Aggiunta del vocabolario specifico dell'app

Il vocabolario specifico dell'app definisce le parole e le frasi specifiche che saranno note a tutti gli utenti dell'app, ad esempio i tipi di veicolo o i nomi degli allenamenti. Poiché queste sono parte dell'applicazione, sono definite in un file di `AppIntentVocabulary.plist` come parte del bundle dell'app principale. Inoltre, le parole e le frasi devono essere localizzate.

I termini del vocabolario specifici dell'app devono appartenere a una delle categorie seguenti:

- Opzioni di corsa.
- Nomi degli allenamenti.

Il file del vocabolario specifico dell'app contiene due chiavi a livello radice:

- `ParameterVocabularies` **obbligatorio** : definisce i termini e i parametri di finalità personalizzati dell'app a cui si applicano.
- `IntentPhrases` **facoltativo** : contiene frasi di esempio che usano i termini personalizzati definiti nel `ParameterVocabularies`.

Ogni voce nella `ParameterVocabularies` deve specificare una stringa ID, un termine e l'intento a cui si applica il termine. Inoltre, un singolo termine può essere applicato a più Intent.

Per un elenco completo dei valori accettabili e della struttura dei file necessari, vedere le informazioni di riferimento sul formato di file per il [vocabolario delle app](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CustomVocabularyKeys.html#//apple_ref/doc/uid/TP40016875-CH10-SW1)Apple.

Per aggiungere un file di `AppIntentVocabulary.plist` al progetto dell'app, eseguire le operazioni seguenti:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Fare clic con il pulsante destro del mouse sul nome del progetto nella **Esplora soluzioni** e scegliere **Aggiungi** > **nuovo file...**  > **iOS**:

    [![](implementing-sirikit-images/plist01.png "Add a property list")](implementing-sirikit-images/plist01.png#lightbox)
2. Fare doppio clic sul file `AppIntentVocabulary.plist` nel **Esplora soluzioni** per aprirlo per la modifica.
3. Fare clic sul **+** per aggiungere una chiave, impostare il **nome** su `ParameterVocabularies` e il **tipo** su `Array`:

    [![](implementing-sirikit-images/plist02.png "Set the Name to ParameterVocabularies and the Type to Array")](implementing-sirikit-images/plist02.png#lightbox)
4. Espandere `ParameterVocabularies`, fare clic sul pulsante **+** e impostare il **tipo** su `Dictionary`:

    [![](implementing-sirikit-images/plist03.png "Set the Type to Dictionary")](implementing-sirikit-images/plist03.png#lightbox)
5. Fare clic sul **+** per aggiungere una nuova chiave, impostare il **nome** su `ParameterNames` e il **tipo** su `Array`:

    [![](implementing-sirikit-images/plist04.png "Set the Name to ParameterNames and the Type to Array")](implementing-sirikit-images/plist04.png#lightbox)
6. Fare clic sul **+** per aggiungere una nuova chiave con il **tipo** di `String` e il valore come uno dei nomi di parametro disponibili. Ad esempio, `INStartWorkoutIntent.workoutName`:

    [![](implementing-sirikit-images/plist05.png "The INStartWorkoutIntent.workoutName key")](implementing-sirikit-images/plist05.png#lightbox)
7. Aggiungere la chiave di `ParameterVocabulary` alla chiave `ParameterVocabularies` con il **tipo** di `Array`:

    [![](implementing-sirikit-images/plist06.png "Add the ParameterVocabulary key to the ParameterVocabularies key with the Type of Array")](implementing-sirikit-images/plist06.png#lightbox)
8. Aggiungere una nuova chiave con il **tipo** di `Dictionary`:

    [![](implementing-sirikit-images/plist07.png "Add a new key with the Type of Dictionary")](implementing-sirikit-images/plist07.png#lightbox)
9. Aggiungere la chiave di `VocabularyItemIdentifier` con il **tipo** di `String` e specificare un ID univoco per il termine:

    [![](implementing-sirikit-images/plist08.png "Add the VocabularyItemIdentifier key with the Type of String and specify a unique ID")](implementing-sirikit-images/plist08.png#lightbox)
10. Aggiungere la chiave di `VocabularyItemSynonyms` con il **tipo** di `Array`:

    [![](implementing-sirikit-images/plist09.png "Add the VocabularyItemSynonyms key with the Type of Array")](implementing-sirikit-images/plist09.png#lightbox)
11. Aggiungere una nuova chiave con il **tipo** di `Dictionary`:

    [![](implementing-sirikit-images/plist10.png "Add a new key with the Type of Dictionary")](implementing-sirikit-images/plist10.png#lightbox)
12. Aggiungere la chiave di `VocabularyItemPhrase` con il **tipo** di `String` e il termine definito dall'app:

    [![](implementing-sirikit-images/plist11.png "Add the VocabularyItemPhrase key with the Type of String and the term the app are defining")](implementing-sirikit-images/plist11.png#lightbox)
13. Aggiungere la chiave di `VocabularyItemPronunciation` con il **tipo** di `String` e la pronuncia fonetica del termine:

    [![](implementing-sirikit-images/plist12.png "Add the VocabularyItemPronunciation key with the Type of String and the phonetic pronunciation of the term")](implementing-sirikit-images/plist12.png#lightbox)
14. Aggiungere la chiave di `VocabularyItemExamples` con il **tipo** di `Array`:

    [![](implementing-sirikit-images/plist13.png "Add the VocabularyItemExamples key with the Type of Array")](implementing-sirikit-images/plist13.png#lightbox)
15. Aggiungere alcune chiavi `String` con esempi di utilizzo del termine:

    [![](implementing-sirikit-images/plist14.png "Add a few String keys with example uses of the term")](implementing-sirikit-images/plist14.png#lightbox)
16. Ripetere i passaggi precedenti per tutti gli altri termini personalizzati che l'app deve definire.
17. Comprime la chiave `ParameterVocabularies`.
18. Aggiungere la chiave di `IntentPhrases` con il **tipo** di `Array`:

    [![](implementing-sirikit-images/plist15.png "Add the IntentPhrases key with the Type of Array")](implementing-sirikit-images/plist15.png#lightbox)
19. Aggiungere una nuova chiave con il **tipo** di `Dictionary`:

    [![](implementing-sirikit-images/plist16.png "Add a new key with the Type of Dictionary")](implementing-sirikit-images/plist16.png#lightbox)
20. Aggiungere la chiave di `IntentName` con il **tipo** di `String` e finalità per l'esempio:

    [![](implementing-sirikit-images/plist17.png "Add the IntentName key with the Type of String and Intent for the example")](implementing-sirikit-images/plist17.png#lightbox)
21. Aggiungere la chiave di `IntentExamples` con il **tipo** di `Array`:

    [![](implementing-sirikit-images/plist18.png "Add the IntentExamples key with the Type of Array")](implementing-sirikit-images/plist18.png#lightbox)
22. Aggiungere alcune chiavi `String` con esempi di utilizzo del termine:

    [![](implementing-sirikit-images/plist19.png "Add a few String keys with example uses of the term")](implementing-sirikit-images/plist19.png#lightbox)
23. Ripetere i passaggi precedenti per gli Intent necessari all'app per fornire esempi di utilizzo di.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Fare clic con il pulsante destro del mouse sul nome del progetto nella **Esplora soluzioni** e scegliere **Aggiungi > nuovo elemento... > Elenco di proprietà di Apple > > info. plist**:

    [![](implementing-sirikit-images/plist01.w157-sml.png "Add a new Info.plist")](implementing-sirikit-images/plist01.w157.png#lightbox)

2. Fare doppio clic sul file `AppIntentVocabulary.plist` nel **Esplora soluzioni** per aprirlo per la modifica.
3. Fare clic sul **+** per aggiungere una chiave, impostare il **nome** su `ParameterVocabularies` e il **tipo** su `Array`:

    [![](implementing-sirikit-images/plist02w.png "Set the Name to ParameterVocabularies and the Type to Array")](implementing-sirikit-images/plist02w.png#lightbox)
4. Espandere `ParameterVocabularies`, fare clic sul pulsante **+** e impostare il **tipo** su `Dictionary`:

    [![](implementing-sirikit-images/plist03w.png "Set the Type to Dictionary")](implementing-sirikit-images/plist03w.png#lightbox)
5. Fare clic sul **+** per aggiungere una nuova chiave, impostare il **nome** su `ParameterNames` e il **tipo** su `Array`:

    [![](implementing-sirikit-images/plist04w.png "Set the Name to ParameterNames and the Type to Array")](implementing-sirikit-images/plist04w.png#lightbox)
6. Fare clic sul **+** per aggiungere una nuova chiave con il **tipo** di `String` e il valore come uno dei nomi di parametro disponibili. Ad esempio, `INStartWorkoutIntent.workoutName`:

    [![](implementing-sirikit-images/plist05w.png "The INStartWorkoutIntent.workoutName key")](implementing-sirikit-images/plist05w.png#lightbox)
7. Aggiungere la chiave di `ParameterVocabulary` alla chiave `ParameterVocabularies` con il **tipo** di `Array`:

    [![](implementing-sirikit-images/plist06w.png "Add the ParameterVocabulary key to the ParameterVocabularies key with the Type of Array")](implementing-sirikit-images/plist06w.png#lightbox)
8. Aggiungere una nuova chiave con il **tipo** di `Dictionary`:

    [![](implementing-sirikit-images/plist07w.png "Add a new key with the Type of Dictionary")](implementing-sirikit-images/plist07w.png#lightbox)
9. Aggiungere la chiave di `VocabularyItemIdentifier` con il **tipo** di `String` e specificare un ID univoco per il termine:

    [![](implementing-sirikit-images/plist08w.png "Add the VocabularyItemIdentifier key with the Type of String and specify a unique ID for the term")](implementing-sirikit-images/plist08w.png#lightbox)
10. Aggiungere la chiave di `VocabularyItemSynonyms` con il **tipo** di `Array`:

    [![](implementing-sirikit-images/plist09w.png "Add the VocabularyItemSynonyms key with the Type of Array")](implementing-sirikit-images/plist09w.png#lightbox)
11. Aggiungere una nuova chiave con il **tipo** di `Dictionary`:

    [![](implementing-sirikit-images/plist10w.png "Add a new key with the Type of Dictionary")](implementing-sirikit-images/plist10w.png#lightbox)
12. Aggiungere la chiave di `VocabularyItemPhrase` con il **tipo** di `String` e il termine definito dall'app:

    [![](implementing-sirikit-images/plist11w.png "Add the VocabularyItemPhrase key with the Type of String and the term the app are defining")](implementing-sirikit-images/plist11w.png#lightbox)
13. Aggiungere la chiave di `VocabularyItemPronunciation` con il **tipo** di `String` e la pronuncia fonetica del termine:

    [![](implementing-sirikit-images/plist12w.png "Add the VocabularyItemPronunciation key with the Type of String and the phonetic pronunciation of the term")](implementing-sirikit-images/plist12w.png#lightbox)
14. Aggiungere la chiave di `VocabularyItemExamples` con il **tipo** di `Array`:

    [![](implementing-sirikit-images/plist13w.png "Add the VocabularyItemExamples key with the Type of Array")](implementing-sirikit-images/plist13w.png#lightbox)
15. Aggiungere alcune chiavi `String` con esempi di utilizzo del termine:

    [![](implementing-sirikit-images/plist14w.png "Add a few String keys with example uses of the term")](implementing-sirikit-images/plist14w.png#lightbox)
16. Ripetere i passaggi precedenti per tutti gli altri termini personalizzati che l'app deve definire.
17. Comprime la chiave `ParameterVocabularies`.
18. Aggiungere la chiave di `IntentPhrases` con il **tipo** di `Array`:

    [![](implementing-sirikit-images/plist15w.png "Add the IntentPhrases key with the Type of Array")](implementing-sirikit-images/plist15w.png#lightbox)
19. Aggiungere una nuova chiave con il **tipo** di `Dictionary`:

    [![](implementing-sirikit-images/plist16w.png "Add a new key with the Type of Dictionary")](implementing-sirikit-images/plist16w.png#lightbox)
20. Aggiungere la chiave di `IntentName` con il **tipo** di `String` e finalità per l'esempio:

    [![](implementing-sirikit-images/plist17w.png "Add the IntentName key with the Type of String and Intent for the example")](implementing-sirikit-images/plist17w.png#lightbox)
21. Aggiungere la chiave di `IntentExamples` con il **tipo** di `Array`:

    [![](implementing-sirikit-images/plist18w.png "Add the IntentExamples key with the Type of Array")](implementing-sirikit-images/plist18w.png#lightbox)
22. Aggiungere alcune chiavi `String` con esempi di utilizzo del termine:

    [![](implementing-sirikit-images/plist19w.png "Add a few String keys with example uses of the term")](implementing-sirikit-images/plist19w.png#lightbox)
23. Ripetere i passaggi precedenti per gli Intent necessari all'app per fornire esempi di utilizzo di.

-----

> [!IMPORTANT]
> Il `AppIntentVocabulary.plist` verrà registrato con Siri nei dispositivi di test durante lo sviluppo e potrebbe essere necessario un po' di tempo prima che Siri includa il vocabolario personalizzato. Di conseguenza, il tester dovrà attendere alcuni minuti prima di provare a testare il vocabolario specifico dell'app quando è stato aggiornato.

Per altre informazioni, vedere il [riferimento al vocabolario specifico dell'app](~/ios/platform/sirikit/understanding-sirikit.md) e Apple [specificando il riferimento al vocabolario personalizzato](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SpecifyingCustomVocabulary.html#//apple_ref/doc/uid/TP40016875-CH6-SW1).

## <a name="adding-an-intents-extension"></a>Aggiunta di un'estensione per Intent

Ora che l'app è stata preparata per l'adozione di SiriKit, lo sviluppatore dovrà aggiungere una o più estensioni per Intent alla soluzione per gestire gli Intent necessari per l'integrazione con Siri.

Per ogni estensione per Intent è necessario eseguire le operazioni seguenti:

- Aggiungere un progetto di estensione per Intent alla soluzione app Xamarin.iOS.
- Configurare il file di estensione per Intent `Info.plist`.
- Modificare la classe principale dell'estensione per Intent.

Per altre informazioni, vedere le informazioni di [riferimento sull'estensione per Intent](~/ios/platform/sirikit/understanding-sirikit.md) e la pagina relativa alla [creazione del riferimento all'estensione per Intent](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/CreatingtheIntentsExtension.html#//apple_ref/doc/uid/TP40016875-CH4-SW1).

### <a name="creating-the-extension"></a>Creazione dell'estensione

Per aggiungere un'estensione per Intent alla soluzione, procedere come segue:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Fare clic con il pulsante destro del mouse sul **nome della soluzione** nel **riquadro della soluzione** e scegliere **Aggiungi** > **Aggiungi nuovo progetto.**
2. Nella finestra di dialogo selezionare **iOS** > **Extensions** > **estensione per finalità** e fare clic sul pulsante **Avanti** : 

    [![](implementing-sirikit-images/intents05.png "Select Intent Extension")](implementing-sirikit-images/intents05.png#lightbox)
3. Immettere quindi un **nome** per l'estensione finalità e fare clic sul pulsante **Avanti** : 

    [![](implementing-sirikit-images/intents06.png "Enter a Name for the Intent Extension")](implementing-sirikit-images/intents06.png#lightbox)
4. Infine, fare clic sul pulsante **Crea** per aggiungere l'estensione per finalità alla soluzione app: 

    [![](implementing-sirikit-images/intents07.png "Add the Intent Extension to the apps solution")](implementing-sirikit-images/intents07.png#lightbox)
5. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sulla cartella **riferimenti** dell'estensione per finalità appena creata. Controllare il nome del progetto di libreria di codice condiviso comune (creato dall'app precedente) e fare clic sul pulsante **OK** : 

    [![](implementing-sirikit-images/intents08.png "Select the name of the common shared code library project")](implementing-sirikit-images/intents08.png#lightbox)
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Fare clic con il pulsante destro del mouse sul **nome della soluzione** nel **Esplora soluzioni** e scegliere **Aggiungi** > **Aggiungi nuovo progetto.**
2. Nella finestra di dialogo selezionare **Visual C# > iOS Extensions > estensione per finalità** e fare clic sul pulsante **Avanti** :

    [![](implementing-sirikit-images/intents05.w157-sml.png "Select Intent Extension")](implementing-sirikit-images/intents05.w157.png#lightbox)
3. Immettere quindi un **nome** per l'estensione finalità e fare clic sul pulsante **OK** .
4. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sulla cartella **riferimenti** dell'estensione per Intents appena creata e scegliere **Aggiungi > riferimento**. Controllare il nome del progetto di libreria di codice condiviso comune (creato dall'app precedente) e fare clic sul pulsante **OK** :

    [![](implementing-sirikit-images/intents08w.png "Select the name of the common shared code library project")](implementing-sirikit-images/intents08w.png#lightbox)
    
-----

Ripetere questi passaggi per il numero di estensioni per finalità (basate sull' [architettura dell'app per le estensioni](#architecting-the-app-for-extensions) precedente) richieste dall'app.

### <a name="configuring-the-infoplist"></a>Configurazione di info. plist

Per ogni estensione per Intent che è stata aggiunta alla soluzione dell'app, deve essere configurata nei file di `Info.plist` per l'uso con l'app.

Analogamente a qualsiasi estensione di app tipica, l'app avrà le chiavi esistenti di `NSExtension` e `NSExtensionAttributes`. Per un'estensione per Intent è necessario configurare due nuovi attributi:

[![](implementing-sirikit-images/intents01.png "The two new attributes that must be configured")](implementing-sirikit-images/intents01.png#lightbox)

- **IntentsSupported** : è obbligatorio ed è costituito da una matrice di nomi di classe Intent che l'app vuole supportare dall'estensione per finalità.
- **IntentsRestrictedWhileLocked** -chiave facoltativa per l'app per specificare il comportamento della schermata di blocco dell'estensione. È costituito da una matrice di nomi di classe Intent che l'app vuole richiedere che l'utente sia connesso per l'uso dall'estensione per finalità.

Per configurare il file di `Info.plist` dell'estensione Intent, fare doppio clic su di esso nel **Esplora soluzioni** per aprirlo per la modifica. Passare quindi alla visualizzazione **origine** , quindi espandere il `NSExtension` e `NSExtensionAttributes` chiavi nell'Editor:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](implementing-sirikit-images/intents02.png "The NSExtension and NSExtensionAttributes keys in the editor")](implementing-sirikit-images/intents02.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents02w.png "The NSExtension and NSExtensionAttributes keys in the editor")](implementing-sirikit-images/intents02w.png#lightbox)

-----

Espandere la chiave di `IntentsSupported` e aggiungere il nome di una classe di finalità che verrà supportata da questa estensione. Per l'app MonkeyChat di esempio, supporta il `INSendMessageIntent`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](implementing-sirikit-images/intents09.png "The INSendMessageIntent key")](implementing-sirikit-images/intents09.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents09w.png "The INSendMessageIntent key")](implementing-sirikit-images/intents09w.png#lightbox)

-----

Se l'app richiede facoltativamente che l'utente sia connesso al dispositivo per usare un determinato scopo, espandere la chiave `IntentRestrictedWhileLocked` e aggiungere i nomi delle classi degli Intent con accesso limitato. Per l'app MonkeyChat di esempio, l'utente deve essere connesso per inviare un messaggio di chat per aggiungere `INSendMessageIntent`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](implementing-sirikit-images/intents10.png "The added INSendMessageIntent key")](implementing-sirikit-images/intents10.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents10w.png "The added INSendMessageIntent key")](implementing-sirikit-images/intents10w.png#lightbox)

-----

Per un elenco completo dei domini per finalità disponibili, vedere le informazioni di [riferimento sui domini Intent](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)di Apple.

### <a name="configuring-the-main-class"></a>Configurazione della classe principale

Successivamente, lo sviluppatore dovrà configurare la classe principale che funge da punto di ingresso principale per l'estensione della finalità in Siri. Deve essere una sottoclasse di `INExtension` conforme al delegato di `IINIntentHandler`. Esempio:

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

È necessario che l'app implementi un solo metodo solitario sulla classe principale dell'estensione Intent, il metodo `GetHandler`. A questo metodo viene passato un preventivo da SiriKit e l'app deve restituire un **gestore preventivo** corrispondente al tipo della finalità specificata.

Poiché l'app MonkeyChat di esempio gestisce un solo scopo, viene restituito nel metodo `GetHandler`. Se l'estensione ha gestito più finalità, lo sviluppatore aggiunge una classe per ogni tipo di Intent e restituisce un'istanza in base al `Intent` passato al metodo.

### <a name="handling-the-resolve-stage"></a>Gestione della fase di risoluzione

La fase di risoluzione è il punto in cui l'estensione finalità chiarirà e convaliderà i parametri passati da Siri e sarà stata impostata tramite la conversazione dell'utente.

Per ogni parametro che viene inviato da Siri è presente un metodo `Resolve`. L'app dovrà implementare questo metodo per ogni parametro che l'app potrebbe richiedere alla guida di Siri per ottenere la risposta corretta dall'utente.

Nel caso dell'app MonkeyChat di esempio, l'estensione per finalità richiederà uno o più destinatari a cui inviare il messaggio. Per ogni destinatario nell'elenco, l'estensione dovrà eseguire una ricerca di contatti che può avere il risultato seguente:

- Viene trovato esattamente un contatto corrispondente.
- Sono stati trovati due o più contatti corrispondenti.
- Nessun contatto corrispondente trovato.

Inoltre, MonkeyChat richiede contenuto per il corpo del messaggio. Se l'utente non lo ha specificato, Siri deve richiedere all'utente il contenuto.

L'estensione per finalità dovrà gestire correttamente ognuno di questi casi.

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

Per altre informazioni, vedere la Guida di riferimento per [la fase di risoluzione e la risoluzione dei](~/ios/platform/sirikit/understanding-sirikit.md) [problemi di gestione degli Intent](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ResolvingandHandlingIntents.html#//apple_ref/doc/uid/TP40016875-CH5-SW1)in Apple.

### <a name="handling-the-confirm-stage"></a>Gestione della fase di conferma

La fase di conferma è il punto in cui l'estensione preventivo verifica che disponga di tutte le informazioni per soddisfare la richiesta dell'utente. L'app vuole inviare la conferma insieme a tutti i dettagli di supporto di ciò che sta per succedere a Siri, in modo che possa essere confermata con l'utente che si tratta dell'azione desiderata.

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

Per ulteriori informazioni, vedere la Guida di [riferimento per la fase di conferma](~/ios/platform/sirikit/understanding-sirikit.md).

### <a name="processing-the-intent"></a>Elaborazione della finalità

Questo è il punto in cui l'estensione finalità esegue effettivamente l'attività per soddisfare la richiesta dell'utente e passa i risultati a Siri in modo che l'utente possa essere informato.

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

Per altre informazioni, vedere [il riferimento per la fase di handle](~/ios/platform/sirikit/understanding-sirikit.md).

## <a name="adding-an-intents-ui-extension"></a>Aggiunta di un'estensione per interfaccia utente Intent

L'estensione facoltativa dell'interfaccia utente di Intent è la possibilità di portare l'interfaccia utente e la personalizzazione dell'app nell'esperienza Siri e di fare in modo che gli utenti si sentano connessi all'app. Con questa estensione, l'app può portare il marchio, nonché informazioni visive e altre informazioni nella trascrizione.

[![](implementing-sirikit-images/intentsui01.png "An example Intents UI Extension output")](implementing-sirikit-images/intentsui01.png#lightbox)

Analogamente all'estensione per Intent, lo sviluppatore eseguirà il passaggio seguente per l'estensione dell'interfaccia utente Intent:

- Aggiungere un progetto di estensione per interfaccia utente Intent alla soluzione app Xamarin.iOS.
- Configurare l'estensione per interfaccia utente Intent `Info.plist` file.
- Modificare la classe principale dell'estensione dell'interfaccia utente di Intent.

Per ulteriori informazioni, vedere la Guida di [riferimento per l'estensione dell'interfaccia utente Intent](~/ios/platform/sirikit/understanding-sirikit.md) e Apple [fornisce un riferimento all'interfaccia personalizzata](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/ProvidingaCustomInterface.html#//apple_ref/doc/uid/TP40016875-CH7-SW1).

### <a name="creating-the-extension"></a>Creazione dell'estensione

Per aggiungere un'estensione per interfaccia utente Intent alla soluzione, procedere come segue:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

1. Fare clic con il pulsante destro del mouse sul **nome della soluzione** nel **riquadro della soluzione** e scegliere **Aggiungi** > **Aggiungi nuovo progetto.**
2. Nella finestra di dialogo selezionare **iOS** > **Extensions** > **estensione dell'interfaccia utente** e fare clic sul pulsante **Avanti** : 

    [![](implementing-sirikit-images/intents11.png "Select Intent UI Extension")](implementing-sirikit-images/intents11.png#lightbox)
3. Immettere quindi un **nome** per l'estensione finalità e fare clic sul pulsante **Avanti** : 

    [![](implementing-sirikit-images/intents12.png "Enter a Name for the Intent Extension")](implementing-sirikit-images/intents12.png#lightbox)
4. Infine, fare clic sul pulsante **Crea** per aggiungere l'estensione per finalità alla soluzione app: 

    [![](implementing-sirikit-images/intents13.png "Add the Intent Extension to the apps solution")](implementing-sirikit-images/intents13.png#lightbox)
5. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sulla cartella **riferimenti** dell'estensione per finalità appena creata. Controllare il nome del progetto di libreria di codice condiviso comune (creato dall'app precedente) e fare clic sul pulsante **OK** : 

    [![](implementing-sirikit-images/intents14.png "Select the name of the common shared code library project")](implementing-sirikit-images/intents14.png#lightbox)
    
# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

1. Fare clic con il pulsante destro del mouse sul **nome della soluzione** nel **Esplora soluzioni** e scegliere **Aggiungi** > **Aggiungi nuovo progetto...**
2. Nella finestra di dialogo selezionare **iOS** > **Extensions** > **estensione dell'interfaccia utente Intent** e fare clic sul pulsante **Avanti** .
3. Immettere quindi un **nome** per l'estensione finalità e fare clic sul pulsante **OK** .
4. Nella **Esplora soluzioni**fare clic con il pulsante destro del mouse sulla cartella **riferimenti** dell'estensione per finalità appena creata. Controllare il nome del progetto di libreria di codice condiviso comune (creato dall'app precedente) e fare clic sul pulsante **OK** .
    
-----

### <a name="configuring-the-infoplist"></a>Configurazione di info. plist

Configurare il file di `Info.plist` dell'estensione dell'interfaccia utente Intent in modo che funzioni con l'app.

Analogamente a qualsiasi estensione di app tipica, l'app avrà le chiavi esistenti di `NSExtension` e `NSExtensionAttributes`. Per un'estensione per Intent è necessario configurare un nuovo attributo:

[![](implementing-sirikit-images/intents03.png "The one new attribute that must be configured")](implementing-sirikit-images/intents03.png#lightbox)

**IntentsSupported** è obbligatorio ed è costituito da una matrice di nomi di classe Intent che l'app vuole supportare dall'estensione per finalità.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

Per configurare il file di `Info.plist` dell'estensione dell'interfaccia utente Intent, fare doppio clic su di esso nella **Esplora soluzioni** per aprirlo per la modifica. Passare quindi alla visualizzazione **origine** , quindi espandere il `NSExtension` e `NSExtensionAttributes` chiavi nell'Editor:

[![](implementing-sirikit-images/intents04.png "The NSExtension and NSExtensionAttributes keys in the editor")](implementing-sirikit-images/intents04.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

Per configurare il file di `Info.plist` dell'estensione dell'interfaccia utente Intent, fare doppio clic su di esso nella **Esplora soluzioni** per aprirlo per la modifica. Espandere le chiavi `NSExtension` e `NSExtensionAttributes` nell'Editor:

[![](implementing-sirikit-images/intents04w.png "Tthe NSExtension and NSExtensionAttributes keys in the editor")](implementing-sirikit-images/intents04w.png#lightbox)

-----

Espandere la chiave di `IntentsSupported` e aggiungere il nome di una classe di finalità che verrà supportata da questa estensione. Per l'app MonkeyChat di esempio, supporta il `INSendMessageIntent`:

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

[![](implementing-sirikit-images/intents15.png "The INSendMessageIntent key")](implementing-sirikit-images/intents15.png#lightbox)

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

[![](implementing-sirikit-images/intents15w.png "The INSendMessageIntent key")](implementing-sirikit-images/intents15w.png#lightbox)

-----

Per un elenco completo dei domini per finalità disponibili, vedere le informazioni di [riferimento sui domini Intent](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)di Apple.

### <a name="configuring-the-main-class"></a>Configurazione della classe principale

Configurare la classe principale che funge da punto di ingresso principale per l'estensione dell'interfaccia utente Intent in Siri. Deve essere una sottoclasse di `UIViewController` conforme all'interfaccia `IINUIHostedViewController`. Esempio:

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

Siri passerà un'istanza della classe `INInteraction` al metodo `Configure` dell'istanza di `UIViewController` all'interno dell'estensione dell'interfaccia utente per finalità.

L'oggetto `INInteraction` fornisce tre elementi chiave di informazioni all'estensione:

1. Oggetto preventivo in fase di elaborazione.
2. Oggetto della risposta Intent dalla `Confirm` e `Handle` metodi dell'estensione per finalità.
3. Stato di interazione che definisce lo stato dell'interazione tra l'app e Siri.

L'istanza `UIViewController` è la classe principale per l'interazione con Siri e, poiché eredita da `UIViewController`, ha accesso a tutte le funzionalità di UIKit.

Quando Siri chiama il metodo `Configure` della `UIViewController` passa in un contesto di visualizzazione che informa che il controller di visualizzazione verrà ospitato in una scheda Siri snippit o maps.

Siri passerà anche un gestore di completamento che l'app deve restituire la dimensione desiderata della visualizzazione al termine della configurazione dell'app.

### <a name="design-the-ui-in-ios-designer"></a>Progettare l'interfaccia utente in iOS designer

Layout dell'interfaccia utente dell'estensione per interfaccia utente Intent in iOS designer. Fare doppio clic sul file di `MainInterface.storyboard` dell'estensione nel **Esplora soluzioni** per aprirlo per la modifica. Trascinare tutti gli elementi dell'interfaccia utente necessari per compilare l'interfaccia utente e salvare le modifiche.

> [!IMPORTANT]
> Sebbene sia possibile aggiungere elementi interattivi, ad esempio `UIButtons` o `UITextFields` al `UIViewController`dell'estensione dell'interfaccia utente Intent, questi sono strettamente proibiti come l'interfaccia utente Intent in non interattivo e l'utente non sarà in grado di interagire con essi.

### <a name="wire-up-the-user-interface"></a>Collegare l'interfaccia utente

Con l'interfaccia utente dell'estensione per interfaccia utente creata in iOS designer, modificare la sottoclasse `UIViewController` ed eseguire l'override del metodo `Configure` come indicato di seguito:

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

### <a name="overriding-the-default-siri-ui"></a>Override dell'interfaccia utente Siri predefinita

L'estensione dell'interfaccia utente Intent verrà sempre visualizzata insieme ad altri contenuti Siri, ad esempio l'icona e il nome dell'app nella parte superiore dell'interfaccia utente o, in base allo scopo, i pulsanti (ad esempio Send o Cancel) potrebbero essere visualizzati in basso.

Esistono alcuni casi in cui l'app può sostituire le informazioni che Siri Visualizza all'utente per impostazione predefinita, ad esempio la messaggistica o le mappe in cui l'app può sostituire l'esperienza predefinita con una personalizzata per l'app.

Se l'estensione dell'interfaccia utente Intent deve eseguire l'override degli elementi dell'interfaccia utente Siri predefinita, la sottoclasse `UIViewController` dovrà implementare l'interfaccia `IINUIHostedViewSiriProviding` e acconsentire esplicitamente alla visualizzazione di un particolare elemento di interfaccia.

Aggiungere il codice seguente alla sottoclasse `UIViewController` per indicare a Siri che l'estensione dell'interfaccia utente Intent sta già visualizzando il contenuto del messaggio:

```csharp
public bool DisplaysMessage {
    get {return true;}
}
```

### <a name="considerations"></a>Considerazioni

Apple suggerisce che lo sviluppatore prende in considerazione le considerazioni seguenti quando si progettano e implementano le estensioni dell'interfaccia utente per finalità:

- **Tenere presente l'utilizzo della memoria** : poiché le estensioni dell'interfaccia utente per finalità sono temporanee e vengono visualizzate solo per un breve periodo di tempo, il sistema impone vincoli di memoria più rigidi rispetto a quelli usati con un'applicazione completa.
- **Prendere in considerazione le dimensioni di visualizzazione minime e massime** : assicurarsi che le estensioni dell'interfaccia utente siano valide per ogni tipo di dispositivo iOS, dimensioni e orientamento. Inoltre, potrebbe non essere possibile concedere le dimensioni desiderate restituite dall'app a Siri.
- **Usare i modelli di layout flessibili e adattivi** per assicurarsi che l'interfaccia utente abbia un aspetto eccezionale in ogni dispositivo.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato SiriKit e come può essere aggiunto alle app Xamarin.iOS per fornire servizi accessibili all'utente tramite Siri e l'app Maps in un dispositivo iOS.

## <a name="related-links"></a>Collegamenti correlati

- [Esempio ElizaChat](https://docs.microsoft.com/samples/xamarin/ios-samples/ios10-elizachat)
- [Guida alla programmazione di SiriKit](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/index.html)
- [Informazioni di riferimento sul Framework Intent](https://developer.apple.com/reference/intents)
- [Informazioni di riferimento sul Framework dell'interfaccia utente Intent](https://developer.apple.com/reference/intentsui)
- [Informazioni di riferimento sui domini Intent](https://developer.apple.com/library/prerelease/content/documentation/Intents/Conceptual/SiriIntegrationGuide/SiriDomains.html#//apple_ref/doc/uid/TP40016875-CH9-SW2)
