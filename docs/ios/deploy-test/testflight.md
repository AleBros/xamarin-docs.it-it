---
title: Uso di TestFlight per distribuire app Xamarin.iOS
description: TestFlight ora è di proprietà di Apple ed è lo strumento principale per testare la versione beta delle app Xamarin.iOS. Questo articolo descrive in dettaglio tutti i passaggi del processo TestFlight, dal caricamento dell'app all'uso di iTunes Connect.
ms.prod: xamarin
ms.assetid: BA880768-2BC8-41E4-B57E-A56F8EED4690
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 2f5d04b49658abe9d366e264d8f1a659732a2c33
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028480"
---
# <a name="using-testflight-to-distribute-xamarinios-apps"></a>Uso di TestFlight per distribuire app Xamarin.iOS

_TestFlight ora è di proprietà di Apple ed è lo strumento principale per testare la versione beta delle app Novell. iOS. In questo articolo vengono illustrati tutti i passaggi del processo TestFlight, dal caricamento dell'app all'uso di iTunes Connect._

Il test della versione beta è parte integrante del ciclo di sviluppo software. Esistono numerose applicazioni multipiattaforma che è possibile usare per semplificare questo processo, ad esempio [HockeyApp](https://hockeyapp.net/features/), [Applause](https://www.applause.com/mobile-app-testing) e ovviamente il test della versione beta dell'app nativa di Google Play per le app Android. Questo documento descrive lo strumento TestFlight di Apple.

TestFlight è il servizio offerto da Apple per testare la versione beta di app iOS ed è accessibile solo tramite [iTunes Connect](https://itunesconnect.apple.com/). È attualmente disponibile per le app iOS 8.0 e versioni successive. TestFlight consente di testare la versione beta con utenti interni ed esterni e, potendo questi ultimi rivedere poi la versione beta dell'app, semplifica considerevolmente il processo di revisione finale al momento della pubblicazione nell'App Store.

In passato, il file binario veniva generato in Visual Studio per Mac e caricato nel sito Web di TestFlightApp per la distribuzione ai tester. Al nuovo processo sono stati aggiunti numerosi miglioramenti che consentono di avere nell'App Store app ben testate e di qualità elevata. Esempio:

- La revisione della versione beta dell'app richiesta per il test esterno aumenta le probabilità di accettazione della revisione finale nell'App Store, in quanto in entrambi i casi è necessaria la conformità alle linee guida di Apple.
- Prima di caricare l'app, è necessario registrarla in iTunes Connect. In questo modo si garantisce che non vi siano discordanze tra profili di provisioning, nomi e certificati.
- TestFlight è ora un'app a tutti gli effetti e come tale risulta più veloce.
- Dopo aver completato il test della versione beta, il processo per sottoporre l'app alla revisione è rapido ed efficiente. È sufficiente premere un pulsante.

## <a name="requirements"></a>Requisiti

Con TestFlight è possibile testare solo le app iOS 8.0 o versioni successive.

Tutti i tester devono testare l'app almeno in un dispositivo iOS 8. È tuttavia buona norma testare l'app in tutte le versioni iOS

## <a name="provisioning"></a>Provisioning

Per testare le compilazioni con TestFlight, è necessario creare un *profilo di distribuzione dell'App Store*  con il nuovo entitlement beta. Questo entitlement consente di testare la versione beta tramite TestFlight ed è contenuto nel **nuovo** profilo di distribuzione dell'App Store. È possibile seguire le istruzioni dettagliate della guida [Creating a Distribution Profile](~/ios/get-started/installation/device-provisioning/manual-provisioning.md#provisioningprofile) (Creazione di un profilo di distribuzione) per generare un nuovo profilo.

È possibile confermare che nel profilo di distribuzione è contenuto l'entitlement beta durante la [convalida della compilazione di Xcode](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md), come illustrato di seguito:

[![](testflight-images/validate-build.png "Submitting the App to Apple")](testflight-images/validate-build.png#lightbox)

## <a name="testflight-workflow"></a>Flusso di lavoro di TestFlight

Il flusso di lavoro seguente illustra i passaggi necessari per iniziare a usare TestFlight e testare la versione beta dell'app:

1. Per le nuove app, creare un [record iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md).
2. [Archiviare e pubblicare](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) l'applicazione in iTunes Connect.
3. Gestire il test della versione beta:
    - Aggiungere i metadati.
    - Aggiungere gli utenti interni:
      - Max 25 utenti.
    - Aggiungere gli utenti esterni:
      - Max 1000 utenti.
      - È richiesta una revisione del test della versione beta per verificare la conformità alle linee guida di Apple.
4. Ricevere commenti e suggerimenti dagli utenti, intervenire e tornare al passaggio 2.

## <a name="create-an-itunes-connect-record"></a>Creare un record iTunes Connect

1. Accedere al [portale di iTunes Connect](https://itunesconnect.apple.com/) usando le credenziali per sviluppatore Apple.
2. Selezionare **My Apps** (App personali):

    [![](testflight-images/my-apps.png "Select My Apps")](testflight-images/my-apps.png#lightbox)

3. Nella schermata **My Apps** (App personali) fare clic sul pulsante **+** nell'angolo superiore sinistro della schermata per aggiungere una nuova app. Se gli account per sviluppatore sono Mac e iOS, viene chiesto di scegliere il nuovo tipo di app.

Si aprirà la finestra di invio **New iOS App** (Nuova app iOS) in cui devono essere contenute esattamente le stesse informazioni specificate nel file Info.plist dell'app

Per altre informazioni sulla creazione di un nuovo record iTunes Connect, vedere la guida [Creating an iTunes Connect Record](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md) (Creazione di un record iTunes Connect).

### <a name="completing-the-new-ios-app-submission-form"></a>Completamento del modulo d'invio di una nuova app iOS

Il modulo deve contenere esattamente le informazioni specificate nel file Info.plist dell'app, come illustrato di seguito:

[![](testflight-images/infoplist.png "The app's Info.plist")](testflight-images/infoplist.png#lightbox)
[![](testflight-images/newiosapp.png "The form on iTunes Connect")](testflight-images/newiosapp.png#lightbox)

- **Name** (Nome): il nome descrittivo usato per la configurazione del bundle dell'app. Deve corrispondere esattamente al nome specificato in **Nome applicazione** specificato nel file `Info.plist`.
- **Primary Language** (Lingua principale): la lingua di base dell'app. Di norma è la lingua parlata.
- **Bundle ID** (ID bundle):un elenco sotto forma di menu a discesa di tutti gli ID app creati nell'account per sviluppatore.
  - **Bundle ID Suffix** (Suffisso ID bundle): se è stato selezionato un ID bundle con carattere jolly (ad esempio un ID che termina con *, come nell'esempio illustrato sopra), viene visualizzata una finestra aggiuntiva, in cui è necessario immettere il suffisso dell'ID Bundle. In questo esempio l'**ID bundle** e `mobi.chkn.*`, mentre il suffisso è **PageView**. Insieme costituiscono l'**ID bundle** nel file `Info.plist`.
- **Versione** (Versione): il numero della versione dell'app che viene caricata. È scelta dallo sviluppatore.
- **SKU** (SKU): il codice SKU è un ID univoco per l'app, che non è visualizzato dagli utenti. Può essere inteso come se fosse l'ID prodotto. Nell'esempio precedente sono stati scelta la data e un numero di versione per tale data.

## <a name="upload-your-app"></a>Caricare l'app

Dopo aver creato il record iTunes Connect, è possibile caricare le nuove compilazioni. Tenere presente che le compilazioni devono contenere il nuovo entitlement beta.

Prima compilare il [file distribuibile finale](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) nell'IDE, quindi [inviare l'app ad Apple](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) tramite Application Loader o la funzione di archiviazione in Xcode.

<!-- markdownlint-disable MD001 -->

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio per Mac](#tab/macos)

### <a name="create-an-archive"></a>Creare un archivio

 Per compilare un file binario in Visual Studio per Mac, è necessario usare la funzione _Archivia_. Fare clic con il pulsante destro del mouse sul progetto e selezionare **Archivia per la pubblicazione** come illustrato di seguito:

 [![](testflight-images/new-archive.png "Select Archive for Publishing")](testflight-images/new-archive.png#lightbox)

 Fare riferimento alla guida [Building the Distributable](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) (Compilazione del file distribuibile).

### <a name="sign-and-distribute-your-app"></a>Firmare e distribuire l'app

 Dopo aver creato un archivio, si aprirà automaticamente la visualizzazione **Archivi**, in cui vengono visualizzati tutti i progetti archiviati, raggruppati per soluzione. Per firmare l'app e prepararla per la distribuzione, selezionare **Firma e distribuisci...** , come illustrato di seguito:

[![](testflight-images/archive-view.png "Creating an archive will automatically open the Archives View")](testflight-images/archive-view.png#lightbox)

 Si aprirà la pubblicazione guidata. Selezionare il canale di distribuzione **App Store** per creare un pacchetto e aprire Application Loader. Nella schermata Profilo di provisioning selezionare l'identità di firma e il profilo di provisioning o ripetere la firma con un'altra identità. Verificare i dettagli del pacchetto e fare clic su **Pubblica** per salvare il file `.ipa`

[![](testflight-images/group.png "Select your signing identity and provisioning profile, or re-sign with another identity")](testflight-images/group.png#lightbox)

 Fare riferimento alla sezione [Invio dell'app ad Apple](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) per altre informazioni su questi passaggi.

### <a name="submitting-your-build"></a>Invio della compilazione
 La pubblicazione guidata apre il programma Application Loader che consente di caricare la compilazione in iTunes Connect. Selezionare l'opzione **Deliver Your App** (Invia l'app) e caricare il file `.ipa` creato in precedenza. Application Loader convaliderà e caricherà la compilazione in iTunes Connect.

 Fare riferimento alla sezione [Invio dell'app ad Apple](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) per altre informazioni su questi passaggi.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

### <a name="building-your-final-distributable"></a>Compilazione del file distribuibile finale
 Il plug-in di Xamarin per Visual Studio non supporta l'archiviazione di app Xamarin.iOS per la pubblicazione nell'App Store. Esistono però due opzioni per pubblicare un'applicazione iOS da Visual Studio. Questi sono:

1. Caricare un pacchetto IPA creato con il comando di compilazione del pacchetto ad-hoc.
1. Caricare un bundle `.app` compresso.

 La guida [Building the Distributable](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) (Compilazione del file distribuibile) contiene istruzioni su entrambe le opzioni.

### <a name="submitting-your-build"></a>Invio della compilazione
 Per inviare l'app ad Apple, è necessario spostare l'host di compilazione e usare il programma Application Loader che viene installato come parte di Xcode. Per altre informazioni sull'accesso ad Application Loader, vedere la guida di Apple [Access Application Loader](https://help.apple.com/itc/apploader/#/apdATD1E927-D1E1A1303-D1E927A1126) (Accedere ad Application Loader).

Dopo aver aperto il programma, selezionare l'opzione **Deliver Your App** (Invia l'app) e caricare il file ZIP o il file `.ipa` creato in precedenza. Application Loader convaliderà e caricherà la compilazione in iTunes Connect.

 Fare riferimento alla sezione [Invio dell'app ad Apple](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) per altre informazioni su questi passaggi.

-----

La guida [Pubblicazione nell'App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md) descrive tutti i passaggi precedenti in modo più dettagliato. Fare riferimento a questa guida per approfondire il processo di invio nell'App Store.

Tornare alla sezione **My Apps** (App personali) e verificare che l'applicazione sia stata caricata. A questo punto è possibile eseguire il test della versione beta.

## <a name="manage-beta-testing"></a>Gestire il test della versione beta

### <a name="add-metadata"></a>Aggiungere i metadati

Per iniziare a usare TestFlight, selezionare la scheda **Prerelease** (Versione provvisoria) dell'app. Sono disponibili tre schede contenenti un elenco di compilazioni, tester interni ed esterni, come illustrato di seguito:

[![](testflight-images/app-uploaded.png "Builds, Internal Testers, and External Testers tabs")](testflight-images/app-uploaded.png#lightbox)

Per aggiungere i metadati all'app, fare clic sul numero di build e selezionare TestFlight:

[![](testflight-images/metadata.png "Add Metadata")](testflight-images/metadata.png#lightbox)

In **Test Information** (Informazioni test), i tester possono ottenere le informazioni più importanti che riguardano l'app, ad esempio:

- Cosa testare
- La descrizione dell'app.
- L'URL di marketing che offre informazioni sull'app che si sta aggiungendo.
- L'URL dell'informativa sulla privacy che offre informazioni relative all'informativa sulla privacy aziendale.
- L'indirizzo di posta elettronica a cui inviare commenti e suggerimenti.

Si noti che questi metadati **non** sono necessari per i tester interni, ma **sono** richiesti per i tester esterni.

<a name="beta-testing" />

### <a name="enable-beta-testing"></a>Abilitare il test della versione beta

Quando si è pronti ad avviare il test dell'app, abilitare il comando **TestFlight Beta Testing** (Test beta di TestFlight) per la versione:

[![](testflight-images/turn-on-testing.png "Turn on the TestFlight Beta Testing switch")](testflight-images/turn-on-testing.png#lightbox)

Ogni compilazione rimane attiva per **60 giorni** dalla data in cui è stata abilitata l'opzione di test della versione beta in TestFlight. Nella pagina **Test Information** (Informazioni test) è possibile visualizzare i giorni rimanenti per ogni compilazione:

[![](testflight-images/daysleft.png "The Test Information page")](testflight-images/daysleft.png#lightbox)

Il test può essere disabilitato in qualsiasi momento.

### <a name="internal-testers"></a>Tester interni

Sono tester interni i membri del team di sviluppo ai quali è assegnato uno dei ruoli seguenti in iTunes Connect:

- **Admin** (Amministratore): un amministratore è autorizzato ad aggiungere e gestire nuovi utenti in iTunes Connect.
- **Legal** (Legale): l'agente del team è l'unico utente amministratore a cui viene assegnato il ruolo Legal. che gli consente di firmare i contratti legali.
- **Technical** (Tecnico): un utente tecnico può modificare la maggior parte delle proprietà riguardanti un'app. Può ad esempio modificare le informazioni sull'app, caricare un file binario e inviare un'app per la revisione.

Ogni compilazione può essere condivisa da un massimo di 25 membri.

Per aggiungere i tester, passare a **Users and Roles** (Utenti e ruoli) nella schermata principale di iTunes Connect:

[![](testflight-images/users-and-roles.png "Users and Roles on the main iTunes Connect screen")](testflight-images/users-and-roles.png#lightbox)

Gli utenti di iTunes Connect esistenti vengono visualizzati nell'elenco. Per selezionarli, fare clic sul nome, abilitare l'opzione **Internal Tester** (Tester interno) e fare clic su **Save** (Salva):

[![](testflight-images/internal-tester.png "Turn on the Internal Tester switch")](testflight-images/internal-tester.png#lightbox)

Per aggiungere un utente che non è presente nell'elenco, selezionare il pulsante **+** accanto a *User* (Utenti) e specificare nome, cognome e indirizzo di posta elettronica per creare un account. L'utente dovrà confermare l'indirizzo di elettronica per attivare l'account:

[![](testflight-images/add-new-user.png "Adding a user")](testflight-images/add-new-user.png#lightbox)

Tornando a **My Apps > Prerelease > Internal Testers** (App personali > Versione provvisoria > Tester interni), è possibile visualizzare gli utenti aggiunti per eseguire il test interno della versione beta di TestFlight:

[![](testflight-images/select-users.png "A list of users that have been added for TestFlight Internal beta testing")](testflight-images/select-users.png#lightbox)

È possibile invitare i tester selezionando il nome e facendo clic sul pulsante **Invite** (Invita). I tester riceveranno un messaggio di posta elettronica contenente un invito al test dell'app.

È possibile visualizzare lo stato dell'invito nella colonna di stato della pagina Internal Testers (Tester interni):

[![](testflight-images/status-added.png "The invitation status")](testflight-images/status-added.png#lightbox)

### <a name="external-testers"></a>Tester esterni

Prima di invitare tester esterni al test della versione beta dell'app, è necessario che l'app sia sottoposta a una revisione dell'app beta e che pertanto sia conforme alle linee guida descritte in [App Store Review Guidelines](https://developer.apple.com/app-store/review/guidelines/) (Linee guida alla revisione nell'App Store).

Per inviare l'app per la revisione, fare clic sul testo **Submit For Beta App Review** (Invia per revisione app beta) accanto alla compilazione, come illustrato nell'immagine seguente:

[![](testflight-images/beta-app-review.png "Submit For Beta App Review")](testflight-images/beta-app-review.png#lightbox)

L'app supera la revisione se sono stati immessi tutti i metadati necessari nella pagina Beta Information (Informazioni beta) di TestFlight.

A questo punto è possibile preparare gli inviti e aggiungere fino a 2000 tester esterni tramite la scheda External Testers (Tester esterni), immettendo il loro indirizzo di posta elettronica, nome e cognome, come illustrato nello screenshot seguente. L'indirizzo di posta elettronica immesso non deve essere l'ID Apple, ma l'indirizzo che riceverà l'invito.

[![](testflight-images/add-external.png "Invite testers")](testflight-images/add-external.png#lightbox)

Se il numero di tester esterni è elevato, è possibile usare il collegamento **Import File** (Importa file) per importare un file `CSV` con il formato seguente per ogni riga:

``` 
first name, last name, email address
```

È anche possibile aggiungere tester esterni a gruppi diversi per organizzarli.

Dopo aver immesso i dettagli dei tester esterni, fare clic su **Add** (Aggiungi) e confermare di avere il consenso degli utenti a inviare un invito:

[![](testflight-images/confirm-consent.png "Confirm you have the users consent to invite them")](testflight-images/confirm-consent.png#lightbox)

Se la revisione della versione beta dell'app ha esito positivo, è possibile inviare gli inviti ai tester esterni. A questo punto, il testo sotto **External** (Esterno) nella pagina di compilazione diventerà **Send Invites** (Invia inviti). Fare clic sul collegamento per inviare gli inviti a tutti i tester che sono stati aggiunti.

Se l'app è stata rifiutata, è necessario risolvere i problemi visualizzati in **Resolution Center** (Centro risoluzioni) e inviare nuovamente l'intero file binario aggiornato per la revisione.

## <a name="as-a-beta-tester"></a>Tester beta

Dopo essere stati inviati, i tester riceveranno un messaggio di posta elettronica simile a quello nello screenshot seguente:

[![](testflight-images/tester-email.png "An example invite email")](testflight-images/tester-email.png#lightbox)

Fare clic sul pulsante **Open in TestFlight** (Aprire in TestFlight) per aprire l'applicazione TestFlight. Se non è già stata scaricata, si aprirà direttamente l'App Store in cui è possibile scaricarla.

Dopo aver aperto l'app in TestFlight, saranno visualizzati i dettagli di quanto è necessario e al tester sarà chiesto di installare l'applicazione nel proprio dispositivo iOS 8.0 (o versione successiva):

[![](testflight-images/install-app.png "TestFlight will show details of what to test for")](testflight-images/install-app.png#lightbox)

Le compilazioni di test vengono contrassegnate nella schermata iniziale del dispositivo da un punto arancione che precede il nome dell'applicazione.

Tramite l'app TestFlight i tester possono inviare commenti e suggerimenti che saranno poi scaricabili dall'indirizzo di posta elettronica specificato nei metadati.

### <a name="beta-testing-complete"></a>Test beta completato

Dopo aver completato il test della versione beta, è ora possibile inviare l'app da sottoporre alla revisione nell'App Store di Apple. Questo processo avviene molto semplicemente in iTunes Connect facendo clic sul pulsante **Submit for Review** (Invia per la revisione), come illustrato di seguito:

[![](testflight-images/submit-for-review.png "Click the Submit for Review button")](testflight-images/submit-for-review.png#lightbox)

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come usare il test della versione beta in TestFlight di Apple tramite iTunes Connect. Ha descritto come caricare una nuova compilazione in iTunes Connect e come invitare tester beta interni ed esterni a usare l'app.

## <a name="related-links"></a>Collegamenti correlati

- [Creazione di un record iTunes Connect](~/ios/deploy-test/app-distribution/app-store-distribution/itunesconnect.md#creating)
- [Pubblicazione nell'App Store](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
- [Provisioning di un'app per la distribuzione nell'App Store](~/ios/deploy-test/app-distribution/app-store-distribution/index.md#provisioning)
- [Uso del test della versione beta in TestFlight di Apple](https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Chapters/BetaTestingTheApp.html#//apple_ref/doc/uid/TP40011225-CH35-SW2)
