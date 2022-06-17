# Nextcloud, Apache2 und MySQL Installation

* Aktualisiere die Paketlisten & installiere die Updates.

```bash
apt update && apt upgrade -y
```

* Installiere für die weitere Installation benötigte Pakete.

```bash
apt install ca-certificates nano lsb-release gnupg apt-transport-https curl unzip -y
```

Wähle dein Betriebssystem aus.
Solltest du nicht wissen, welches Betriebssystem du verwendest, kannst du dies mit dem Befehl
```bash
cat /etc/issue
```
nachschauen.

{% tabs %}
{% tab title="Debian" %}
* Füge die Paket-Quelle für die PHP7.4 Version hinzu.

```bash
curl -fsSL https://packages.sury.org/php/apt.gpg -o /usr/share/keyrings/php-archive-keyring.gpg
```

```bash
echo "deb [signed-by=/usr/share/keyrings/php-archive-keyring.gpg] https://packages.sury.org/php/ $(lsb_release -sc) main" > /etc/apt/sources.list.d/php.list
```
{% endtab %}

{% tab title="Ubuntu" %}
* Füge die Paket-Quelle für die PHP7.4 Version hinzu.

```bash
apt install software-properties-common -y
```

```bash
add-apt-repository ppa:ondrej/php
```
{% endtab %}
{% endtabs %}

* Aktualisiere noch einmal die Paketlisten
```bash
apt update
```
## Installation von Apache2
* Installiere den Apache2 Webserver
```bash
apt install apache2 -y
```
## Installation von PHP
* Installiere PHP7.4 sowie die PHP-Module
```bash
apt install php7.4 php7.4-zip php7.4-bz2 php7.4-cli php7.4-common php7.4-xsl php7.4-curl php7.4-opcache php7.4-gd php7.4-intl php7.4-json php7.4-mbstring php7.4-mysql php7.4-readline php7.4-xml libapache2-mod-php7.4 -y
```
## Installation von PHPMyAdmin
* Installiere MySQL
```bash
apt install mariadb-server mariadb-client -y
```

Schließe die MySQL Installation ab
{% tabs %}
{% tab title="Debian 10 & Ubuntu" %}
Gebe den Befehl 
```bash
mysql_secure_installation
``` 
ein. Bei der ersten Abfrage des aktuellen Passworts drücke einfach "Enter". Bestätige die nächste Frage bzgl. der Änderung des Root-Passworts mit "Enter". Nun musst du ein Passwort für den Root-Benutzer des MariaDB-Servers vergeben. Während der Eingabe erscheinen keine Zeichen, das ist jedoch normal. Bestätige alle darauffolgenden Fragen (Löschung des anonymen Benutzers, Verbieten des externen Root-Logins aus Sicherheitsgründen, Entfernen der Testdatenbank und Aktualisieren der Rechte) ebenfalls mit "Enter".

{% endtab %}

{% tab title="Debian 11" %}
Gebe den Befehl 
```bash
mysql_secure_installation
```
ein. Bei der ersten Abfrage des aktuellen Passworts drücke einfach "Enter". Gebe bei der anschließenden Frage bzgl. des Wechsels zur Unix-Socket-Authentifizierung "n" ein und drücke die "Enter"-Taste. Bestätige die nächste Frage bzgl. der Änderung des Root-Passworts mit "Enter". Nun musst du ein Passwort für den Root-Benutzer des MariaDB-Servers vergeben. Während der Eingabe erscheinen keine Zeichen, das ist jedoch normal. Bestätige alle darauffolgenden Fragen (Löschung des anonymen Benutzers, Verbieten des externen Root-Logins aus Sicherheitsgründen, Entfernen der Testdatenbank und Aktualisieren der Rechte) ebenfalls mit "Enter".

{% endtab %}
{% endtabs %}

* Wechsel mit dem Befehl
```bash
cd /usr/share
```
in das Verzeichnis wo PhpMyAdmin installiert wird.

* Lade PhpMyAdmin herunter
```bash
wget https://www.phpmyadmin.net/downloads/phpMyAdmin-latest-all-languages.zip -O phpmyadmin.zip
```

* Entpacke das Archiv
```bash
unzip phpmyadmin.zip
```

* Entferne das heruntergeladene Archiv
```bash
rm phpmyadmin.zip
```

Nenne das PhpMyAdmin Verzeichnis um
```bash
mv phpMyAdmin-*-all-languages phpmyadmin
```

Vergebe die benötigten Rechte
```bash
chmod -R 0755 phpmyadmin
```

* Erstelle die Apache Konfigurationsdatei
```bash
echo "Alias /phpmyadmin /usr/share/phpmyadmin

<Directory /usr/share/phpmyadmin>
    Options SymLinksIfOwnerMatch
    DirectoryIndex index.php
</Directory>

# Disallow web access to directories that don't need it
<Directory /usr/share/phpmyadmin/templates>
    Require all denied
</Directory>
<Directory /usr/share/phpmyadmin/libraries>
    Require all denied
</Directory>
<Directory /usr/share/phpmyadmin/setup/lib>
    Require all denied
</Directory>" >> /etc/apache2/conf-available/phpmyadmin.conf
```

* Aktiviere die Apache Konfigurationsdatei
```bash
a2enconf phpmyadmin
```

* Reloade den Apache Service
```bash
systemctl reload apache2
```

* Erstelle das Temporäre Verzeichnis für PhpMyAdmin
```bash 
mkdir /usr/share/phpmyadmin/tmp/
```

* Gebe dem Webnutzer die Berechtigung auf das temporäre Verzeichnis zuzugreifen.
```bash
chown -R www-data:www-data /usr/share/phpmyadmin/tmp/
```

{% tabs %}
{% tab title="Debian 10 & Ubuntu" %}
* Melde dich bei dem MySQL Server an

```bash
mysql -u root
```

* Stelle das Authentifizierungsplugin des Root-Benutzers von UNIX auf die Standardauthentifizierung um.

```bash
UPDATE mysql.user SET plugin = 'mysql_native_password' WHERE user = 'root' AND plugin = 'unix_socket';
```

```bash 
FLUSH PRIVILEGES;
```
{% endtab %}

{% tab title="Debian 11" %}
* Melde dich bei dem MySQL Server an

```bash
mysql -u root
```

* Erstellt ein nutzernamen und Passwort. Bedenke dein username und password hier zu ändern (rot markiert)
```bash
CREATE USER 'username'@'localhost' IDENTIFIED BY 'password';
```

* Dieser Befehl weißt deinem Nutzernamen alle Rechte, die man als Inhaber braucht
```bash
GRANT ALL PRIVILEGES ON *.* TO 'username'@'localhost' WITH GRANT OPTION;
```

```bash 
exit
```
{% endtab %}
{% endtabs %}

## Installation von Nextcloud

* Navigiere hiermit zum path vom Web-Server
```bash 
cd /var/www/html
```

* Lade Nextcloud herunter
```bash 
wget https://download.nextcloud.com/server/releases/latest.tar.bz2
```

* Entpacke Nextcloud
```bash 
tar xfvj latest.tar.bz2
```

* Lösche Nextcloud Archiv
```bash 
rm latest.tar.bz2
```

* Apache2 mod_rewrite aktiviert
```bash 
a2enmod rewrite
```

* Apache2 neustarten
```bash 
systemctl restart apache2
```

* Apache2 Web-Server Besitzerrechte vergeben
```bash 
chown -R www-data:www-data /var/www/html/nextcloud/
```

## Nextcloud Nutzer anlegen

* Navigiere in dein Browser und gebe deine IP-Adresse und anschließend /phpmyadmin ein.

![](https://bilderupload.org/image/a98a79993-screenshot-2022-06-17-173.png)

* Melde dich mit deinen PHPMyAdmin Nutzernamen und Password an, was du vorhin gemacht hast

![](https://bilderupload.org/image/e58680326-screenshot-2022-06-17-173.png)

* Navigiere nun zum Punkt: Benutzerkonten

![](https://bilderupload.org/image/364580680-pupsl.png)

* drückst auf Benutzerkonto hinzufügen

![](https://bilderupload.org/image/d1d680789-pupsanlegen.png)

* nun lege einen Benutzernamen und Password ein für Nextcloud
* und lege wie aufem Bild ein Punkt bei: Erstelle eine Datenbank mit gleichem Namen und gewähre alle Rechte.
* scrolle runter und drücke auf "OK"

![](https://bilderupload.org/image/a7c980993-anlegenfertig.png)

* Nachdem es Erfolgreich angelgt wurde, navigiere in dein Browser und gebe deine IP-Adresse und anschließend /nextcloud ein.

![](https://bilderupload.org/image/903c82182-nextcloud.png)

* Nun Trage oben deinen Wunsch Benutzernamen und Passwort für Nextcloud ein.
* Zu den Unteren Feldern (Datenbank Informationen) deine Datenbank daten ein.
* und Drücke auf: "Installieren"

![](https://bilderupload.org/image/3d8e82457-nextcloudfertig.png)

# Nun ist die Installation von Nextcloud erfolgreich abgeschlossen
