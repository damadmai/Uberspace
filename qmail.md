# [&larr; Übersicht](./README.md)

# :mailbox: [.qmail](https://wiki.uberspace.de/mail:dotqmail) - [Zustellregeln](http://www.qmail.org/man/man5/dot-qmail.html)

## :mailbox_with_mail: Benachrichtigung bei eingehender E-Mail
Für eine Benachrichtigung im Sinne von "Check mal die Mailbox der primären Adresse" reicht folgender Inhalt der `.qmail`
```
./Maildir/
|echo | mail -s "please check inbox" jemand@example.com
```
Die erste Zeile stellt die betreffende Mail ins primäre Maildir zu. Hier könnte auch ein anderes Maildir, eine [Weiterleitung](https://wiki.uberspace.de/mail:dotqmail#moeglichkeiten_mit_qmail-dateien) oder [`|/usr/bin/vdeliver`](https://wiki.uberspace.de/mail:vmailmgr) stehen, falls ein anderes Postfach verwendet wird.

`echo` in der zweiten Zeile sorgt einfach nur dafür, dass der Inhalt der eingetroffenen E-Mail für die Benachrichtigung an jemand@example.com komplett verworfen wird. Danke an [Jonas](https://jonaspasche.com/) für diesen Tipp.

### :envelope: Benachrichtigung inklusive [Header](https://de.wikipedia.org/wiki/Header_%28E-Mail%29) wie `From:` und `Subject:`
Wenn der Betreff sowie Absender, jedoch nicht der Inhalt der E-Mail weitergeleitet werden soll, hilft folgendes.
```
|/usr/bin/reformime -u | /usr/bin/reformail -U "Content-Type:" -X "Content-Type:" -X "Date:" -X "From:" -X "Subject:" -X "X-Sender:" -X "User-Agent:" | iconv -f utf-8 -t ISO88591 | mutt -e "set send_charset=utf-8; set from='$SENDER'; set user_agent=no; set copy=no" "jemand@example.com" -s "damadmai"
```
[`/usr/bin/reformime`](http://www.courier-mta.org/reformime.html)` -u` dekodiert MIME für Umlaute usw.

[`/usr/bin/reformail`](http://www.courier-mta.org/reformail.html)` -U "Content-Type:" -X "Content-Type:" -X "Date:" -X "From:" -X "Subject:" -X "X-Sender:" -X "User-Agent:"` filtert die gewünschten Header-Zeilen heraus.  
`-U "Content-Type:"` sorgt dafür, dass nur der letzte derartige Header angezeigt wird.

`iconv -f utf-8 -t ISO88591` konvertiert Zeichenkodierung, sodass Umlaute korrekt dargestellt werden.

`mutt` versendet die Benachrichtigung, wobei als Absender der ursprüngliche beibehalten wird. `"damadmai"` am Ende ist der Betreff der Benachrichtigung; dafür könnte auch die primäre E-Mail-Adresse bzw. [`$RECIPIENT`](http://www.qmail.org/man/man8/qmail-command.html) eingetragen werden.

Das kann insofern dahingehend praktisch sein, um E-Mails jemandem weiterzuleiten, dem es jedoch *nicht möglich sein soll, den Body der E-Mail zu lesen* um z.B. Passwort-Zurücksetz-URLs anzuklicken. :wink:

 :inbox_tray: &rarr; "Von mehreren Personen gelesenes E-Mail Postfach"

## Lizenz
[MIT](./LICENSE)
