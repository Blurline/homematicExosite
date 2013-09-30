Implementierung von exosite.com API in die homematic

####Benötigte Addons:
#####CCU1:
[Telnet](http://www.homematic-inside.de/software/addons/item/telnet-dienst) -> mit Telnet ein Passwort für den FTP Zugang auf der CCU einrichten

Telnet Session (Windows) öffnen:

*   Start
*   Eingabeaufforderung
*   `telnet`
*   `open 192.168.X.X`
*   `root`
*   `passwd`
*   dein Passwort
*   dein Passwort

[FTP](http://www.homematic-inside.de/software/addons/item/ftp) -> Installieren

[Filezilla](https://filezilla-project.org/) -> Ordner aus dem GIT als Zip herunterladen und nach */usr/local/addons/* kopieren

*   Server:192.168.X.XXX
*   User:root
*   Passwort:dein Passwort was beim Telnet gesetzt wurde

#####CCU2:
[Filezilla](https://filezilla-project.org/) -> Ordner aus dem GIT als Zip herunterladen und nach */usr/local/addons/* kopieren

*   Server: sftp://192.168.X.XX
*   User:root
*   Passwort: MuZhlo9n%8!G
*   Port: 22

#####CCU1/CCU2
[exosite](https://portals.exosite.com/pricing) -> Community Account

[CUx-Daemon](http://www.homematic-inside.de/software/cuxdaemon) -> Performance schonender Aufruf

*   homematic -> Einstellungen -> Systemsteuerung -> Zusatzsoftware
*   Cux-Damon -> Einstellen
*   Geräte
*   CUxD Gerätetyp -> (28)System) 

![CuxD](https://github.com/nleutner/homematicWeather/blob/develop/addons/homematicWeather/doc/images/Cux%20Exec.jpg?raw=true)

*   homematic -> Posteingang

![homematic](https://raw.github.com/nleutner/homematicWeather/develop/addons/homematicWeather/doc/images/Cux%20CCU.gif)



###addons/homematicExosite





####config.tcl
Diese Datei ist die einzige die angepasst werden muss.

 Variabel                 |Beschreibung                                                                |
:-------------------------|:---------------------------------------------------------------------------|
cikDevice                 |cik ID des Devices in dem die Geräte Wert übertragen werden sollen          |
cikSystemvariables        |cik ID des Devices in dem dieSystemvariabel Werte laufen sollen             |
cikCCU                    |cik ID des Devices in dem der CCU load laufen sollen                        |
cikCUxD                   |cik ID des Devices in dem die Werte ON_TIME_24H laufen sollen               |




####exositeCCU.tcl
Diese Programm holt sich den aktuellen load der CCU und übergibt ihn an exosite.


#####Systemvariabeln
 Name                     | Variablentyp| Werte|Maßeinheit
:-------------------------|:------------|:-----|:-------
                          |             |      |

#####exositeAlias
Data Source Name          |Data Source Format   |Unit    |Alias      |Data Source Calculation                                                               
:-------------------------|:--------------------|:-------|:----------|:--------------------------------------------|
CCU load                  |float                |%       |load       |Multiply 100


#####Aufruf im homematic Programm:
```
dom.GetObject("CUxD.CUX2801001:1.CMD_EXEC").State("cd /usr/local/addons/homematicExosite && tclsh exositeCCU.tcl");
```





####exositeCUxd.tcl
Diese Programm holt sich nur den Wert aus TIME_ON_24H des cuxd Gerät 90 State(2) und übergibt ihn an exosite.

#####Systemvariabeln
 Name                     | Variablentyp| Werte|Maßeinheit
:-------------------------|:------------|:-----|:-------
                          |             |      |

#####exositeAlias
Data Source Name          |Data Source Format   |Unit    |Alias      |Data Source Calculation                                                               
:-------------------------|:--------------------|:-------|:----------|:--------------------------------------------|
CCU load                  |float                |       |load       |Multiply 100

#####Aufruf im homematic Programm:
```
dom.GetObject("CUxD.CUX2801001:1.CMD_EXEC").State("cd /usr/local/addons/homematicExosite && tclshexositeCuxd.tcl");
```





####exositeDevices.tcl
Der Unterschied zu forecast10day ist, dass das bereitgestellte xml kleiner ist.

#####Systemvariabeln
 Name                     | Variablentyp| Werte|Maßeinheit
:-------------------------|:------------|:-----|:-------
                          |             |      |

#####exositeAlias
Data Source Name        |Data Source Format|Unit|Alias                        |Data Source Calculation                                                               
:-----------------------|:-----------------|:- -|:----------------------------|:---------------------------------
Helligkeit Wetterstation|integer           |%   |2935BRIGHTNESS               |
Gerätename              |integer           |    |Channel ID LOWBAT            |
Gerätename              |float             |%   |Channel ID LEVEL             |
Gerätename              |integer           |    |Channel ID STATE             |
Gerätename              |integer           |C   |Channel ID SETPOINT          |
Gerätename              |integer           |%   |Channel ID BRIGHTNESS        |
Gerätename              |integer           |%   |Channel ID HUMIDITY          |
Gerätename              |integer           |%   |Channel ID VALVE_STATE       |
Gerätename              |integer           |C   |Channel ID TEMPERATURE       |
Gerätename              |integer           |    |Channel ID MOTION            |
Gerätename              |integer           |kmh |Channel ID WIND_SPEED        |
Gerätename              |integer           |    |Channel ID RAINING           |
Gerätename              |integer           |    |Channel ID SUNSHINEDURATION  |
Gerätename              |integer           |C   |Channel ID SET_TEMPERATURE   |
Gerätename              |integer           |C   |Channel ID ACTUAL_TEMPERATURE|


#####Aufruf im homematic Programm:
```
dom.GetObject("CUxD.CUX2801001:1.CMD_EXEC").State("cd /usr/local/addons/homematicExosite && tclsh exositeDevices.tcl");
```





####exositeSystemvariables.tcl
Dieses Programm ruft die Wetterprognose für die nächsten 10 Tage auf und schreibt in die Systemvariabel Wetterprognose

In die anderen Systemvariabeln werden die jeweiligen Werte direkt geschrieben.

#####Systemvariabeln
 Name                     | Variablentyp| Werte|Maßeinheit
:-------------------------|:------------|:-----|:-------
                          |             |      |

#####exositeAlias
Data Source Name        |Data Source Format|Unit|Alias                        |Data Source Calculation                                                               
:-----------------------|:-----------------|:- -|:----------------------------|:---------------------------------
Anwesenheit             |integer           |    |5987                         |                                  
Systemvariabelname      |float             |    |Systemvariabel ID            |                                  

#####Aufruf im homematic Programm:
```
dom.GetObject("CUxD.CUX2801001:1.CMD_EXEC").State("cd /usr/local/addons/homematicExosite && tclsh exositeSystemvariabels.tcl");
```
