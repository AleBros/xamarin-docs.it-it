---
title: "Perché la compilazione iOS non riesce con l'errore: nessuna chiave di firma del codice iPhone valida trovata nel keychain?"
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 9DF24C46-D521-4112-9B21-52EA4E8D90D0
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: fe267db1f83695b3d0e8f3d828f91e01b56ba8ee
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61419665"
---
# <a name="why-does-my-ios-build-fail-with-no-valid-iphone-code-signing-keys-found-in-keychain"></a>Perché la compilazione iOS non riesce con l'errore: nessuna chiave di firma del codice iPhone valida trovata nel keychain?

## <a name="cause-of-the-error"></a>Causa dell'errore
Questo messaggio di errore si verifica quando il progetto in questione esegue la ricerca di credenziali di firma codice valide ma non è possibile trovarli. La firma del codice è necessaria per il test e le distribuzioni nei dispositivi iOS fisici; così come App e Ad-hoc archiviare le compilazioni. 


### <a name="provisioning-devices"></a>Il provisioning dei dispositivi
Se è ancora stato effettuato il provisioning di un dispositivo iOS prima, la Guida seguente illustra in dettaglio la procedura dettagliata completa: [Guida Provisioning dei dispositivi](~/ios/get-started/installation/device-provisioning/index.md)


## <a name="bug-when-using-ios-simulator"></a>Bug quando si usa iOS Simulator

> [!NOTE]
> Questo problema è stato risolto nelle versioni recenti di Xamarin per Visual Studio. Tuttavia, se il problema si verifica la versione più recente del software, inviare un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) il completo controllo delle versioni delle informazioni e full output del log di compilazione.


Si è verificato un bug in Xamarin.Visual Studio fino alla 3.11 che ha causato il progetto iOS in un modello di xamarin. Forms per aggiungere che codesign entitlements. plist per simulatore compilazioni; bloccando effettivamente a test con il simulatore.

### <a name="how-to-fix"></a>Come correggere
È possibile risolvere il problema rimuovendo le `<CodesignEntitlements>` si basa sul flag di debug nel file con estensione csproj. Procedere come segue:

*Avviso: Gli errori nel file con estensione csproj possono interrompere il progetto, pertanto è consigliabile eseguire il backup dei file prima di procedere.*

1. Fare clic con il pulsante destro del progetto iOS nel riquadro della soluzione e selezionare **Scarica progetto**
2. Con il pulsante destro del progetto fare clic su Nuovo e selezionare **modifica csproj [NomeProgetto]**
3. Individuare i gruppi di proprietà di Debug, è consigliabile iniziare con flag con un aspetto simile al seguente:
   - Debug: `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Debug|iPhoneSimulator' ">`
   - Versione: `<PropertyGroup Condition=" '$(Configuration)|$(Platform)' == 'Release|iPhoneSimulator' ">`
4. In ognuna delle compilazioni che usano il simulatore, eliminare o impostare come commento la proprietà seguente: `<CodesignEntitlements>Entitlements.plist</CodesignEntitlements>`
5. Ricaricare il progetto e dovrebbe essere in grado di distribuire al simulatore.

### <a name="next-steps"></a>Passaggi successivi
Per ulteriore assistenza, contattare il supporto tecnico o se il problema rimane anche dopo che usano le informazioni sopra riportate, vedi [le opzioni di supporto sono disponibili per Xamarin?](~/cross-platform/troubleshooting/support-options.md) per informazioni sulle opzioni di contatto, suggerimenti, nonché come archiviare un nuovo bug se necessario. 
