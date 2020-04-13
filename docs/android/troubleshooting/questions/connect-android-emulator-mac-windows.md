---
title: È possibile connettersi a emulatori Android in esecuzione in un Mac da una macchina virtuale Windows?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7B6752BB-8E4C-4690-B275-7E425A051F45
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/21/2018
ms.openlocfilehash: 49d1eea60f766f4cb61484a6e441506cf8f046ff
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "78293074"
---
# <a name="is-it-possible-to-connect-to-android-emulators-running-on-a-mac-from-a-windows-vm"></a>È possibile connettersi a emulatori Android in esecuzione in un Mac da una macchina virtuale Windows?

Per connettersi all'emulatore Android in esecuzione su un Mac da una macchina virtuale Windows, attenersi alla seguente procedura:

1. Avviare l'emulatore sul Mac.

2. Uccidi `adb` il server sul Mac:

    ```bash
    adb kill-server
    ```

3. Si noti che l'emulatore è in ascolto su 2 porte TCP sull'interfaccia di rete di loopback:

    ```bash
    lsof -iTCP -sTCP:LISTEN -P | grep 'emulator\|qemu'

    emulator6 94105 macuser   20u  IPv4 0xa8dacfb1d4a1b51f      0t0  TCP localhost:5555 (LISTEN)
    emulator6 94105 macuser   21u  IPv4 0xa8dacfb1d845a51f      0t0  TCP localhost:5554 (LISTEN)
    ```

    La porta dispari è quella utilizzata `adb`per connettersi a . Vedere [https://developer.android.com/tools/devices/emulator.html#emulatornetworking](https://developer.android.com/tools/devices/emulator.html#emulatornetworking)anche .

4. _Opzione 1:_ utilizzare `nc` per inoltrare i pacchetti TCP in ingresso ricevuti esternamente sulla porta 5555 (o su qualsiasi altra porta desiderata) alla porta dispari sull'interfaccia loopback (**127.0.0.1 5555** in questo esempio) e per inoltrare i pacchetti in uscita indietro l'altro modo:

    ```bash
    cd /tmp
    mkfifo backpipe
    nc -kl 5555 0<backpipe | nc 127.0.0.1 5555 > backpipe
    ```

    Finché i `nc` comandi rimangono in esecuzione in una finestra del terminale, i pacchetti verranno inoltrati come previsto. È possibile digitare Control-C nella `nc` finestra Terminale per chiudere i comandi al termine dell'utilizzo dell'emulatore.

    (L'opzione 1 è in genere più semplice dell'opzione 2, soprattutto se **Preferenze di Sistema > Sicurezza & Privacy > Firewall** è attivato.)

    _Opzione 2_ `pfctl` : Utilizzare per reindirizzare i pacchetti TCP dalla porta `5555` (o da qualsiasi altra porta desiderata) sull'interfaccia Shared [Networking](https://kb.parallels.com/en/4948) alla porta dispari sull'interfaccia di loopback (in`127.0.0.1:5555` questo esempio):

    ```bash
    sed '/rdr-anchor/a rdr pass on vmnet8 inet proto tcp from any to any port 5555 -> 127.0.0.1 port 5555' /etc/pf.conf | sudo pfctl -ef -
    ```

    Questo comando imposta l'inoltro delle porte utilizzando il `pf packet filter` servizio di sistema. Le interruzioni di riga sono importanti. Assicurarsi di mantenerli intatti durante l'operazione di copia e incolla. Sarà inoltre necessario regolare il nome dell'interfaccia da *vmnet8* se si utilizza Parallels. `vmnet8`è il nome del *dispositivo NAT* speciale per la modalità *Shared Networking* in VMWare Fusion. L'interfaccia di rete appropriata in Parallels è probabilmente [vnic0](https://download.parallels.com/doc/psbm/en/Parallels_Server_Bare_Metal_Users_Guide/29258.htm).

5. Connettersi all'emulatore dal computer Windows:

    ```cmd
    C:\> adb connect ip-address-of-the-mac:5555
    ```

    Sostituire "ip-address-of-the-mac" con l'indirizzo IP del Mac, ad esempio come elencato da `ifconfig vmnet8 | grep 'inet '`. Se necessario, `5555` sostituire con l'altra porta desiderata dal passaggio 4\. (Nota: un modo per ottenere `adb` l'accesso dalla riga di comando è tramite [**Strumenti > prompt dei comandi di Android > Android Adb**](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat) in Visual Studio.)

### <a name="alternate-technique-using-ssh"></a>Tecnica alternativa con`ssh`

Se hai abilitato _l'accesso remoto_ sul `ssh` Mac, puoi utilizzare l'inoltro delle porte per connetterti all'emulatore.

1. Installare un client SSH in Windows. Un'opzione consiste nell'installare [Git per Windows](https://git-for-windows.github.io/). Il `ssh` comando sarà quindi disponibile nel prompt dei comandi **Git Bash.**

2. Seguire i passaggi da 1 a 3 `adb` dall'alto per avviare l'emulatore, terminare il server sul Mac e identificare le porte dell'emulatore.

3. Esegui `ssh` su Windows per configurare l'inoltro delle`localhost:15555` porte bidirezionale tra una porta locale in Windows (in questo`127.0.0.1:5555` esempio) e la porta dell'emulatore con numeri dispari sull'interfaccia di loopback del Mac (in questo esempio):

    ```cmd
    C:\> ssh -L localhost:15555:127.0.0.1:5555 mac-username@ip-address-of-the-mac
    ```

    Sostituire `mac-username` con il nome `whoami`utente Mac come indicato da . Sostituire `ip-address-of-the-mac` con l'indirizzo IP del Mac.

4. Connettersi all'emulatore utilizzando la porta locale in Windows:

    ```cmd
    C:\> adb connect localhost:15555
    ```

    (Nota: un modo semplice per `adb` ottenere l'accesso dalla riga di comando è tramite [ **Strumenti > prompt dei comandi di Android > Android Adb** in Visual Studio](~/cross-platform/troubleshooting/questions/version-logs.md#adb-logcat).)

Un piccolo avvertenza: `5555` se si utilizza `adb` la porta per la porta locale, si potrebbe pensare che l'emulatore è in esecuzione localmente su Windows. Ciò non causa problemi in Visual Studio, ma in Visual Studio per Mac causa l'uscita dell'app immediatamente dopo l'avvio.

### <a name="alternate-technique-using-adb--h-is-not-yet-supported"></a>L'utilizzo `adb -H` di tecniche alternative non è ancora supportato

In teoria, un altro `adb`approccio potrebbe essere quello di `adb` utilizzare la funzionalità incorporata [https://stackoverflow.com/a/18551325](https://stackoverflow.com/a/18551325)per connettersi a un server in esecuzione su un computer remoto (vedere ad esempio ).
Ma le estensioni IDE Xamarin.Android non forniscono attualmente un modo per configurare tale opzione.

## <a name="contact-information"></a>Informazioni contatto

Questo documento illustra il comportamento corrente a partire da marzo 2016. La tecnica descritta in questo documento non fa parte della suite di test stabile per Xamarin, quindi potrebbe rompersi in futuro.

Se si nota che la tecnica non funziona più, o se si notano altri errori nel [http://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm](https://forums.xamarin.com/discussion/33702/android-emulator-from-host-device-inside-windows-vm)documento, sentitevi liberi di aggiungere alla discussione sul seguente thread del forum: .
Grazie.
