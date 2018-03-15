#<a name="---"></a>---
titolo: ms. topic "Parte 6 – test e App Store approvazioni": article ms. Prod: ms. AssetID xamarin: ms. Technology 46E0578A-7EB9-C105-ABB0-A043E501F36B: autore xamarin multipiattaforma: author asb3993: amburns ms. date: 23/03/2017
---

# <a name="part-6---testing-and-app-store-approvals"></a>Parte 6 - test e le approvazioni App Store

<a name="Testing" />


## <a name="testing"></a>Test

Sarà necessario passare un processo di approvazione prima che vengano pubblicati; molte App (anche le app Android, in alcuni archivi) test è fondamentale per garantire l'applicazione raggiunge il mercato (tanto ha esito positivo con i clienti). Test può assumere forme diverse, da sviluppatori a livello di unit test per la gestione dei test in un'ampia gamma di componenti hardware della versione beta.

 <a name="Test_on_All_Platforms" />


### <a name="test-on-all-platforms"></a>In tutte le piattaforme di test

Vi sono piccole differenze tra quelle .NET supportate in Windows phone, tablet e nei dispositivi desktop, nonché le limitazioni in iOS che impediscono di codice dinamico deve essere generato in tempo reale. Il piano di test del codice su più piattaforme durante lo sviluppo, o pianifica un periodo per effettuare il refactoring e aggiorna la parte del modello dell'applicazione alla fine del progetto.

È sempre consigliabile usare il simulatore o dell'emulatore per più versioni del sistema operativo e anche funzionalità di diversi dispositivi e le configurazioni di test.

È necessario testare anche in tutti i dispositivi hardware fisico diverso come è possibile.

 <a name="Devices_in_cloud" />


#### <a name="devices-in-cloud"></a>Dispositivi nel cloud

L'ecosistema tablet e telefoni mobili sta crescendo simultaneamente, rendendo impossibile eseguire i test sul numero sempre maggiore di dispositivi disponibili. Per risolvere il problema di che un numero di servizi offerta la possibilità di controllare in remoto i dispositivi diversi, in modo che le applicazioni possono essere installate e test senza dover investire direttamente in un numero elevato di hardware.

[Test di App Center](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest) offre un modo semplice per testare applicazioni iOS e Android in centinaia di dispositivi diversi.

 <a name="Test_Management" />


### <a name="test-management"></a>Gestione test

Durante il test delle applicazioni all'interno dell'organizzazione o la gestione di un programma beta con utenti esterni, esistono due sfide:

-   **Distribuzione** : gestione del processo di provisioning (soprattutto per i dispositivi iOS) e il recupero di versioni aggiornate del software ai tester.
-   **Commenti e suggerimenti** : la raccolta di informazioni sull'utilizzo dell'applicazione e informazioni dettagliate sugli errori che possono verificarsi.


Esistono una serie di servizi consentono di risolvere questi problemi, fornendo un'infrastruttura che viene compilata nell'applicazione per raccogliere e segnalare errori e utilizzo e inoltre semplificare il processo di provisioning per agevolare iscrizione e gestire i propri dispositivi e i tester .

Il [Xamarin Insights anteprima](http://xamarin.com/insights) offre una soluzione per la seconda parte di questo problema, fornendo l'arresto anomalo del sistema informazioni sull'utilizzo dell'applicazione reporting e complesse.


 <a name="Test_Automation" />


### <a name="test-automation"></a>Automazione dei test

Xamarin [UITest](https://docs.microsoft.com/appcenter/test-cloud/preparing-for-upload/uitest) può essere utilizzato per creare script di test che possono essere eseguiti in locale o caricato in interfaccia utente automatizzati [App centro Test](https://docs.microsoft.com/appcenter/test-cloud/).


 <a name="Unit_Testing" />


## <a name="unit-testing"></a>Testing unità

 <a name="Touch.Unit" />


#### <a name="touchunit"></a>Touch.Unit

Xamarin include un framework di unit test denominato Touch.Unit che segue lo stile JUnit/NUnit la scrittura di test.

Consultare il nostro [Unit test con xamarin. IOS](~/ios/deploy-test/touch.unit.md) documentazione per informazioni dettagliate sulla scrittura di test e in esecuzione Touch.Unit.

 <a name="Andr.Unit" />


#### <a name="andrunit"></a>Andr.Unit

È presente un equivalente di open source di Touch.Unit per Android chiamato Andr.Unit. È possibile scaricarlo da [github](https://github.com/spouliot/Andr.Unit) e leggere sullo strumento su [ @spouliotdel blog](http://spouliot.wordpress.com/2011/10/30/andr-unit-joins-the-family/).

 <a name="Windows_Phone" />


#### <a name="windows-phone"></a>Windows Phone

Di seguito sono riportati alcuni collegamenti per il programma di installazione unit test per Windows Phone:

-   [http://www.jeff.wilcox.name/2010/05/sl3-utf-bits/](http://www.jeff.wilcox.name/2010/05/sl3-utf-bits/)
-   [http://www.jeff.wilcox.name/2011/06/updated-ut-mango-bits/](http://www.jeff.wilcox.name/2011/06/updated-ut-mango-bits/)
-   [http://www.smartypantscoding.com/a-cheat-sheet-for-unit-testing-silverlight-apps-on-windows-phone-7](http://www.smartypantscoding.com/a-cheat-sheet-for-unit-testing-silverlight-apps-on-windows-phone-7)
-   [http://mobile.dzone.com/articles/unit-testing-your-windows](http://mobile.dzone.com/articles/unit-testing-your-windows)


&nbsp;

 <a name="App_Store_Approvals" />


## <a name="app-store-approvals"></a>Approvazioni App Store

Apple e Microsoft unico archivio nelle rispettive piattaforme di operare: App Store e Marketplace rispettivamente. Bloccare i dispositivi e implementare un processo di revisione applicazione rigorosi per controllare la qualità delle applicazioni disponibili per il download. Di Android aprire natura sono disponibili numerose opzioni di archivio che vanno da Google Play, che è ampiamente disponibile e non dispone di alcun processo di revisione per Appstore di Amazon per gli sforzi per Android e specifiche dell'hardware come App Samsung più limitato distribuzione e implementare un processo di approvazione.

In attesa di un'app per la revisione possono essere molto senza - maggiormente le esigenze di business spesso significa applicazioni vengono inviate per l'approvazione con il margine minimo per correggere l'errore prima di una data di avvio "destinazione". Il processo può richiedere fino a due settimane e non è necessariamente trasparente: è limitato feedback sullo stato di avanzamento dell'applicazione fino a quando non viene infine rifiutato o approvato. Rifiuto significano manca una finestra del marketing di opportunità, soprattutto se si verifica più volte e il passaggio di settimane tra la data di avvio originale e quando l'applicazione viene infine approvata.

 <a name="Be_prepared" />


### <a name="be-prepared"></a>Essere preparati

Nella prima parte dei suggerimenti: pianificare in anticipo l'avvio dell'applicazione e quindi tenere conto per un rifiuto possibili e la presentazione. Ogni archivio richiede di creare un account prima dell'invio dell'app, eseguire questa operazione non appena possibile.
Durante l'iscrizione di Google Play richiede solo alcuni minuti se sono disponibili le app, il processo ottiene molto più complesso se si vendono li ed è necessario fornire servizi bancari e fiscali informazioni. Apple e i processi di Microsoft sono entrambi molto più complessa di Google, potrebbero richiedere una settimana o per l'account approvato così scomporre questo momento in piani di avvio.

Quando l'account è stata approvata, si è pronti per l'invio di un'app. Il processo per l'invio di applicazioni viene descritta nella seguente documentazione:

-   [La pubblicazione in App Store iOS di Apple](~/ios/deploy-test/app-distribution/app-store-distribution/publishing-to-the-app-store.md)
-   [Preparazione di un'applicazione per Google Play](~/android/deploy-test/publishing/publishing-to-google-play/index.md)
-  Gli sviluppatori di Windows, visitare il [Windows Dev Center](https://developer.microsoft.com/en-us/windows/windows-apps) leggere sull'invio delle app.


Nella parte restante di questa sezione vengono illustrati i fattori che è opportuno tenere in considerazione per assicurare che l'app è approvata senza eventuali interruzioni a singhiozzo.

 <a name="Quality" />


### <a name="quality"></a>Qualità

Sembra ovvio, ma le applicazioni verranno rifiutate spesso perché non soddisfa un determinato livello di qualità: Dopotutto, questo è il motivo per cui gli archivi curati hanno un processo di approvazione in primo luogo!

Arresti anomali del sistema sono una causa comune di rifiuto. Se è troppo facile rendere l'arresto anomalo dell'applicazione, è certo che si rifiutate. La maggior parte degli sviluppatori non inviano le proprie App nella previsione che verrà arrestano, ma spesso. Testare accuratamente l'app prima di inviarlo, messa a fuoco non solo ad assicurandosi che tutto funziona ma anche di gestire comuni scenari di errore per dispositivi mobili, ad esempio problemi di rete e i vincoli di risorse come memoria o spazio di archiviazione. Usare il simulatore e i dispositivi fisici per eseguire il test, indipendentemente dalla modalità codice viene eseguito in un simulatore, solo un dispositivo in grado di dimostrare reali prestazioni di un'app. Utilizzare i diversi dispositivi come è possibile trovare e integrare un team di beta tester se è possibile: servizi di terze parti possono semplificare la gestione di commenti e suggerimenti e la distribuzione beta.

Tutti i sistemi operativi per dispositivi mobili verrà terminare un'applicazione che non si avvia in modo sufficientemente rapido. Il periodo di tempo consentito varia, ma in generale le app consigliabile mirare a ottenere risponde in pochi secondi e utilizzare attività in background per eseguire operazioni che potrebbero richiedere più tempo. Le applicazioni che richiedono troppo tempo per caricare o non risponde in uso regolari sono verrà rifiutate. Quando un elemento avviene in background o l'app verrà visualizzata anomali e rifiutati ancora una volta, sempre fornire i commenti degli utenti.

 <a name="Check_Your_Edge_Cases" />


### <a name="check-your-edge-cases"></a>Controllare i casi limite

Trap comune per gli sviluppatori non riesce a indirizzo-casi limite, specialmente quelle che richiedono riconfigurazione il simulatore o dispositivo per verificare correttamente. Può essere facile dimenticare che non tutti i clienti verrà "Consenti" app di accedere al percorso perché dopo che lo sviluppatore ha accettato la richiesta di una volta, non si verrà mai richiesto nuovamente. Utilizzo di rete e le autorizzazioni sono specificamente relativo durante il processo di approvazione, ovvero che una piccola supervisione in queste aree può causare il rifiuto.

Nell'elenco seguente è un buon punto di partenza per la verifica di casi limite che potrebbero essere stati esclusi:

-   **I clienti possono "Nega" accesso ai servizi** : soprattutto in iOS, ad esempio informazioni di posizione geografica è disponibile solo se l'utente concede l'autorizzazione all'applicazione l'accesso ai dati. Tester di applicazioni spesso devono installare nuovamente l'applicazione nello stato iniziale e non consentire le richieste di autorizzazione per verificare che l'applicazione si comporta in modo appropriato. Attivare l'autorizzazione e off per verificare il comportamento corretto come cambiare i clienti.
-   **I clienti si trovano ovunque** : non si supponga che un'app verrà utilizzata solo nella città o il paese in cui è stato sviluppato. Codice compatibile con le coordinate GPS, i valori di data e ora e valuta può essere tutti interessato dalla posizione del cliente e le impostazioni locali. Tutte le piattaforme offerta un simulatore che consentono di specificare posizioni diverse e le impostazioni locali - utilizzato per posizioni di test in altri emisferi e con le impostazioni cultura che formattare date e valute in modo diverso. I valori di latitudine e longitudine possono essere positivo o negativo, il separatore decimale può essere un punto o una virgola e date possono essere formattate numerose modi - occuparsene!
-   **Connessioni di rete lente** : gli sviluppatori di app spesso operare in un mondo' ideale' lavorare in modo rapido, sempre la connettività di rete, ovviamente, non è il caso nel mondo reale. Test di connettività di rete lenta (ad esempio una connessione 3G scarsa) e con nessun accesso alla rete è essenziale per garantire che un'app anomalo non vengono inviati. Il processo di approvazione verrà sempre includere un test con il dispositivo in modalità aereo, verificare che sia stata testata che per se stessi.
-   **Hardware varia** : ricordare di test sull'hardware che si prevede di supportare più vecchio, più lenta. Esistono due aspetti che potrebbero incidere sull'app: prestazioni, potrebbero essere inutilizzabile in un dispositivo precedente e il supporto per funzionalità hardware, ad esempio una videocamera, microfono, GPS, Giroscopio o altri componenti facoltativi. Dovrebbero influire negativamente sulle applicazioni normalmente (e non dell'arresto anomalo) quando un componente non è disponibile.


 <a name="Guidelines_are_more_than_just_a_‘guide’" />


### <a name="guidelines-are-more-than-just-a-guide"></a>Linee guida sono molto più di una 'Guida'

Apple è noti per essere strict sulla conformità per le linee guida dell'interfaccia umana come uno dei principali punti di forza di propria piattaforma coerenza (e l'aumento percepito di usabilità). Microsoft ha adottato un approccio simile con applicazioni di Windows che implementa l'interfaccia utente in stile Metro. Il processo di approvazione per entrambe le piattaforme comporterà l'app viene valutato per l'adesione alla filosofia di progettazione rilevanti.

Questo non significa che l'innovazione dell'interfaccia utente non è supportato o sconsigliato, ma alcuni aspetti "solo effettuare operazione" o l'app verrà rifiutato.

In iOS, inclusi impropriamente icone predefinite o tramite altre metafore consolidati in modo non coerente. ad esempio utilizzando l'icona 'comporre' per un elemento diverso dalla creazione di nuovo contenuto.

Gli sviluppatori di Windows devono essere allo stesso modo attenzione; un errore comune non riesce a supportare l'hardware del nuovo pulsante in base alle linee guida Microsoft.

Favorire le finestre di progettazione per leggere e seguire le linee guida di progettazione per ogni piattaforma.

 <a name="Implementing_Platform-Specific_Features" />


### <a name="implementing-platform-specific-features"></a>Implementare le funzionalità specifiche della piattaforma

Gli aspetti sono un po' più restrittivo per quanto riguarda l'implementazione di servizi specifico della piattaforma, soprattutto in iOS. Per evitare automatico-rifiuto da Apple, sono disponibili alcune regole per effettuare le seguenti funzionalità di iOS:

-   **Acquisti in-App** – applicazioni non devono implementare meccanismi di pagamento esterno per i prodotti digitali inclusi valuta di gioco, le funzionalità dell'applicazione, le sottoscrizioni riviste e altro ancora. le app iOS devono usare per consentire ai servizi basati su iTunes di Apple per questo tipo di funzionalità. È presente un punto debole - App come il lettore Kindle e alcune applicazioni basato su sottoscrizione consentono di acquistare contenuti in un punto che viene associato a un "account" è quindi possibile accedere tramite l'app, tuttavia in questo caso l'app non può contenere collegamenti o fare riferimento al timeout dell'app processo di acquisto (o, ancora una volta, si verrà rifiutato).
-   **backup in iCloud** : con l'avvento del iCloud, sono molto più rigoroso revisori di Apple relative come app di usano l'archiviazione (per verificare l'esperienza di backup remoto del cliente è piacevole). Le app che lo spazio di archiviazione in grado di backup rifiuti può ottenere rifiutato, pertanto utilizzare la cartella della Cache in modo appropriato e seguire Apple dell'altre linee guida relative alla memoria.
-   **Newsstand** – giornali e riviste App sono un'ottima soluzione per Newsstand di Apple, tuttavia, le applicazioni devono implementare almeno il rinnovo automatico sottoscrizione supporto sfondo e download per essere approvato.
-   **Esegue il mapping** – è sempre più comune per aggiungere mappe mobili sovrapposizioni e altre funzionalità, tuttavia attenzione a non essere per nascondere la mappa 'crediti' informazioni (ad esempio, il logo di Google in iOS5) con questa operazione determinerà il rifiuto.


 <a name="Manage_Your_Metadata" />


### <a name="manage-your-metadata"></a>Gestire i metadati

Oltre ai problemi tecnici evidenti che possono comportare un'applicazione viene rifiutata, sono disponibili alcune più complessi aspetti di presentazione che potrebbe causare il rifiuto, specialmente per i metadati (descrizione, le parole chiave e le immagini di marketing) che si inviare l'applicazione per la visualizzazione all'interno dell'App Store o Marketplace.

-   **Immagini** : attenersi alle linee guida della piattaforma per le icone di applicazione e archiviare le immagini. Non utilizzare immagini marchio, abbiamo visto App rifiutate in quanto le relative icone in evidenza un disegno di un iPhone.
-   **Marchi** : evitare di utilizzare qualsiasi marchi diverso dal proprio. App sono state negate per citare marchi nella descrizione dell'app o anche le parole chiave di Apple App Store.
-   **Descrizione** : non utilizzare la parola "beta" o in qualsiasi modo indicano che l'applicazione non è pronto per. Non fanno riferimento ad altre piattaforme per dispositivi mobili (anche se l'app è multipiattaforma). Verificare in particolare, che l'app non esattamente ciò che si dice che avviene. Se si specifica una serie di funzionalità nella descrizione, è meglio sia ovvio come utilizzare ognuna di queste funzionalità o si otterrà un rifiuto "funzionalità indicate nella descrizione dell'applicazione non è implementata".


Inserire come impegno nei metadati dell'applicazione in sviluppo e test. Applicazioni ottenere rifiutate per violazioni secondarie nei metadati, è quindi utile per il tempo per cui viene eseguita correttamente.

 <a name="App_Stores:_Not_For_Everyone" />


### <a name="app-stores-not-for-everyone"></a>App Store: Non per tutti gli utenti

L'obiettivo principale degli archivi in ogni piattaforma è distribuzione consumer - la possibilità di raggiungere il numero di clienti possibile. Tuttavia, non tutte le applicazioni sono destinate al consumer, è presente un rapida crescita di applicazioni interne ed extranet simile che richiedono distribuzione limitata ai dipendenti, fornitori o ai clienti. Queste applicazioni non sono "per la vendita" e non richiedono l'approvazione, dopo la distribuzione di controlli per sviluppatori a un gruppo di utenti chiuso.
Supporto per questo tipo di distribuzione varia a seconda della piattaforma.

Android offre la massima flessibilità, in questo senso: è possibile installare applicazioni direttamente da un allegato di posta elettronica o URL (purché ne consente la configurazione del dispositivo). Questo significa che è molto semplice per creare e distribuire le applicazioni aziendali interne o pubblicare le applicazioni a clienti specifici o fornitori.

Apple fornisce un'opzione di distribuzione interne per gli sviluppatori registrati in iOS Developer Enterprise Program, che ignora il processo di approvazione di App Store e consente alle aziende di distribuire le app internamente ai propri dipendenti.
Purtroppo questa licenza non soddisfare le esigenze per la distribuzione di app extranet simile ad altri gruppi di clienti o fornitori chiusi. [Enterprise (e Ad-Hoc) distribuzione](~/ios/deploy-test/app-distribution/ipa-support.md)


 <a name="App_Store_Summary" />


### <a name="app-store-summary"></a>Riepilogo di App Store

Il processo di revisione può risultare scoraggiante, ma come il resto del ciclo di vita di sviluppo consentono di garantire la corretta esecuzione con una pianificazione e prestare attenzione ai dettagli. Tutto dipende da alcuni semplici passaggi: leggere e comprendere le linee guida dell'interfaccia utente, è necessario adottare, seguire le regole, se si implementa funzionalità specifiche della piattaforma, effettuare test approfonditi (quindi testare altri) e infine verificare che i metadati dell'applicazione sia corretto prima di inviare.

Una parola ultimo dei suggerimenti agli sviluppatori la pubblicazione in Google Play: la mancanza di processo di approvazione potrebbe sembrare semplifica il processo, ma i clienti saranno ancora più complesse rispetto a un team di revisione. Seguire queste linee guida come se l'app può ottenere rifiutato, in caso contrario sarà ai clienti di eseguire il rifiuto.

