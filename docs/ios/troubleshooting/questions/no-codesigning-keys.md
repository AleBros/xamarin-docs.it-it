---
title: 'Perché viene compilazioni iOS eseguito con: nessun codice iPhone valido le chiavi di firma disponibile nel portachiavi?'
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9DF24C46-D521-4112-9B21-52EA4E8D90D0
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.openlocfilehash: 5334e3009906896644caa47c715f912fa379c627
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
ms.locfileid: "30776690"
---
# <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychain"></a>Perché viene compilazioni iOS eseguito con: nessun codice iPhone valido le chiavi di firma disponibile nel portachiavi?

## <a name="cause-of-the-error"></a>Causa dell'errore
Questo messaggio di errore si verifica quando il progetto in questione esegue la ricerca di credenziali di firma codice valide ma non è possibile trovarli. La firma del codice è obbligatorio per il test e le distribuzioni nei dispositivi iOS fisici; nonché Ad-hoc & App archivio compilazioni. 


### <a name="provisioning-devices"></a>Il provisioning di dispositivi
Se è ancora stato effettuato il provisioning di un dispositivo iOS prima, la seguente Guida verrà visualizzata la procedura dettagliata completa: [Guida Provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md)


## <a name="bug-when-using-ios-simulator"></a>Bug quando si utilizza simulatore iOS

> [!NOTE]
> Questo problema è stato risolto nelle versioni recenti di Xamarin per Visual Studio. Tuttavia, se il problema si verifica la versione più recente del software, inviare un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) il completo controllo delle versioni delle informazioni e full output del log di compilazione.


Si è verificato un bug in Xamarin.Visual Studio 3.11 che ha causato il progetto iOS in un modello di xamarin. Forms per aggiungere che codesign Entitlements.plist al simulatore compila; il blocco in modo efficace di test tramite il simulatore.

### <a name="how-to-fix"></a>Come correggere
È possibile risolvere il problema rimuovendo il `<CodesignEntitlements>` compila il flag di debug nel file csproj. Procedere come segue:

*Avviso: Gli errori nei file con estensione csproj possono interrompere il progetto, pertanto è consigliabile eseguire il backup del file prima di procedere.*

1. Fare clic con il pulsante destro sul progetto iOS nel riquadro di soluzione e selezionare **Scarica progetto**
2. Fare clic nuovamente il progetto e selezionare **modifica csproj [NomeProgetto]**
3. Individuare i gruppi di proprietà di Debug, deve iniziare con flag simili al seguente:
   - Eseguire il debug: `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|iPhoneSimulator' ">`
   - Versione: `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhoneSimulator' ">`
4. In ognuna delle compilazioni che usi il simulatore, eliminare o impostare come commento la seguente proprietà: `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. Ricaricare il progetto e dovrebbe essere in grado di distribuire al simulatore.

### <a name="next-steps"></a>Passaggi successivi
Per ulteriore assistenza, contattare Microsoft, o se il problema rimane anche dopo l'utilizzo di queste informazioni, vedere [quali opzioni di supporto sono disponibili per Xamarin?](~/cross-platform/troubleshooting/support-options.md) per informazioni sulle opzioni di contatto, i suggerimenti, nonché come Se necessario, archiviare un nuovo bug. 
