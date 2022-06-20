# Sinusbot installer

## Automatische Installation

```bash
bash <(wget -O - 'https://raw.githubusercontent.com/SinusBot/installer-linux/master/sinusbot_installer.sh')
```

## Manuelle Installation

* Aktualisiere die Paketlisten & installiere die Updates.

```bash
apt update && apt upgrade -y
```

* Installiere für die weitere Installation benötigte Pakete.

```bash
apt install x11vnc xvfb libxcursor1 ca-certificates bzip2 libnss3 libegl1-mesa x11-xkb-utils libasound2 libpci3 libxslt1.1 libxkbcommon0 libxss1 libglib2.0-0 libxcomposite1 curl screen nano -y
```

{% tabs %}
{% tab title="Musik Funktion" %}
* Benötigte Pakete für die YouTube Wiedergabefunktion

```bash
apt install python -y
```

```bash
wget https://yt-dl.org/downloads/latest/youtube-dl -O /usr/local/bin/youtube-dl
```

```bash
chmod +x /usr/local/bin/youtube-dl
```
{% endtab %}


* Nutzer erstellen, damit man später Sinusbot ausführen kann.

{% tabs %}
{% tab title="Außen ereichbar" %}
* Wenn du dich von außen direkt mit dem "sinusbot" Nutzer anmelden möchtest

```bash
adduser sinusbot
```

{% endtab %}

{% tab title="Außen nicht ereichbar" %}
* Mit diesem Befehl, kannst du dich nur mit Root in den Nutzer "sinusbot" anmelden

```bash
adduser --disabled-login sinusbot
```

{% endtab %}
{% endtabs %}

* Bei den folgenden fragen, kannst du die mit der "Enter" Taste durchklicken und am ende mit "Y/J" Bestätigen

```bash
![](https://bilderupload.org/image/e46758677-sinusbot.png)
```

* Verzeichnis erstellen für Sinusbot

```bash
mkdir -p /opt/sinusbot
```

* Sinusbot Ordner Rechte vergeben

```bash
chown -R sinusbot:sinusbot /opt/sinusbot/
```

* Navigiere zum Sinusbot Ordner

```bash
cd /opt/sinusbot/
```

* Installiere Sinusbot

```bash
wget https://www.sinusbot.com/dl/sinusbot.current.tar.bz2
```

* Entpacke Sinusbot

```bash
tar xjf sinusbot.current.tar.bz2
```

* Lösche die .tar.bz2 Ordner

```bash
rm sinusbot.current.tar.bz2
```

* Kopiere die Konfigurationsdatei

```bash
cp config.ini.dist config.ini
```

## Teamspeak3 Client Installation

* Installiere Teamspeak3 Client

```bash
wget https://files.teamspeak-services.com/releases/client/3.5.3/TeamSpeak3-Client-linux_amd64-3.5.3.run
```

* Teamspeak3 Client rechte vergeben

```bash
chmod +x TeamSpeak3-Client-linux_amd64-3.5.3.run
```

* Teamspeak3 Client starten

```bash
./TeamSpeak3-Client-linux_amd64-3.5.3.run
```

* Drücke danach "Enter" -> "q" und bestätige diese mit "y" und danach "Enter"

```bash
./TeamSpeak3-Client-linux_amd64-3.5.3.run
```

* Erstelle ein Plugin verzeichnis

```bash
mkdir TeamSpeak3-Client-linux_amd64/plugins
```

* Kopiere die Musikbot Plugins in das verzeichnis

```bash
cp plugin/libsoundbot_plugin.so /opt/sinusbot/TeamSpeak3-Client-linux_amd64/plugins/
```

* Gebe sinusbot Startrechte

```bash
chmod +x sinusbot
```

* Melde dich als sinusbot an

```bash
su sinusbot
```

* Navigiere in dein Sinusbot Ordner

```bash
cd /opt/sinusbot/
```
