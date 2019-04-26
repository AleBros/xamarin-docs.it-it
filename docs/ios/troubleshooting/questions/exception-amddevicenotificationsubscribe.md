---
title: System. Exception AMDeviceNotificationSubscribe ha restituito...
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 7E4ACC7E-F4FB-46C1-8837-C7FBAAFB2DC7
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/21/2017
ms.openlocfilehash: 4fb0712366422e8810a2db60d40c3b85d9f4cd82
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421944"
---
# <a name="systemexception-amdevicenotificationsubscribe-returned-"></a>System. Exception AMDeviceNotificationSubscribe ha restituito...

> [!IMPORTANT]
> Questo problema è stato risolto nelle versioni recenti di Xamarin. Tuttavia, se il problema si verifica la versione più recente del software, inviare un [nuovo bug](~/cross-platform/troubleshooting/questions/howto-file-bug.md) il completo controllo delle versioni delle informazioni e full output del log di compilazione.


## <a name="fix"></a>Correggi

1.  Terminare il `usbmuxd` elaborare in modo che il sistema verrà riavviato dal:

    ```csharp
    sudo killall -QUIT usbmuxd
    ```

2.  Se il problema che persiste, riavviare il computer Mac.

## <a name="error-message"></a>Messaggio di errore

```csharp
Exception: Exception type: System.Exception
AMDeviceNotificationSubscribe returned: 3892314211
  at Xamarin.MacDev.IPhoneDeviceManager.SubscribeToNotifications () [0x00000] in <filename unknown="">:0
  at Xamarin.MacDev.IPhoneDeviceManager.StartWatching (Boolean useOwnRunloop) [0x00000] in <filename unknown="">:0
  at Mtb.Server.DeviceListener.Start () [0x00000] in <filename unknown="">:0
  at Mtb.Application.MainClass.RunDeviceListener () [0x00000] in <filename unknown="">:0
  at Mtb.Application.MainClass.Main (System.String[] args) [0x00000] in <filename unknown="">:0
```

Questo messaggio può essere visualizzato in una finestra di dialogo di errore quando si avvia Visual Studio per Mac o nel `mtbserver.log` file nell'app Build Host di xamarin. IOS (**Build Host di xamarin. IOS > Visualizza i Log di Host di compilazione**).

Si noti che questo è un problema insolito. Se Visual Studio si verificano problemi durante la connessione all'host di compilazione Mac, sono presenti altri errori che sono più probabile che venga visualizzato nel `mtbserver.log` file.

### <a name="errors-in-systemlog"></a>Errori nel System

Errore alcuni casi i seguenti due messaggi possono anche essere inclusi più volte in `/var/log/system.log`:

```csharp
17:17:11.369 usbmuxd[55040]: dnssd_clientstub ConnectToServer: socket failed 24 Too many open files
17:17:11.369 com.apple.usbmuxd[55040]: _BrowseReplyReceivedCallback Error doing DNSServiceResolve(): -65539
```

## <a name="additional-information"></a>Informazioni aggiuntive

Una probabilità la causa radice dell'errore è che servizi di sistema responsabili per la segnalazione delle informazioni di dispositivo e del simulatore iOS possono in rari casi per OS X entri in uno stato imprevisto. Xamarin non può interagire correttamente con i servizi di sistema in questo stato. Il riavvio del computer si riavvia i servizi di sistema e consente di risolvere il problema.

In base gli errori rilevati durante `system.log` sembra che questo problema potrebbe essere correlato a Bonjour (`mDNSResponder`). Modifica tra diverse reti Wi-Fi potrebbe aumentare le probabilità di raggiungere il problema.

## <a name="references"></a>Riferimenti

*   [Bug 11789 - MonoTouch.MobileDevice.MobileDeviceException: Amdevicenotificationsubscribe ha restituito: 0XE8000063 [NORESPONSE RISOLTI]](https://bugzilla.xamarin.com/show_bug.cgi?id=11789)
