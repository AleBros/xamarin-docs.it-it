---
title: Pubblicazione in Google Play
ms.prod: xamarin
ms.assetid: FB1CC234-3554-8566-48BD-2B9B3A28CC7F
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 04e83fc68218216fe36cce67e43b83e8ad8feaa5
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "80070706"
---
# <a name="publishing-to-google-play"></a>Pubblicazione in Google Play

Esistono vari mercati di app per la distribuzione di applicazioni, ma Google Play è probabilmente il più grande e il più visitato del mondo per le app per Android. Google Play offre una piattaforma unica per la distribuzione, la pubblicità, la vendita e l'analisi delle vendite di un'app per Android.

Questa sezione illustra argomenti specifici per Google Play, ad esempio la registrazione per diventare un editore, la raccolta di risorse che aiutano Google Play a promuovere e pubblicizzare l'applicazione, le linee guida per la valutazione dell'applicazione in Google Play e l'uso di filtri per limitare la distribuzione di un'applicazione a dispositivi specifici.

## <a name="requirements"></a>Requisiti

Per distribuire un'applicazione mediante Google Play è necessario creare un account sviluppatore. Questa operazione deve essere eseguita una sola volta e comporta una tariffa unica di 25 USD.

Tutte le applicazioni devono essere firmate con una chiave di crittografia che scade dopo il 22 ottobre 2033.

Un APK pubblicato in Google Play può avere dimensioni massime di 100 MB. Se un'applicazione supera tali dimensioni, Google Play consente la pubblicazione di risorse aggiuntive tramite i *file di espansione APK*. I file di espansione Android consentono due file aggiuntivi per l'APK, ognuno con dimensioni massime pari a 2 GB. Google Play archivia e distribuisce questi file gratuitamente. I file di espansione vengono descritti in un'altra sezione.

Google Play non è disponibile a livello globale. È possibile che la distribuzione di applicazioni non sia supportata in determinate località.

## <a name="becoming-a-publisher"></a>Diventare un editore

Per pubblicare applicazioni in Google Play è necessario avere un account editore. Per creare un account editore seguire questa procedura:

1. Visitare [Google Play Developer Console](https://play.google.com/apps/publish).
1. Immettere le informazioni di base dell'identità di sviluppatore.
1. Leggere e accettare il Contratto di distribuzione per sviluppatori basato sulle impostazioni locali.
1. Pagare la tariffa di registrazione di 25 USD.
1. Confermare la verifica tramite posta elettronica.
1. Dopo aver creato l'account è possibile pubblicare applicazioni con Google Play.

Google Play non supporta tutti i paesi del mondo. Gli elenchi più aggiornati dei paesi sono disponibili nei seguenti link:

1. [Posizioni supportate &amp; per la registrazione](https://support.google.com/googleplay/android-developer/bin/answer.py?hl=en&amp;answer=150324) &ndash; dei commercianti di sviluppatori Questo è un elenco di tutti i paesi in cui gli sviluppatori possono registrarsi come commercianti e vendere applicazioni a pagamento.

1. [](https://support.google.com/googleplay/android-developer/bin/answer.py?hl=en&amp;answer=138294)Paesi in cui è supportata la distribuzione per gli utenti Google Play&ndash;: questo elenco include tutti i paesi nei quali è possibile distribuire le applicazioni.

### <a name="preparing-promotional-assets"></a>Preparazione di asset promozionali

Per una promozione efficace dell'applicazione in Google Play, Google consente agli sviluppatori di includere asset promozionali come screenshot, elementi grafici e video. Google Play usa queste risorse per pubblicizzare e promuovere l'applicazione.

#### <a name="launcher-icons"></a>Icone launcher

Un'*icona launcher* è un elemento grafico che rappresenta un'applicazione. Ogni icona launcher deve essere un file con estensione png a 32 bit dotato di un canale alfa per la trasparenza. Un'applicazione deve avere icone launcher per tutte le densità schermo generiche, come descritto nell'elenco seguente:

- **ldpi** (120 dpi) &ndash; 36 x 36 pixel
- **mdpi** (160 dpi) &ndash; 48 x 48 pixel
- **hdpi** (240 dpi) &ndash; 72 x 72 pixel
- **xhdpi** (320 dpi) &ndash; 96 x 96 pixel

Le icone launcher sono il primo elemento dell'applicazione visualizzato dall'utente in Google Play, pertanto devono essere significative e visivamente accattivanti.

Suggerimenti per le icone launcher:

1. **Le** &ndash; icone di avvio semplici e ordinate devono essere mantenute semplici e ordinate. Evitare di includere il nome dell'applicazione nell'icona. Le icone più semplici sono più facili da ricordare e anche più facili da distinguere in dimensioni ridotte.

1. **Le icone non dovrebbero essere sottili** &ndash; Icone troppo sottili non si distinguono bene su tutti gli sfondi.

1. **Utilizzare il canale** &ndash; alfa Le icone devono utilizzare il canale alfa e non devono essere immagini a cornice intera.

#### <a name="high-resolution-application-icons"></a>Icone dell'applicazione ad alta risoluzione

Per le applicazioni in Google Play deve essere disponibile una versione ad alta risoluzione dell'icona dell'applicazione. Questa versione viene usata solo da Google Play e non sostituisce l'icona launcher dell'applicazione. Le specifiche per l'icona ad alta risoluzione sono:

1. File con estensione png a 32 bit con un canale alfa
1. 512 x 512 pixel
1. Dimensioni massime: 1024 KB

[Android Asset Studio](https://romannurik.github.io/AndroidAssetStudio/) è uno strumento utile per creare icone launcher e l'icona dell'applicazione ad alta risoluzione.

#### <a name="screenshots"></a>Screenshots (Schermate)

Google Play richiede un minimo di due e un massimo di otto screenshot per un'applicazione. Verranno visualizzati nella pagina dei dettagli di un'applicazione in Google Play.

Le specifiche per gli screenshot sono:

1. File con estensione png o jpg a 24 bit senza canale alfa
1. Larghezza x altezza: 320 x 480 o 480 x 800 o 480 x 854. Le immagini con orientamento orizzontale vengono ritagliate.

#### <a name="promotional-graphic"></a>Immagine promozionale

Questa immagine facoltativa viene usata da Google Play:

1. È un file con estensione png o jpg a 24 bit con larghezza 180 e altezza 120, senza canale alfa.
1. Nessun bordo.

#### <a name="feature-graphic"></a>Immagine in primo piano

Usata nella sezione App in primo piano di Google Play. Questa immagine può essere visualizzata da sola, senza l'icona dell'applicazione.

1. File con estensione png o jpg con larghezza 1024 e altezza 500, senza canale alfa e senza trasparenza.
1. Tutto il contenuto importante va incluso in un frame da 924 x 500. I pixel esterni a questo frame potrebbero essere ritagliati per esigenze grafiche.
1. Questa immagine potrebbe essere ridotta in scala: usare testo grande e grafica semplice.

#### <a name="video-link"></a>Collegamento video

URL di riferimento a un video di YouTube che presenta l'applicazione. Il video può durare da 30 secondi a 2 minuti e visualizza le funzionalità più importanti dell'applicazione.

### <a name="publishing-to-google-play"></a>Pubblicazione in Google Play

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Xamarin Android 7.0 introduce un flusso di lavoro integrato per la pubblicazione di app in Google Play da Visual Studio. Se si sta usando una versione di Xamarin Android precedente alla 7.0 è necessario caricare manualmente il pacchetto APK tramite Google Play Developer Console. Prima di usare il flusso di lavoro integrato è anche necessario avere almeno un pacchetto APK già caricato. Se non è ancora stato caricato il primo pacchetto APK, è necessario caricarlo manualmente. Per altre informazioni, vedere [Manually Uploading the APK](~/android/deploy-test/publishing/publishing-to-google-play/manually-uploading-the-apk.md) (Caricare manualmente l'APK).

L'argomento [Creating a New Certificate](~/android/deploy-test/signing/index.md#newcert) (Creazione di un nuovo certificato) illustra come creare un nuovo certificato per firmare le app Android. Il passaggio successivo è la pubblicazione dell'app firmata in Google Play:

1. Accedere al proprio account per sviluppatore di Google Play per creare un nuovo progetto collegato all'account.
2. Creare un **client OAuth** che autentica l'app.
3. Immettere l'ID client e il Segreto client risultanti in Visual Studio.
4. Registrare l'account con Visual Studio.
5. Firmare l'app con il proprio certificato.
6. Pubblicare l'app firmata in Google Play.

In [Archivia per la pubblicazione](~/android/deploy-test/release-prep/index.md#archive), la finestra di dialogo **Canale di distribuzione** offre due opzioni di distribuzione: **Ad hoc** e **Google Play**. Se invece viene visualizzata la finestra di dialogo **Identità di firma**, fare clic su **Indietro** per tornare alla finestra di dialogo **Canale di distribuzione**. Seleziona **Google Play**:

[![Finestra di dialogo Canale di distribuzione](images/vs/01-distribution-channel-sml.png)](images/vs/01-distribution-channel.png#lightbox)

Nella finestra di dialogo **Identità di firma** selezionare l'identità creata in [Creating a New Certificate](~/android/deploy-test/signing/index.md#newcert) (Creazione di un nuovo certificato) e fare clic su **Continua**:

[![Finestra di dialogo Identità firma](images/vs/02-select-identity-sml.png)](images/vs/02-select-identity.png#lightbox)

Nella finestra di dialogo **Account Google Play** fare clic sul pulsante **+** per aggiungere un nuovo account Google Play:

[![Finestra di dialogo Account Google Play](images/vs/03-google-play-accounts-sml.png)](images/vs/03-google-play-accounts.png#lightbox)

Nella finestra di dialogo **Registra accesso API a Google** indicare l'_ID client_ e il _Segreto client_ che consentono all'API l'accesso all'account per sviluppatore di Google Play:

[![Finestra di dialogo Registra accesso API Google](images/vs/04-register-google-api-access-sml.png)](images/vs/04-register-google-api-access.png#lightbox)

La sezione successiva spiega come creare un nuovo progetto API Google e come generare l'_ID client_ e il _Segreto client_ necessari.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

Visual Studio per Mac dispone di un flusso di lavoro integrato per la pubblicazione di app in Google Play.

L'argomento [Creare un nuovo certificato](~/android/deploy-test/signing/index.md#newcert) illustra come creare un nuovo certificato per firmare le app Android. La procedura seguente descrive come pubblicare un'applicazione Xamarin.Android in Google Play:

1. Accedere al proprio account per sviluppatore di Google Play per creare un nuovo progetto collegato all'account.
2. Creare un _client OAuth_ che autentica l'app.
3. Immettere l'_ID client_ e il _Segreto client_ risultanti in Visual Studio per Mac.
4. Registrare l'account con Visual Studio per Mac.
5. Firmare l'applicazione con il proprio certificato.
6. Pubblicare l'applicazione firmata in Google Play.

In [Archivia per la pubblicazione](~/android/deploy-test/release-prep/index.md#archive), la finestra di dialogo **Firma e distribuisci** offre due opzioni di distribuzione. Selezionare **Google Play** e fare clic su **Avanti**:

[![Selezionare la finestra di dialogo Di distribuzione Android](images/xs/01-select-google-play-sml.png)](images/xs/01-select-google-play.png#lightbox)

Nella finestra di dialogo **Account dell'API di Google Play** indicare l'_ID client_ e il _Segreto client_ che consentono all'API l'accesso all'account per sviluppatore di Google Play:

[![Finestra di dialogo Account API di Google Play](images/xs/02-google-play-api-account-sml.png)](images/xs/02-google-play-api-account.png#lightbox)

La sezione successiva spiega come creare un nuovo progetto API Google e come generare l'_ID client_ e il _Segreto client_ necessari.

-----

#### <a name="create-a-google-api-project"></a>Creare un progetto di API di Google

In primo luogo accedere all'[account per sviluppatore di Google Play](https://play.google.com/apps/publish).
Se non si dispone di un account per sviluppatore di Google Play, vedere [Get Started with Publishing](https://developer.android.com/distribute/googleplay/start.html) (Introduzione alla pubblicazione).
Anche la [Guida introduttiva](https://developers.google.com/android-publisher/getting_started) dell'API descrive come usare l'API Google Play Developer. Dopo aver effettuato l'accesso a Google Play Developer Console, fai clic su **CREA APPLICATION:**

[![Pulsante Crea nuovo progetto](images/01-create-new-project-sml.png)](images/01-create-new-project.png#lightbox)

Dopo aver creato il nuovo progetto, verrà collegato al tuo account Google Play Developer Console.

Il passaggio successivo è la creazione di un client OAuth per l'applicazione (se non è già stato creato). Quando gli utenti richiedono l'accesso ai dati privati usando l'app, l'autenticazione dell'app viene eseguita mediante l'ID Client OAuth.

Passare alla pagina **Impostazioni**.

[![Icona Impostazioni](images/02-google-play-developer-console-sml.png)](images/02-google-play-developer-console.png#lightbox)

Nella pagina **Impostazioni** selezionare **Accesso API** e fare clic su **CREATE OAUTH CLIENT** per creare un nuovo client OAuth:

[![Pulsante Crea client OAuth](images/03-create-oauth-client-sml.png)](images/03-create-oauth-client.png#lightbox)

Dopo alcuni secondi viene generato un nuovo ID client. Fare clic su **View in Google Developers Console** (Visualizza in Google Play Developer Console) per visualizzare il nuovo ID client in Google Play Developer Console:

[![ID client visualizzato](images/04-generated-client-id-sml.png)](images/04-generated-client-id.png#lightbox)

L'ID client viene visualizzato insieme al nome e alla data di creazione. Fare clic sull'icona **Edit OAuth Client** (Modifica client OAuth) per visualizzare il Segreto client per l'app:

[![Visualizzare le credenziali dell'app](images/05-google-developer-console-sml.png)](images/05-google-developer-console.png#lightbox)

Il nome predefinito del client OAuth è *Google Play Android Developer*. Questo nome può essere convertito nel nome dell'app Xamarin.Android o in qualsiasi nome appropriato. In questo esempio il nome del client OAuth viene convertito nel nome dell'app, ovvero **MyApp**:

[![ID client e segreto visualizzati](images/06-client-id-and-secret-sml.png)](images/06-client-id-and-secret.png#lightbox)

Fare clic su **Salva** per salvare le modifiche. Viene riportata la pagina Credenziali in cui scaricare le credenziali facendo clic sull'icona **Scarica JSON:This** returns to the **Credentials** page where to download the credentials by clicking on the Download JSON icon:

[![Scarica l'icona JSON](images/07-download-json-sml.png)](images/07-download-json.png#lightbox)

Il file JSON contiene l'ID client e il Segreto client che possono essere incollati nella finestra di dialogo **Firma e distribuisci** nel passaggio successivo.

#### <a name="register-google-api-access"></a>Registrare l'accesso API a Google

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

Usare l'ID Client e il Segreto client per completare la finestra di dialogo **Account dell'API di Google Play** in Visual Studio per Mac. È possibile assegnare una descrizione all'account. In questo modo è possibile registrare più account Google Play e in futuro caricare APK per diversi account Google Play. Copiare l'ID Client e il Segreto Client in questa finestra di dialogo e fare clic su **Registra**:

[![Finestra di dialogo Registra accesso API Google](images/vs/05-enter-client-id-and-secret-sml.png)](images/vs/05-enter-client-id-and-secret.png#lightbox)

Viene aperto un Web browser che richiede di effettuare l'accesso all'account Google Play Android Developer (se non è già stato effettuato). Dopo l'accesso, nel Web browser viene visualizzata la richiesta seguente.
Fare clic su **Allow** (Consenti) per autorizzare l'app:

[![Autorizza la finestra di dialogo dell'app](images/vs/06-authorize-app-sml.png)](images/vs/06-authorize-app.png#lightbox)

#### <a name="publish"></a>Pubblica

Dopo aver fatto clic su **Consenti**, il browser segnala _Il codice di verifica ricevuto. Chiusura..._ e l'app viene aggiunta all'elenco degli account Google Play in Visual Studio. Nella finestra di dialogo **Account Google Play** fare clic su **Continua**:

[![Account aggiunto agli account Google Play](images/vs/07-account-added-sml.png)](images/vs/07-account-added.png#lightbox)

Viene visualizzata la finestra di dialogo **Versione di Google Play**. Google Play offre cinque possibili tracce per il caricamento della tua app:

- **Interno** &ndash; Utilizzato per distribuire rapidamente l'app per i test interni e i controlli di controllo qualità.
- **Alpha** &ndash; Utilizzato per caricare una versione precedente dell'app in un piccolo elenco di tester.
- **Beta** &ndash; Utilizzato per il caricamento di una versione precedente dell'app in un elenco più ampio di tester.
- **Produzione** &ndash; Utilizzata per la distribuzione completa al Google Play Store.
- **Personalizzato** &ndash; Utilizzato per testare le versioni non definitive dell'app con utenti specifici creando un elenco di tester in base all'indirizzo di posta elettronica.

Scegliere la versione Google Play da usare per il caricamento dell'app e fare clic su **Carica**.

[![Account aggiunto agli account Google Play](images/vs/08-google-play-track-sml.png)](images/vs/07-account-added.png#lightbox)

Per ulteriori informazioni sui test di Google Play, consultate [Configurare test aperti/chiusi/interni.](https://support.google.com/googleplay/android-developer/answer/3131213?hl=en)

Viene quindi visualizzata una finestra di dialogo che richiede la password per il certificato di firma.
Immettere la password e fare clic su **OK**:

[![Finestra di dialogo Password di firma](images/vs/09-certificate-password-sml.png)](images/vs/09-certificate-password.png#lightbox)

**Gestione archivi** visualizza lo stato del caricamento:

[![Avanzamento del caricamento dell'APK](images/vs/10-uploading-apk-sml.png)](images/vs/10-uploading-apk.png#lightbox)

Al termine del caricamento, un messaggio nell'angolo inferiore sinistro della finestra di Visual Studio indica il completamento dell'operazione:

[![Pubblicazione del messaggio di pubblicazione del progetto completata](images/vs/11-published-sml.png)](images/vs/11-published.png#lightbox)

### <a name="troubleshooting"></a>Risoluzione dei problemi

Se non vedi la traccia personalizzata quando selezioni una traccia Google Play, assicurati di aver creato una versione per quella traccia sulla Console per sviluppatori Google Play. Per istruzioni su come creare una versione, vedere [Preparare & l'implementazione delle versioni.](https://support.google.com/googleplay/android-developer/answer/7159011?hl=en)

Si noti che per il funzionamento corretto di **Pubblica in Google Play** è necessario che almeno un pacchetto APK sia già stato inoltrato a Google Play Store. Se in precedenza non è stato caricato nessun pacchetto APK, la procedura di pubblicazione guidata visualizza il seguente messaggio di errore nel riquadro **Errori**:

[![È necessario caricare manualmente il primo APK per questa applicazione](images/vs/12-upload-error-sml.png)](images/vs/12-upload-error.png#lightbox)

Quando si verifica questo errore, carica manualmente un APK (ad esempio una build Ad Hoc) tramite Google Play Developer Console e utilizza la finestra di dialogo Canale di **distribuzione** per i successivi aggiornamenti APK.  Per altre informazioni, vedere [Manually Uploading the APK](~/android/deploy-test/publishing/publishing-to-google-play/manually-uploading-the-apk.md) (Caricare manualmente l'APK). Il codice versione dell'APK deve cambiare ad ogni caricamento, altrimenti si verifica l'errore seguente:

[![APK con codice di versione (1) è già stato aggiornato](images/vs/13-version-code-error-sml.png)](images/vs/13-version-code-error.png#lightbox)

Per risolvere questo errore, ricompilare l'app con un numero di versione diverso e inoltrarla di nuovo a Google Play mediante la finestra di dialogo **Canale di distribuzione**.

# <a name="visual-studio-for-mac"></a>[Visual Studio per Mac](#tab/macos)

Usare l'ID Client e il Segreto client per completare la finestra di dialogo **Account dell'API di Google Play** in Visual Studio per Mac. È possibile assegnare una descrizione all'account. In questo modo è possibile registrare più account Google Play e in futuro caricare APK per diversi account Google Play. Copiare l'ID Client e il Segreto Client in questa finestra di dialogo e fare clic su **Registra**:

[![Finestra di dialogo Autorizza accesso](images/xs/03-register-sml.png)](images/xs/03-register.png#lightbox)

Se l'ID client e il Segreto client vengono accettati viene visualizzato il messaggio **Registrazione completata**. Fare clic su **Avanti**:

[![Registrazione riuscita](images/xs/04-registration-successful-sml.png)](images/xs/04-registration-successful.png#lightbox)

Nella finestra di dialogo **Account Google Play** selezionare un account Google e una versione per il caricamento dell'applicazione:

[![Scegliere la finestra di dialogo dell'account Google](images/xs/05-choose-google-account-sml.png)](images/xs/05-choose-google-account.png#lightbox)

Google Play offre cinque possibili tracce per il caricamento della tua app:

- **Interno** &ndash; Utilizzato per distribuire rapidamente l'app per i test interni e i controlli di controllo qualità.
- **Alpha** &ndash; Utilizzato per caricare una versione precedente dell'app in un piccolo elenco di tester.
- **Beta** &ndash; Utilizzato per il caricamento di una versione precedente dell'app in un elenco più ampio di tester.
- **Produzione** &ndash; Utilizzata per la distribuzione completa al Google Play Store.
- **Personalizzato** &ndash; Utilizzato per testare le versioni non definitive dell'app con utenti specifici creando un elenco di tester in base all'indirizzo di posta elettronica.

Per ulteriori informazioni sui test di Google Play, consultate Configurare i [test alfa/beta.](https://support.google.com/googleplay/android-developer/answer/3131213?hl=en)

Scegliere quindi un'identità di firma che verrà usata per la firma dell'app.
Selezionare **Usa chiave esistente** per usare un'identità di firma esistente. In alternativa, per informazioni sulla creazione di una nuova chiave vedere [Creating a New Certificate](~/android/deploy-test/signing/index.md#newcert) (Creazione di un nuovo certificato). Dopo aver selezionato un certificato per firmare l'applicazione, fare clic su **Avanti**:

[![Finestra di dialogo identità di firma Android](images/xs/06-android-signing-identity-sml.png)](images/xs/06-android-signing-identity.png#lightbox)

A questo punto è possibile caricare l'app in Google Play. La finestra di dialogo Pubblica su &ndash; Google **Play** riepiloga le informazioni sulla tua app, fai clic su **Pubblica** per pubblicare la tua app su Google Play:

[![Finestra di dialogo Pubblica su Google Play](images/xs/07-publish-to-google-play-sml.png)](images/xs/07-publish-to-google-play.png#lightbox)

### <a name="troubleshooting"></a>Risoluzione dei problemi

Se non vedi la traccia personalizzata quando selezioni una traccia Google Play in cui caricare la tua app, assicurati di aver creato una versione per quella traccia su Google Play Developer Console. Per istruzioni su come creare una versione, vedere [Preparare & l'implementazione delle versioni.](https://support.google.com/googleplay/android-developer/answer/7159011?hl=en)

Si noti che per il funzionamento corretto di **Pubblica in Google Play** è necessario che almeno un pacchetto APK sia già stato inoltrato a Google Play Store. Se non è stato caricato un APK può verificarsi l'errore seguente:

> _Google Play richiede di caricare manualmente il primo APK per questa app. È possibile utilizzare un APK ad hoc per questo._

o

> _No application was found for the given package name. [404]_ (Nessuna applicazione trovata con il nome pacchetto dato. [404])

Per risolvere questo errore, carica manualmente un APK (ad esempio una build ad hoc) tramite La Console per sviluppatori di Google Play e utilizza la finestra di dialogo **Pubblica su Google Play** per i successivi aggiornamenti APK. Per informazioni su come caricare manualmente un APK, vedere [Manually Uploading the APK](~/android/deploy-test/publishing/publishing-to-google-play/manually-uploading-the-apk.md) (Caricare manualmente l'APK).

-----
