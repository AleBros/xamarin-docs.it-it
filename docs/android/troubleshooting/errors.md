---
title: Matrice di errori di xamarin
ms.topic: article
ms.prod: xamarin
ms.assetid: 7EBE4C01-8EFC-4B7E-97BA-D879994F59AB
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 1c9284f3db1c503b5c88f0d310f916f4c9e3363d
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="xamarinandroid-errors-matrix"></a>Matrice di errori di xamarin

## <a name="errors-reference"></a>Riferimento per gli errori

Questo documento fornisce alcune informazioni sui codici di errore diversi da Xamarin.

<table>
    <thead>
        <tr>
            <td>Category</td>
            <td>Descrizione</td>
        </tr>
    </thead>
    <tbody>
        <tr><td>XA0xxx</td><td><code>mandroid</code> Errori</td></tr>
        <tr><td>XA1xxx</td><td>Copia del file o collegamenti simbolici (progetto correlato) errori</td></tr>
        <tr><td>XA2xxx</td><td>Errori del linker</td></tr>
        <tr><td>XA3xxx</td><td>Errori AOT</td></tr>
        <tr><td>XA4xxx</td><td>Errori di generazione di codice</td></tr>
        <tr><td>XA5xxx</td><td>GCC e gli errori Toolchain</td></tr>
        <tr><td>XA6xxx</td><td><code>mandroid</code> Errori interni degli strumenti</td></tr>
        <tr><td>XA7xxx</td><td>Riservata</td></tr>
        <tr><td>XA8xxx</td><td>Riservata</td></tr>
        <tr><td>XA9xxx</td><td>Errori di gestione delle licenze</td></tr>
    </tbody>
</table>

## <a name="error-codes"></a>Codici di errore

### <a name="xa0xxx-errors"></a>Errori XA0xxx

<table>
    <thead>
        <tr><td>Codice di errore</td><td>Descrizione</td></tr>
    </thead>
    <tbody>
        <tr><td>XA0000</td><td>Errore imprevisto: compilare un report di bug in <a href="http://bugzilla.xamarin.com">http://bugzilla.xamarin.com</a></td></tr>
        <tr><td>XA0001</td><td>'-devname è stato specificato senza alcun intervento specifico del dispositivo</td></tr>
        <tr><td>XA0002</td><td>Impossibile analizzare la variabile di ambiente '{0}'.</td></tr>
        <tr><td>XA0003</td><td>Applicazione '{0} .exe' conflitti di nome con un nome di assembly (DLL) SDK o prodotto.</td></tr>
        <tr><td>XA0004</td><td>La nuova logica di refcounting richiede sgen deve essere abilitata troppo.</td></tr>
        <tr><td>XA0005</td><td>La directory di output '{0}' non esiste.</td></tr>
        <tr><td>XA0006</td><td>È presente alcuna piattaforma di sviluppo per sistemi operativi in '{0}', utilizzare - platform = PLAT per specificare il SDK</td></tr>
        <tr><td>XA0007</td><td>L'assembly principale '{0}' non esiste.</td></tr>
        <tr><td>XA0008</td><td>È necessario fornire un solo assembly radice</td></tr>
        <tr><td>XA0009</td><td>Errore durante il caricamento degli assembly: {0}</td></tr>
        <tr><td>XA0010</td><td>Non è riuscito ad analizzare gli argomenti della riga di comando: {0}</td></tr>
        <tr><td>XA0011</td><td>{0} è stato compilato con un runtime più recente (\\{1 \\}) rispetto a MonoTouch supportato</td></tr>
        <tr><td>XA0012</td><td>I dati incompleti viene forniti per completare `{0}`.</td></tr>
        <tr><td>XA0013</td><td>Supporto di profilatura richiede sgen deve essere abilitata troppo</td></tr>
        <tr><td>XA0014</td><td>iOS {0} non supporta l'assegnazione ARMv6 compilazione di applicazioni</td></tr>
        <tr><td>XA0020</td><td>Impossibile determinare il percorso mandroid.</td></tr>
        <tr><td>XA0100</td><td>EmbeddedNativeLibrary '{0}' non è valido nel progetto di applicazione Android. Utilizzare AndroidNativeLibrary.</td></tr>
    </tbody>
</table>

### <a name="xa1xxx-errors"></a>Errori XA1xxx

<table>
    <thead>
        <tr><td>Codice di errore</td><td>Descrizione</td></tr>
    </thead>
    <tbody>
        <tr><td>XA1001</td><td>Impossibile trovare un'applicazione nella directory specificata</td></tr>
        <tr><td>XA1002</td><td>Non è stato possibile creare collegamenti simbolici, sono stati copiati i file</td></tr>
        <tr><td>XA1003</td><td>Impossibile terminare l'applicazione '{0}'. È possibile terminare l'applicazione manualmente.</td></tr>
        <tr><td>XA1004</td><td>Impossibile ottenere l'elenco delle applicazioni installate.</td></tr>
        <tr><td>XA1005</td><td>Impossibile terminare l'applicazione '{0}' nel dispositivo '\\{1 \\}': {2}. È possibile terminare l'applicazione manualmente.</td></tr>
        <tr><td>XA1006</td><td>Impossibile installare l'applicazione '{0}' nel dispositivo '\\{1 \\}': {2}.</td></tr>
        <tr><td>XA1007</td><td>Non è stato possibile avviare l'applicazione '{0}' nel dispositivo '\\{1 \\}': {2}. È comunque possibile avviare manualmente l'applicazione toccando su di esso.</td></tr>
        <tr><td>XA1008</td><td>Impossibile avviare il simulatore: {0}</td></tr>
        <tr><td>XA1009</td><td>Impossibile copiare l'assembly '{0}' a '\\{1 \\}': {2}</td></tr>
        <tr><td>XA1010</td><td>Non è stato possibile caricare l'assembly '{0}': \\{1 \\}</td></tr>
        <tr><td>XA1011</td><td>Impossibile aggiungere il file di risorsa mancante: '{0}'</td></tr>
        <tr><td>XA1101</td><td>Non è stato possibile avviare app</td></tr>
        <tr><td>XA1102</td><td>Impossibile connettersi all'app (per terminarlo): {0}</td></tr>
        <tr><td>XA1103</td><td>Impossibile scollegare</td></tr>
        <tr><td>XA1104</td><td>Impossibile inviare il pacchetto: {0}</td></tr>
        <tr><td>XA1105</td><td>Tipo di risposta non prevista</td></tr>
        <tr><td>XA1106</td><td>Impossibile ottenere l'elenco delle applicazioni nel dispositivo: timeout della richiesta.</td></tr>
        <tr><td>XA1107</td><td>Impossibile avviare l'applicazione</td></tr>
        <tr><td>XA1201</td><td>Non è stato possibile caricare il simulatore: {0}</td></tr>
        <tr><td>XA1301</td><td>La libreria nativa `{0}` (\\{1 \\}) è stata ignorata perché il architecture(s) compilazione corrente ({2}) non corrisponde</td></tr>
    </tbody>
</table>

### <a name="xa2xxx-errors"></a>Errori XA2xxx

<table>
    <thead>
        <tr><td>Codice di errore</td><td>Descrizione</td></tr>
    </thead>
    <tbody>
        <tr><td>XA2001</td><td>Non è stato possibile collegare gli assembly</td></tr>
        <tr><td>XA2002</td><td>Non può risolvere il riferimento: {0}</td></tr>
        <tr><td>XA2003</td><td>Opzione '{0}' verrà ignorata perché il collegamento è disabilitato.</td></tr>
        <tr><td>XA2004</td><td>Impossibile individuare il file di definizione del linker aggiuntive '{0}'.</td></tr>
        <tr><td>XA2005</td><td>Le definizioni di '{0}' non può essere analizzate.</td></tr>
        <tr><td>XA2006</td><td>Riferimento all'elemento dei metadati '{0}' (definito in '\\{1 \\}') da '{2}' non è possibile risolvere.</td></tr>
    </tbody>
</table>

### <a name="xa3xxx-errors"></a>Errori XA3xxx

Si tratta di errori AOT.

<table>
    <thead>
        <tr><td>Codice di errore</td><td>Descrizione</td></tr>
    </thead>
    <tbody>
        <tr><td>XA3001</td><td>Impossibile non AOT l'assembly '{0}'</td></tr>
        <tr><td>XA3002</td><td>Restrizione AOT: metodo '{0}' deve essere statico poiché è decorata con [MonoPInvokeCallback].</td></tr>
        <tr><td>XA3003</td><td>In conflitto: opzioni di debug e - llvm. Debug soft è disabilitato.</td></tr>
    </tbody>
</table>

### <a name="xa4xxx-errors"></a>Errori XA4xxx

Si tratta di errori di generazione di codice.

<table>
    <thead>
        <tr><td>Codice di errore</td><td>Descrizione</td></tr>
    </thead>
    <tbody>
        <tr><td>XA4001</td><td>Il modello principale non è stato expansed a `{0}`.</td></tr>
        <tr><td>XA4101</td><td>Il programma di registrazione non è possibile generare una firma per il tipo `{0}`.</td></tr>
        <tr><td>XA4102</td><td>Il programma di registrazione trovato un tipo non valido `{0}` nella firma del metodo `{2}`. In alternativa, usare `{1}`.</td></tr>
        <tr><td>XA4103</td><td>Il programma di registrazione trovato un tipo non valido `{0}` nella firma del metodo `{2}`: il tipo implementa INativeObject, ma non dispone di un costruttore che accetta due (IntPtr, bool) argomenti</td></tr>
        <tr><td>XA4104</td><td>Il programma di registrazione non è possibile effettuare il marshalling del valore restituito per il tipo `{0}` nella firma del metodo `{1}`.</td></tr>
        <tr><td>XA4105</td><td>Il programma di registrazione non è possibile effettuare il marshalling del parametro di tipo `{0}` nella firma del metodo `{1}`.</td></tr>
        <tr><td>XA4106</td><td>Il programma di registrazione non è possibile effettuare il marshalling del valore restituito per una struttura `{0}` nella firma del metodo `{1}`.</td></tr>
        <tr><td>XA4107</td><td>Il programma di registrazione non è possibile effettuare il marshalling del parametro di tipo `{0}` nella firma del metodo `{1}`.</td></tr>
        <tr><td>XA4108</td><td>Il programma di registrazione non è possibile ottenere il tipo di ObjectiveC per un tipo gestito `{0}`.</td></tr>
        <tr><td>XA4109</td><td>Impossibile compilare il codice generato registrar. Inviare una segnalazione di bug in <a href="http://bugzilla.xamarin.com">http://bugzilla.xamarin.com</a></td></tr>
        <tr><td>XA4110</td><td>Il programma di registrazione non è possibile effettuare il marshalling il parametro out di tipo `{0}` nella firma del metodo `{1}`.</td></tr>
        <tr><td>XA4111</td><td>Il programma di registrazione non è possibile generare una firma per il tipo `{0}' in method `\\{1 \\} '.</td></tr>
        <tr><td>XA4200</td><td>Può solo generare del ACW per i tipi 'claas'.</td></tr>
        <tr><td>XA4201</td><td>Impossibile determinare il nome JNI per il tipo {0}.</td></tr>
        <tr><td>XA4203</td><td>Nome del tipo specificato deve essere completo.</td></tr>
        <tr><td>XA4204</td><td>Impossibile risolvere il tipo di interfaccia '{0}'. Probabilmente manca un riferimento a un assembly.</td></tr>
        <tr><td>XA4205</td><td>[ExportField] sono utilizzabili solo in metodi con parametri, 0.</td></tr>
        <tr><td>XA4206</td><td>[Esportazione] non può essere utilizzata su un tipo generico.</td></tr>
        <tr><td>XA4207</td><td>[ExportField] può essere usato in un tipo generico.</td></tr>
        <tr><td>XA4208</td><td>[Java.Interop.ExportFieldAttribute] può essere usato in un metodo che restituisce void.</td></tr>
        <tr><td>XA4209</td><td>Impossibile creare JavaTypeInfo per classe: {0} a causa di \\{1 \\}</td></tr>
        <tr><td>XA4210</td><td>È necessario aggiungere un riferimento a Mono.Android.Export.dll quando si utilizza ExportAttribute o ExportFieldAttribute.</td></tr>
        <tr><td>XA4211</td><td>AndroidManifest.xml //uses-sdk/@android:targetSdkVersion '{0}' è minore di $(TargetFrameworkVersion) '\\{1 \\}'. Utilizzando API-\  per la compilazione ACW.</td></tr>
    </tbody>
</table>

### <a name="xa5xxx-errors"></a>Errori XA5xxx

<table>
    <thead>
        <tr><td>Codice di errore</td><td>Descrizione</td></tr>
    </thead>
    <tbody>
        <tr><td>XA5101</td><td>Manca un compilatore '{0}'. Installare Android NDK.</td></tr>
        <tr><td>XA5102</td><td>Conversione da assembly in codice nativo non è riuscita. Inviare una segnalazione di bug in <a href="http://bugzilla.xamarin.com">http://bugzilla.xamarin.com</a></td></tr>
        <tr><td>XA5103</td><td>Impossibile compilare il file '{0}'. Inviare una segnalazione di bug in <a href="http://bugzilla.com">http://bugzilla.xamarin.com</a></td></tr>
        <tr><td>XA5201</td><td>Collegamento nativo non è riuscita. Verificare i flag di utente per gcc: {0}</td></tr>
        <tr><td>XA5202</td><td>Collegamento nativo non è riuscita. Esaminare il log di compilazione.</td></tr>
        <tr><td>XA5303</td><td>Collegamento avviso nativo: {0}</td></tr>
        <tr><td>XA5300</td><td>Android SDK non trovato o non è completamente installato.</td></tr>
        <tr><td>XA5301</td><td>Strumento 'striscia' mancante. Installare il componente 'Strumenti da riga di comando' Xcode</td></tr>
        <tr><td>XA5302</td><td>Strumento 'dsymutil' mancante. Installare il componente 'Strumenti da riga di comando' Xcode</td></tr>
        <tr><td>XA5203</td><td>Impossibile generare i simboli di debug (dSYM directory). Esaminare il log di compilazione.</td></tr>
        <tr><td>XA5204</td><td>Impossibile rimuovere il file binario finale. Esaminare il log di compilazione.</td></tr>
        <tr><td>XA5205</td><td>Strumento 'aapt' mancante. Installare il pacchetto di strumenti di compilazione di Android SDK.</td></tr>
        <tr><td>XA5206</td><td>{0}. \\{1 \\} directory Android risorsa non esiste.</td></tr>
        <tr><td>XA5207</td><td>{0}. Java \\{1 \\} file di libreria non esiste.</td></tr>
        <tr><td>XA5208</td><td>Download non riuscito. Scaricare {0} e inserirlo nella directory \\{1 \\}.</td></tr>
        <tr><td>XA5209</td><td>Decompressione non riuscita. Scaricare {0} e decomprimerlo nella directory \\{1 \\}.</td></tr>
        <tr><td>XA5210</td><td>{0}. \\{1 \\} file libreria nativa non esiste.</td></tr>
    </tbody>
</table>

### <a name="xa6xxx-errors"></a>Errori XA6xxx

<table>
    <thead>
        <tr><td>Codice di errore</td><td>Descrizione</td></tr>
    </thead>
    <tbody>
        <tr><td>XA6001</td><td>Versione in esecuzione di Cecil non supporta la rimozione di assembly</td></tr>
        <tr><td>XA6002</td><td>Non può rimuovere assembly `{0}`.</td></tr>
        <tr><td>XA6003</td><td>Messaggio UnauthorizedAccessException]</td></tr>
    </tbody>
</table>

### <a name="xa9xxx-errors"></a>Errori XA9xxx

I codici di questo errore sono errori di licenze e l'attivazione.



#### <a name="xa9000"></a>XA9000

 **Causa:** licenza scaduta

 **Controllati durante:** di compilazione

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Immaginare</th>
            <th>Business(trial)</th>
            <th>Ufficio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>AVVISO</td>
            <td>AVVISO</td>
            <td>AVVISO</td>
            <td>AVVISO</td>
            <td>AVVISO</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9001"></a>XA9001

 **Causa:** versione di valutazione scaduta

 **Controllati durante:** di compilazione

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Immaginare</th>
            <th>Business(trial)</th>
            <th>Ufficio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>AVVISO</td>
            <td>AVVISO</td>
            <td>AVVISO</td>
            <td>AVVISO</td>
            <td>AVVISO</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9002"></a>XA9002

 **Causa:** AndroidJavaSource

 **Controllati durante:** di compilazione

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Immaginare</th>
            <th>Business(trial)</th>
            <th>Ufficio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERRORE</td>
            <td>OK</td>
            <td>OK</td>
            <td>OK</td>
            <td>OK</td>
        </tr>
    </tbody>
</table>

 **Causa:** AndroidJavaLibrary

 **Controllati durante:** di compilazione

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Immaginare</th>
            <th>Business(trial)</th>
            <th>Ufficio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERRORE</td>
            <td>OK</td>
            <td>OK</td>
            <td>OK</td>
            <td>OK</td>
        </tr>
    </tbody>
</table>

 **Se un assembly di associazione ha il JAR incorporati, questo viene intercettato in fase di pacchetto, non in fase di compilazione.**

 **Causa:** AndroidNativeLibrary

 **Controllati durante:** di compilazione

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Immaginare</th>
            <th>Business(trial)</th>
            <th>Ufficio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERRORE</td>
            <td>OK</td>
            <td>OK</td>
            <td>OK</td>
            <td>OK</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9003"></a>XA9003

 **Causa:** Serialization

 **Controllati durante:** pacchetto

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Immaginare</th>
            <th>Business(trial)</th>
            <th>Ufficio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERRORE</td>
            <td>ERRORE</td>
            <td>OK</td>
            <td>OK</td>
            <td>OK</td>
        </tr>
    </tbody>
</table>

 **Causa:** System.ServiceModel.Web

 **Controllati durante:** pacchetto

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Immaginare</th>
            <th>Business(trial)</th>
            <th>Ufficio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERRORE</td>
            <td>ERRORE</td>
            <td>OK</td>
            <td>OK</td>
            <td>OK</td>
        </tr>
    </tbody>
</table>

 **Causa:** Mono.Data.Tds

 **Controllati durante:** di compilazione

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Immaginare</th>
            <th>Business(trial)</th>
            <th>Ufficio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERRORE</td>
            <td>ERRORE</td>
            <td>OK</td>
            <td>OK</td>
            <td>OK</td>
        </tr>
    </tbody>
</table>

 **Questo fa riferimento a System.Data.dll, che è consentito**

 **Causa:** Mono.Android.Export

 **Controllati durante:** di compilazione

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Immaginare</th>
            <th>Business(trial)</th>
            <th>Ufficio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERRORE</td>
            <td>OK</td>
            <td>OK</td>
            <td>OK</td>
            <td>OK</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9004"></a>XA9004

 **Causa:** -profilo

 **Controllati durante:** pacchetto

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Immaginare</th>
            <th>Business(trial)</th>
            <th>Ufficio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERRORE</td>
            <td>ERRORE</td>
            <td>OK</td>
            <td>OK</td>
            <td>OK</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9005"></a>XA9005

 **Causa:** limite dimensione (32 kb)

 **Controllati durante:** pacchetto

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Immaginare</th>
            <th>Business(trial)</th>
            <th>Ufficio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERRORE</td>
            <td>OK</td>
            <td>-</td>
            <td>-</td>
            <td>-</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9006"></a>XA9006

 **Causa:** dello spazio dei nomi SqlClient

 **Controllati durante:** pacchetto

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Immaginare</th>
            <th>Business(trial)</th>
            <th>Ufficio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERRORE</td>
            <td>ERRORE</td>
            <td>OK</td>
            <td>OK</td>
            <td>OK</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9008"></a>XA9008

 **Causa:** compilazione dalla riga di comando

 **Controllati durante:** di compilazione

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Immaginare</th>
            <th>Business(trial)</th>
            <th>Ufficio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERRORE</td>
            <td>ERRORE</td>
            <td>OK</td>
            <td>OK</td>
            <td>OK</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9009"></a>XA9009

 **Causa:** il numero di serie mancante

 **Controllati durante:** Untestable

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Immaginare</th>
            <th>Business(trial)</th>
            <th>Ufficio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERRORE</td>
            <td>ERRORE</td>
            <td>ERRORE</td>
            <td>ERRORE</td>
            <td>ERRORE</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9010"></a>XA9010

 **Causa:** ProductId non valido

 **Controllati durante:** di compilazione

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Immaginare</th>
            <th>Business(trial)</th>
            <th>Ufficio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERRORE</td>
            <td>ERRORE</td>
            <td>ERRORE</td>
            <td>ERRORE</td>
            <td>ERRORE</td>
        </tr>
    </tbody>
</table>

Equivale a XA9018



#### <a name="xa9011"></a>XA9011

 **Causa:** non è stato possibile aggiornare il file di licenza (al nuovo formato di file)

 **Controllati durante:** attivazione

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Immaginare</th>
            <th>Business(trial)</th>
            <th>Ufficio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERRORE</td>
            <td>ERRORE</td>
            <td>ERRORE</td>
            <td>ERRORE</td>
            <td>ERRORE</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9012"></a>XA9012

 **Causa:** internet non disponibile

 **Controllati durante:** attivazione

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Immaginare</th>
            <th>Business(trial)</th>
            <th>Ufficio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERRORE</td>
            <td>ERRORE</td>
            <td>ERRORE</td>
            <td>ERRORE</td>
            <td>ERRORE</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9013"></a>XA9013

 **Causa:** errore sconosciuto

 **Controllati durante:** attivazione

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Immaginare</th>
            <th>Business(trial)</th>
            <th>Ufficio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERRORE</td>
            <td>ERRORE</td>
            <td>ERRORE</td>
            <td>ERRORE</td>
            <td>ERRORE</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9014"></a>XA9014

 **Causa:** codice di attivazione non valido

 **Controllati durante:** attivazione

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Immaginare</th>
            <th>Business(trial)</th>
            <th>Ufficio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERRORE</td>
            <td>ERRORE</td>
            <td>ERRORE</td>
            <td>ERRORE</td>
            <td>ERRORE</td>
        </tr>
    </tbody>
</table>



#### <a name="xa9017"></a>XA9017

 **Causa:** server di attivazione non restituisce una licenza valida

 **Controllati durante:** attivazione

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Immaginare</th>
            <th>Business(trial)</th>
            <th>Ufficio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>ERRORE</td>
            <td>ERRORE</td>
            <td>ERRORE</td>
            <td>ERRORE</td>
            <td>ERRORE</td>
        </tr>
    </tbody>
</table>


#### <a name="xa9018"></a>XA9018

**Causa:** licenza non valida

**Controllati durante:** di compilazione

<table>
    <thead>
        <tr>
            <th>Starter</th>
            <th>Immaginare</th>
            <th>Business(trial)</th>
            <th>Ufficio</th>
            <th>Enterprise</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>-</td>
            <td>-</td>
            <td>ERRORE</td>
            <td>-</td>
            <td>-</td>
        </tr>
    </tbody>
</table>
