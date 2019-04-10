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
