# SPRINT 5

### Monitoratge i Logs

Els logs són registres que recullen informació sobre les activitats d’un sistema. Serveixen per diagnosticar problemes, monitoritzar el rendiment, auditar la seguretat i ajudar en el desenvolupament d'aplicacions.

### Rotació i Gestió dels Logs

Els logs es guarden principalment a la carpeta `/var/log`, on cada servei té el seu propi fitxer. Són clau per controlar el sistema, detectar errors i possibles amenaces. A més, segueixen una política de rotació per evitar que ocupin massa espai, mantenint un historial dels registres antics.
![imagen](<img/Imatge enganxada (149).png>)

Per definir una rotació específica d’un log, cal accedir al directori `/etc/logrotate.d/`, on es poden crear o modificar regles personalitzades per a cada servei o aplicació.

![imagen](<img/Imatge enganxada (150).png>)

### Analització de logs

Els logs es poden analitzar amb comandes com cat, però una eina més avançada és journalctl, que permet filtrar esdeveniments per data i hora amb opcions com --since i --until, per visualitzar només els registres d’un període concret.

`journalctl --since "11:00" --until "12:00"`

![imagen](<img/Imatge enganxada (153).png>)

Per visualitzar els logs vinculats a un dispositiu concret, com per exemple un disc, es pot fer servir la comanda:
`journalctl /dev/sda`

![imagen](<img/Imatge enganxada (154).png>)


### Logs personalitzats

Els logs personalitzats permeten adaptar el comportament dels registres del sistema segons les necessitats. Es poden modificar filtres, destinacions i alertes editant fitxers com `/etc/rsyslog.d/50-default.conf`.

![imagen](<img/Imatge enganxada (155).png>)

- S'envien missatges amb logger usant la prioritat mail.alert.

- Aquests missatges es registren als fitxers /var/log/mail.log i /var/log/mail.err.

- S'observen els missatges "Alerta de mail!clear!" i "Alerta de error" dins dels fitxers corresponents.

![imagen](<img/Imatge enganxada (151).png>)


- Es creen missatges amb logger usant diferents prioritats: auth.crit i cron.crit.

- Els missatges són "prova victor" i es mostren per pantalla amb l'opció -s.

![imagen](<img/Imatge enganxada (152).png>)


### Servidor d'Actualitzacions 

Un Servidor d'Actualitzacions és una eina centralitzada que permet gestionar les actualitzacions de diversos equips des d’un sol punt.

Es configuren per connectar-se al servidor local, que conté una còpia dels paquets mitjançant eines com apt-mirror i apache2.

Quan els equips clients executen apt update, contacten amb aquest servidor, que actua com a repositori intern. Això facilita una gestió més controlada i segura, especialment útil en entorns corporatius o xarxes amb accés limitat a Internet.

Avantatges principals:

    Centralització i uniformitat: Tots els equips reben les mateixes versions de programari, cosa que assegura coherència i facilita la compatibilitat.

    Reducció del tràfic extern: Només cal descarregar una vegada els paquets al servidor, estalviant ample de banda.

    Control i seguretat: Es poden validar les actualitzacions abans de distribuir-les i limitar l’accés només a dispositius autoritzats.

Inconvenients:

    Dependència del servidor: Si cau el servidor, cap client podrà actualitzar-se.

    Més manteniment: Cal invertir temps i recursos a mantenir el servidor actualitzat i segur.

    Implementació inicial complexa: Cal configurar tant el mirall de paquets com el servidor web perquè funcioni correctament.

### Configuració del Servidor


Per començar la configuració del servidor del servidor, primer s’actualitza la llista de paquets amb:

`sudo apt update`

Després, s’instal·la el servidor web Apache2, que servirà els paquets als clients:

`sudo apt install apache2`

![imagen](<img/Imatge enganxada (156).png>)

Tot seguit, s’instal·la el paquet apt-mirror, una eina que permet crear una còpia local dels repositoris APT, evitant així la necessitat d’accedir a Internet per descarregar paquets:

`sudo apt install apt-mirror`


![imagen](<img/Imatge enganxada (157).png>)

Un cop instal·lat, cal editar el fitxer de configuració:

`sudo nano /etc/apt/mirror.list`

En aquest fitxer es desactiven (comentant-les) les línies predeterminades i s’afegeixen les línies necessàries per especificar quins repositoris es volen sincronitzar localment.

![imagen](<img/Imatge enganxada (158).png>)

Un cop configurat el fitxer mirror.list, s’executa la comanda següent amb permisos d’administrador per iniciar la descàrrega dels paquets:

`sudo apt-mirror`

![imagen](<img/Imatge enganxada (159).png>)

![imagen](<img/Imatge enganxada (160).png>)

Per fer que aquests paquets siguin accessibles als clients mitjançant el servidor web Apache2, es crea un enllaç simbòlic cap al directori de descàrrega (per exemple, un repositori de Google) dins de /var/www/html:

![imagen](<img/Imatge enganxada (161).png>)

### Configuració dels Clients

S’afegeix la clau de signatura pública de Google, que permet verificar l'autenticitat dels paquets descarregats del repositori:

`wget -q -O - https://dl.google.com/linux/linux_signing_key.pub | sudo apt-key add -`

![imagen](<img/Imatge enganxada (162).png>)


![imagen](<img/Imatge enganxada (163).png>)

Aquesta línia afegeix un repositori personalitzat per a descarregar Google Chrome, però redireccionat a través del servidor d’actualitzacions local amb IP 10.0.2.9. Això vol dir que el client ja no es connecta directament a Google, sinó que descarrega els paquets des del servidor local, on s’ha fet un mirall (mirror) d’aquest repositori.


### Auditoria de Seguretat per a Linux

Lynis és una eina gratuïta d'auditoria de seguretat per a sistemes Linux i Unix. Analitza la configuració del sistema, serveis, aplicacions i altres components com firewalls, bases de dades o la xarxa per detectar vulnerabilitats. És compatible amb moltes distribucions (Ubuntu, Debian, Fedora, etc.) i ofereix recomanacions per reforçar la seguretat. Funciona sota llicència GNU GPL v3.

### Instal·lació de Lynis

Utilitza la comanda següent per instal·lar el programari:

`sudo apt install lynis -ysudo apt install lynis -`

![imagen](<img/Imatge enganxada (164).png>)

Executa:

`sudo lynis audit system`

![imagen](<img/Imatge enganxada (165).png>)

A la part inferior del resultat, es mostra l'índex de seguretat; aquest valor augmenta a mesura que es resolen problemes i es millora la configuració del sistema.

![imagen](<img/Imatge enganxada (166).png>)


Escaneig Ràpid:

Per fer un escaneig ràpid, utilitza la comanda:

`lyins -Q`

![imagen](<img/Imatge enganxada (167).png>)

Això et permetrà saber quins aspectes del sistema cal millorar o completar.

![imagen](<img/Imatge enganxada (168).png>)

### Paquets Necessaris

Per solucionar la manca de paquets, primer actualitza la llista de repositoris i després instal·la els paquets següents:

`sudo apt update && sudo apt install -y apt-listchanges needrestart fail2ban`

![imagen](<img/Imatge enganxada (169).png>)


 Contingut dels paquets:

    cryptsetup: Inclou les eines principals per configurar i utilitzar LUKS. També integra funcionalitats per treballar amb altres sistemes de xifratge com dm-crypt.

    cryptsetup-bin: Proporciona les utilitats binàries essencials per fer funcionar cryptsetup.

`sudo apt install cryptsetup cryptsetup-bin`

![imagen](<img/Imatge enganxada (170).png>)

 Contingut dels paquets:

    cryptsetup: Inclou les eines principals per configurar i utilitzar LUKS. També integra funcionalitats per treballar amb altres sistemes de xifratge com dm-crypt.

    cryptsetup-bin: Proporciona les utilitats binàries essencials per fer funcionar cryptsetup.

`sudo apt install libpam-tmpdir`

![imagen](<img/Imatge enganxada (172).png>)

El paquet `libpam-tmpdir` modifica el comportament dels directoris temporals (/tmp i /var/tmp) per usuari, fent que cada sessió utilitzi un directori temporal propi i aïllat.


`sudo apt install gcc`

![imagen](<img/Imatge enganxada (171).png>)

Gcc és especialment útil en entorns de desenvolupament i en sistemes on es necessita compilar codi personalitzat o ajustar aplicacions al sistema concret.

`sudo apt install clamav clamav-daemon`

![imagen](<img/Imatge enganxada (173).png>)

ClamAV és un antivirus de codi lliure dissenyat per detectar programari maliciós en sistemes Linux. El component freshclam s'encarrega de mantenir actualitzades les bases de dades de definicions de virus per garantir una detecció efectiva.


