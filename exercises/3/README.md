# Beleg 3

Bearbeitsungszeit: 2 Wochen



## Kontext

Aktuell sollten sie einen Dienst haben, welche Songs und Songlisten verwalten kann. Die Daten werden in einer Datenbank gespeichert. Der Dienst besitzt Integrationstests und hat ggf. weitere Tests. 

In Beleg 3 erweitern wir den Dienst um so genannte Nutzer, die sich registrieren, anmelden und Songlisten erstellen und verwalten können.



## Aufgaben

**User Endpoint:**

1. Erstellen sie die Datenbank Tabelle und entsprechendes Java-Objekt für einen User. Ein User hat folgende Eigenschaften⭐:

   ```
   id: (Entweder numerisch oder Hash-String)
   firstName: der Vorname
   lastName: der Nachname
   userName: der "Login" Name, welcher Unique für einen Nutzer ist
   password: das Passwort vom User
   ```

   Fügen sie in der Tabelle folgenden User hinzu:

   ```
   firstName: Max
   lastName: Muster
   userName: Maxinator
   password: Maxinator123
   ```

2. Implementieren sie folgende GET-Anfrage⭐:

   ```http
   GET /TEAMNAME/rest/users
   Accept: application/json
   ```

   welcher alle User zurückgibt. Der Endpoint muss nur JSON zurückgeben. Das Passwort sollte nicht angezeigt werden.

3. (Bonus) Implementieren sie folgende POST-Anfrage⭐:

   ```http
   POST /TEAMNAME/rest/users/signup
   Content-Type: application/json
   ```

   mit folgenden JSON Payload:

   ```
   {
    "firstName": "Max",
    "lastName": "Muster",
    "userName": "MaxiMuster123",
    "password": "Passwort123",
    }
   ```

   Mithilfe von diesen Endpunkt können sich Nutzer registrieren. Der gegebene Username darf nicht existieren.

   - Das Passwort vom Nutzer ist nicht als klartext in ihrer Datenbank gespeichert. Nutzen sie dazu Werkzeuge wie `bcrypt ` ⭐

   Wenn sich ein Nutzer erfolgreich registrieren konnte, schickt ihr Service einen Token zurück wie z.B.:

   ```
   StatusCode: 200, OK  
   Content-Type: “text/plain” 
   qwertyuiiooxd1a245 # Payload enthält nur diesen String (Beispiel eines generierten, zufälligen, „nicht allzu langen“ Tokens) 
   ```

   Mit dem Token kann sich der Nutzer Authentifizieren.

4. Implementieren sie folgende POST-Anfrage⭐:

   ```http
   POST /TEAMNAME/rest/users/signin
   Content-Type: application/json
   ```

   mit folgenden JSON Payload:

   ```
   {
    "userName": "MaxiMuster123",
    "password": "Passwort123",
    }
   ```

   Mithilfe von diesen Endpunkt können sich Nutzer anmelden. Wenn sich ein Nutzer erfolgreich registrieren konnte, schickt ihr Service den User-Token zurück:

   ```
   StatusCode: 200, OK  
   Content-Type: “text/plain” 
   qwertyuiiooxd1a245 # Payload enthält nur diesen String (Beispiel eines generierten, zufälligen, „nicht allzu langen“ Tokens) 
   ```

5. Implementieren Sie Tests für alle User-Endpunkte.
   - Tests für GET⭐
   - Tests für signin⭐
   - Tests für signup⭐



**Erweiterung von SongLists Endpoint:**

1. Die SongList soll um folgende Eigenschaften erweitert werden:

   ```
   isPrivate: ist “private” oder “public”
   ownerName: der Username vom Besitzer der Liste
   ```
   
   - (Bonus): In der Regel ist es hilfreich das Schema der Datenbanktabellen zu versionieren um z.B. bei einem Rollback das Schema zurückzusetzen. Dazu können sie Werkzeuge wie `liquitbase` nutzen.
   
2. Modizifieren sie folgende GET-Anfrage⭐:

   ```http
   GET /TEAMNAME/rest/songLists/SONGID
   Accept: application/json oder application/xml
   Authorization: USERTOKEN
   ```

   welcher die Songliste mit der ID `SONGID ` nur unter folgende Bedingung zurückgibt:

   - der Usertoken gehört dem Besitzer der Songlist
   - die Songliste ist öffentlich

3. Modifizieren sie folgende GET-Anfrage⭐:

   ```http
   GET /TEAMNAME/rest/songLists/
   Accept: application/json oder application/xml
   Authorization: USERTOKEN
   ```

   welcher alle eigenen und öffentlichen Songlisten in JSON oder XML zurückgibt.

4. Modifizieren sie folgende POST-Anfrage⭐:

   ```http
   POST /TEAMNAME/rest/songLists/
   Content-Type: application/json
   Authorization: USERTOKEN
   ```

   mit folgenden modifizierten Beispiel JSON Payload:

   ```
   {
    "name": "SongList123",
    "isPrivate": true,
    "songList": [
       {
          "id": 5,
          "title": "We Built This City",
          "artist": "Starship",
          "label": "Grunt/RCA",
          "released": 1985
       },
       {
          "id": 4,
          "title": "Sussudio",
          "artist": "Phil Collins",
          "label": "Virgin",
          "released": 1985
       }
    ]
    }
   ```

   Die Songliste kann nun entweder als öffentliche oder private Liste erstellt werden. Zusätzlich sollte die Songliste über das Token im Authorization-Header dem Benutzer zugewiesen werden.

5. Implementieren sie folgende Delete-Anfrage⭐:

   ```http
   POST /TEAMNAME/rest/songLists/SONG_ID
   Content-Type: application/json
   Authorization: USERTOKEN
   ```
   
   der die Songlist mit der ID `SONG_ID` nur löscht, wenn der Besitzer die Anfrage gesendet hat.
   
6. Ihre bestehenden Tests wurden entsprechend modifiziert. ⭐

7. Ihr Dienst behandelt inkorrekten Client-Requests entsprechend RFC 2616, Section 10.04: https://tools.ietf.org/html/rfc2616#section-10.4. Beispielsweise sollen HTTP-Methoden, die Ihr Service nicht anbietet mit dem Statuscode 405 beantwortet werden. ⭐



**(Bonus) Dockerisierung der Anwendung**

1. Erstellen Sie ein Dockerfile für Ihre Anwendung. Im Build Prozess soll die Anwendung gebaut werden. Beim Starten des Containers wird ihre Spring Boot Anwendung gestartet. Lesen sie die benötigten Variablen aus den Umgebungsvariablen⭐
   - Beachten Sie dabei die [Best Practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/). 
2. Mit [docker-compose](https://docs.docker.com/compose/) können Sie Ihre lokale Entwicklungsumgebung als mehrere Docker Anwendungen definieren. Erstellen sie eine `docker-compose.yml` in der ihre Dienst und Datenbank als Docker Anwendung spezifiziert sind. Modifizieren sie entsprechend ihre lokale Ausführungsconfiguration um die Datenbank der Docker Anwendung zu nutzen. ⭐
3. Nutzen sie docker-compose für ihre Tests. Definieren sie dazu eine `docker-compose.test.yaml` (siehe https://docs.docker.com/compose/extends/) in der die entsprechenden Variablen mit den Testvariablen überschrieben werden. ⭐
4. Nutzen sie [Multi Stage Builds](https://docs.docker.com/develop/develop-images/multistage-build/) zum optimieren ihrer Images. Dabei können sie einen Stage für "Build", Production" und "Test" erstellen. ⭐

3. Dokumentieren den ihren Build-, Run- und Testprozess mit Docker in der `README.md`.⭐
