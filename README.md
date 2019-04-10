# qgm-troyhunt

A simple script to test linuxmuster.net 6.2 password hashes against the NTLM database of leaked passwords from Troy Hunt.

## Hintergrund 
Die Passwörter, die im Schulnetz mit linuxmuster.net 6.2 verwendet werden, sind nicht im Klartext gespeichert – 
man  kann die Passwörter also nicht sehen. Stattdessen wird ein sogenannter „Hash-Wert“ des Passworts gespeichert, 
der aus dem bei der Anmeldung eingegebenen Passwort errechnet wird - diese Berechnung ist mathematisch nicht umkehrbar. 
Ein Vergleich der Hashwerte genügt, um zu entscheiden, ob das korrekte Passwort eingegeben wurde. Diese Hashwerte sind 
im System des Schulservers im Attribut ``sambaLMPassword`` gespeichert.

```
smbldap-usershow userid
dn: uid=usreid,ou=accounts,dc=base,dc=dn
cn: Vorname Nachname
uidNumber: 11834
sambaPrimaryGroupSID: S-1-5-21-555555267-1141443654-0000000053-21001
sambaAcctFlags: [UX]
gecos: Vorname Nachname
sambaPwdLastSet: 1346555555
mail: nix@irgendwo.de
userPassword: {SSHA}O/1Xx5eaaV4J8B[...]uTA==
sophomorixstatus: U
displayName: Frank Schiebel
sambaLMPassword: D0A1FC635D158F36F911111111113FAE
uid: userid
[...]
```

## Was das Skript tut

Der Sicherheitsforscher Troy Hunt hat eine Datenbank dieser kompromittierten Passwort-Hashes zusammengestellt (eine ca. 20GB große Textdatei). Wenn man Zugriff auf die Passworthashes eines Systems hat, kann mal also durch eine Suche in dieser Datei herausfinden, ob das ursprünglich Passwort in einem solchen Datenleak in der Vergangenheit enthalten war. 
Die Datenbank sieht ausschnittsweise so aus - zuerst steht der „geknackte“ Passworthash, dann durch einen Doppelpunkt getrennt wie oft dieses Passwort in den gestohlenen Datenbanken aufgetaucht ist. Die 6. Zeile ist beispielsweise der Hash des Passworts „12345678“, dieses wurde 2889079 mal gefunden.

```
32ED87BDB5FDC5E9CBA88547376818D4:23174662
C22B315C040AE6E0EFEE3518D830362B:7671364
2D20D252A479F485CDF5E171D93985BF:3810555
8846F7EAEE8FB117AD06BDD830B7586C:3645804
2D7F1A5A61D3A96FB5159B5EEF17ADC6:3093220
259745CB123A52AA2E693AAACCA2DB52:2889079
F9E37E83B83C47A93C2F09F66408631B:2834058
328727B81CA05805A68EF26ACB252039:2484157
5835048CE94AD0564E29A924A03510EF:2401761
7A21990FCD3D759941E45C490F143D5F:2333232
[...]
[...]
7AF54348AC65015CEFC9D27F6E85FD71:18
F03AAFBB8BFDB7B2A325AF1BA646DC97:13
E0DF0C9678DF3BA9B4FD7C04CA4B9940:11
9A82AEA838E8CEDDD3EDA5C8E556A437:7
884DE508B222F2B657E622B83346C7FF:4
0380973B5460025CE6BFDD3A0E1945D7:1
```

Das Skript prüft, ob ein Passworthash aus dem Schulnetz in dieser Datenbank auftaucht. Das Ergebnis muss interpretiert und erklärt werden.

## Interpretation

Wenn ein Passworthash in der Datenbank von Troy Hunt gefunden wird, kann das zwei Ursachen haben: 

- Das Passwort war sehr schwach (geheim, 12345678, o.ä.). Diese Passwörter sind alle in der Datenbank enthalten, und zwar mit einem sehr häufigen Vorkommen. Wenn ich den Hash ihres Passworts im Schulnetz in diesem Fall in der Datenbank gefunden habe, ist ihr Passwort sehr wahrscheinlich nicht in der Datenbank, weil ein anderes Ihrer Konten gehackt wurde, sondern weil Sie einfach ein sehr schlechtes Passwort gewählt haben.
- Wenn ihr Passwort mit einer sehr niedrigen Anzahl an Fundstellen in der Datenbank auftaucht, ist die Wahrscheinlichkeit sehr hoch, dass ein Anbieter, bei dem Sie ein Konto mit diesem Passwort erstellt hatten, nicht gut aufgepasst hat und ihr Passwort damit kompromittiert ist.
