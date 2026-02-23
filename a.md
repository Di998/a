Potrebno je da analizirate sistemske logove kako biste identifikovali potencijalne sigurnosne incidente, pratili aktivnosti korisnika i izvještavali o svakom neobičnom ponašanju. Ova vježba testiraće vašu sposobnost interpretacije podataka iz logova, identifikacije sigurnosnih prijetnji i pružanja jasnih nalaza. Analizirajte **auth.txt** i **ddos.txt** fajlove koji se nalaze na desktop-u računara i obavite zadatke koji su navedeni. Vaš cilj je da demonstrirate svoju stručnost u analizi logova, razumijevanje sigurnosnih pojmova i sposobnost efektivnog dokumentovanja vaših nalaza.  
    **Napomena:** Zadaci se rade preko PowerShell-a. Kada identifikujete ono što je traženo, sačuvajte rezultat u `.txt` formatu na računaru, u formatu: `ZadatakX_Ime_Prezime.txt`.
    

### Zadatak 1 (auth.txt)

Identifikujte sve uspjesne pokušaje prijave (Successful login attempt) iz auth.txt fajla. Sačuvajte rezultat u fajl `Zadatak1_Ime_Prezime.txt`.

**PowerShell komanda:**

```powershell
Get-Content "$env:USERPROFILE\Desktop\auth.txt" |
Where-Object { $_ -like "*Successful login*" } |
Out-File "$env:USERPROFILE\Desktop\Zadatak1_ime_prezime.txt"
```

**Odgovor:**
```
2024-06-27 07:38:47 INFO Successful login for user 'psmith' from IP 192.168.1.47
2024-06-27 08:18:40 INFO Successful login for user 'mjohnson' from IP 192.168.1.51
2024-06-27 08:23:18 INFO Successful login for user 'abrown' from IP 192.168.1.54
2024-06-27 08:38:56 INFO Successful login for user 'mjohnson' from IP 192.168.1.21
2024-06-27 08:41:16 INFO Successful login for user 'dlee' from IP 192.168.1.35
2024-06-27 08:45:16 INFO Successful login for user 'abrown' from IP 192.168.1.51
2024-06-27 08:51:40 INFO Successful login for user 'jkim' from IP 192.168.1.32
2024-06-27 08:57:47 INFO Successful login for user 'psmith' from IP 192.168.1.12
2024-06-27 09:04:25 INFO Successful login for user 'kwhite' from IP 192.168.1.40
2024-06-27 09:06:40 INFO Successful login for user 'jkim' from IP 192.168.1.51
2024-06-27 09:08:50 INFO Successful login for user 'admin' from IP 192.168.1.58
2024-06-27 09:18:32 INFO Successful login for user 'mjohnson' from IP 192.168.1.20
2024-06-27 09:26:22 INFO Successful login for user 'dlee' from IP 192.168.1.59
2024-06-27 09:32:52 INFO Successful login for user 'jkim' from IP 192.168.1.58
2024-06-27 09:34:25 INFO Successful login for user 'dlee' from IP 192.168.1.49
2024-06-27 09:45:39 INFO Successful login for user 'admin' from IP 192.168.1.17
2024-06-27 10:00:20 INFO Successful login for user 'jkim' from IP 192.168.1.43
2024-06-27 10:16:32 INFO Successful login for user 'admin' from IP 192.168.1.53
2024-06-27 10:16:48 INFO Successful login for user 'dlee' from IP 192.168.1.11
2024-06-27 10:22:22 INFO Successful login for user 'tlee' from IP 192.168.1.53
2024-06-27 10:34:07 INFO Successful login for user 'jdoe' from IP 192.168.1.43
2024-06-27 11:23:27 INFO Successful login for user 'mjohnson' from IP 192.168.1.25
```

---

### Zadatak 2 (auth.txt)

**Prebrojte ukupan broj neuspješnih pokušaja prijave (Failed login attempt) u auth.txt i sačuvajte samo broj u `Zadatak2_Ime_Prezime.txt`.**

**PowerShell komanda:**

```powershell
(Get-Content "$env:USERPROFILE\Desktop\auth.txt" | Where-Object { $_ -like "*Failed login attempt*" }).Count | Out-File "$env:USERPROFILE\Desktop\Zadatak2_ime_prezime.txt"
```

Odgovor:

```
60
```

---

### Zadatak 3 (auth.txt)

**Identifikujte korisnike koji imaju najviše neuspješnih pokušaja prijave i izdvojite TOP 3. Sačuvajte rezultat u `Zadatak3_Ime_Prezime.txt`.**

**PowerShell komanda:**

```powershell
Get-Content "$env:USERPROFILE\Desktop\auth.txt" | Where-Object { $_ -like "*Failed login attempt*" } | ForEach-Object { ($_ -split "'")[1] } | Group-Object | Sort-Object Count -Descending | Select-Object -First 3 Name, Count | Out-File "$env:USERPROFILE\Desktop\Zadatak3_ime_prezime.txt"
```

**Odgovor:**

```
Name     Count
----     -----

kwhite       8
mjohnson     8
asmith       7
```

---

### Zadatak 4 (auth.txt)

**Identifikujte IP adresu koja se najviše puta pojavljuje u auth.txt (na osnovu “from IP x.x.x.x”). Sačuvajte rezultat u `Zadatak4_Ime_Prezime.txt`.**

**PowerShell komanda:**

```powershell
Get-Content "$env:USERPROFILE\Desktop\auth.txt" | ForEach-Object { if ($_ -match "from IP ((?:\d{1,3}\.){3}\d{1,3})") { $Matches[1] } } | Group-Object | Sort-Object Count -Descending | Select-Object -First 1 | Select-Object Count, @{Name="IP";Expression={$_.Name}}| Out-File "$env:USERPROFILE\Desktop\Zadatak4_ime_prezime.txt"
```

**Odgovor:**

```
Count IP
----- --
    5 192.168.1.20
```

--- 

### Zadatak 5 (auth.txt)

**Identifikujte IP adrese koje imaju 3 ili više neuspješnih pokušaja prijave (Failed login attempt). Sačuvajte rezultat u `Zadatak5_Ime_Prezime.txt`.**

**PowerShell komanda:**

```powershell
Get-Content "$env:USERPROFILE\Desktop\auth.txt" | Where-Object { $_ -like "*Failed login attempt*" } | ForEach-Object { if ($_ -match "from IP ((?:\d{1,3}\.){3}\d{1,3})") { $Matches[1]}} | Group-Object | Where-Object { $_.Count -ge 3 } | Sort-Object Count -Descending | Select-Object @{Name="IP";Expression={$_.Name}}, Count | Out-File "$env:USERPROFILE\Desktop\Zadatak5_ime_prezime.txt"
```


**Odgovori:**

```
IP           Count
----         -----

192.168.1.20     4
192.168.1.10     3
192.168.1.11     3
192.168.1.15     3
192.168.1.23     3
192.168.1.39     3
```


---

### Zadatak 6 (auth.txt)

**Provjerite da li postoji neuspješan pokušaj prijave za korisnika ‘admin’ i sa koje IP adrese. Sačuvajte rezultat u `Zadatak6_Ime_Prezime.txt`.**

**PowerShell komanda:**

```powershell
Get-Content "$env:USERPROFILE\Desktop\auth.txt" |  Where-Object { $_ -like "*Failed login attempt*" -and $_ -like "*user 'admin'*" } | Out-File "$env:USERPROFILE\Desktop\Zadatak6_Ime_Prezime.txt"
```

**Odgovor:**

```
2024-06-27 07:45:47 WARN Failed login attempt for user 'admin' from IP 192.168.1.32                                                    
2024-06-27 07:53:17 WARN Failed login attempt for user 'admin' from IP 192.168.1.22
2024-06-27 08:43:26 WARN Failed login attempt for user 'admin' from IP 192.168.1.50
2024-06-27 09:42:09 WARN Failed login attempt for user 'admin' from IP 192.168.1.14
2024-06-27 11:11:49 WARN Failed login attempt for user 'admin' from IP 192.168.1.17
2024-06-27 11:23:51 WARN Failed login attempt for user 'admin' from IP 192.168.1.25
```

---

### Zadatak 7 (ddos.txt)

**Identifikujte sve zapise u ddos.txt gdje server vraća HTTP 429 ili HTTP 503. Uklonite duplikate i sortirajte rezultate. Sačuvajte rezultat u `Zadatak7_Ime_Prezime.txt`.**

**PowerShell komanda:**

```powershell
Get-Content "$env:USERPROFILE\Desktop\ddos.txt" |
Where-Object { $_ -match "HTTP 429|HTTP 503" } |
Sort-Object -Unique |
Out-File "$env:USERPROFILE\Desktop\Zadatak7_ime_prezime.txt"
```

**Odgovor:**

```
2024-06-27 10:00:02 WARN HTTP 429 GET /api/orders from 45.67.12.22 ua="python-requests/2.31"

2024-06-27 10:00:09 WARN HTTP 503 GET /api/orders from 45.67.12.99 ua="Mozilla/5.0"

2024-06-27 10:00:51 WARN HTTP 429 GET /api/orders from 45.67.12.41 ua="Go-http-client/1.1"

2024-06-27 10:00:54 WARN HTTP 503 GET /api/orders from 45.67.12.29 ua="Go-http-client/1.1"

2024-06-27 10:00:55 WARN HTTP 429 GET /api/orders from 45.67.12.8 ua="python-requests/2.31"

2024-06-27 10:01:07 WARN HTTP 503 GET /api/orders from 45.67.12.90 ua="Mozilla/5.0"

2024-06-27 10:01:37 WARN HTTP 429 GET /api/orders from 45.67.12.72 ua="curl/7.81.0"

2024-06-27 10:01:38 WARN HTTP 429 GET /login from 45.67.12.88 ua="python-requests/2.31"

2024-06-27 10:01:39 WARN HTTP 503 GET /login from 45.67.12.9 ua="Go-http-client/1.1"

2024-06-27 10:01:43 WARN HTTP 429 GET /login from 45.67.12.46 ua="python-requests/2.31"

2024-06-27 10:01:55 WARN HTTP 503 GET /login from 45.67.12.49 ua="python-requests/2.31"

2024-06-27 10:02:10 WARN HTTP 429 GET /api/orders from 45.67.12.31 ua="Go-http-client/1.1"

2024-06-27 10:02:31 WARN HTTP 429 GET /api/orders from 45.67.12.21 ua="Go-http-client/1.1"

2024-06-27 10:02:39 WARN HTTP 503 GET /api/orders from 45.67.12.74 ua="curl/7.81.0"

2024-06-27 10:02:59 WARN HTTP 503 GET /login from 45.67.12.89 ua="curl/7.81.0"

2024-06-27 10:03:00 WARN HTTP 429 GET /api/orders from 45.67.12.5 ua="curl/7.81.0"

2024-06-27 10:03:38 WARN HTTP 503 GET /api/orders from 45.67.12.58 ua="curl/7.81.0"

2024-06-27 10:03:44 WARN HTTP 503 GET /api/orders from 45.67.12.32 ua="curl/7.81.0"

2024-06-27 10:03:49 WARN HTTP 503 GET /login from 45.67.12.56 ua="curl/7.81.0"

2024-06-27 10:04:02 WARN HTTP 503 GET /login from 45.67.12.73 ua="python-requests/2.31"

2024-06-27 10:04:07 WARN HTTP 429 GET /api/orders from 45.67.12.5 ua="Go-http-client/1.1"

2024-06-27 10:04:11 WARN HTTP 429 GET /login from 45.67.12.3 ua="Go-http-client/1.1"

2024-06-27 10:04:21 WARN HTTP 429 GET /api/orders from 45.67.12.93 ua="curl/7.81.0"

2024-06-27 10:04:22 WARN HTTP 503 GET /api/orders from 45.67.12.8 ua="curl/7.81.0"

2024-06-27 10:04:47 WARN HTTP 503 GET /api/orders from 45.67.12.31 ua="python-requests/2.31"

2024-06-27 10:04:51 WARN HTTP 429 GET /api/orders from 45.67.12.6 ua="curl/7.81.0"

2024-06-27 10:05:01 WARN HTTP 503 GET /api/orders from 45.67.12.72 ua="Mozilla/5.0"

2024-06-27 10:05:04 WARN HTTP 429 GET /login from 45.67.12.60 ua="python-requests/2.31"

2024-06-27 10:05:04 WARN HTTP 503 GET /login from 45.67.12.84 ua="Go-http-client/1.1"

2024-06-27 10:05:16 WARN HTTP 429 GET /api/orders from 45.67.12.49 ua="Mozilla/5.0"

2024-06-27 10:05:24 WARN HTTP 429 GET /login from 45.67.12.65 ua="curl/7.81.0"

2024-06-27 10:05:25 WARN HTTP 503 GET /api/orders from 45.67.12.96 ua="python-requests/2.31"

2024-06-27 10:05:37 WARN HTTP 503 GET /api/orders from 45.67.12.55 ua="Go-http-client/1.1"

2024-06-27 10:05:43 WARN HTTP 429 GET /api/orders from 45.67.12.16 ua="Mozilla/5.0"

2024-06-27 10:05:53 WARN HTTP 429 GET /login from 45.67.12.20 ua="curl/7.81.0"

2024-06-27 10:06:08 WARN HTTP 503 GET /api/orders from 45.67.12.9 ua="curl/7.81.0"

2024-06-27 10:06:11 WARN HTTP 503 GET /api/orders from 45.67.12.95 ua="Go-http-client/1.1"

2024-06-27 10:06:30 WARN HTTP 503 GET /api/orders from 45.67.12.14 ua="Mozilla/5.0"

2024-06-27 10:06:32 WARN HTTP 429 GET /login from 45.67.12.32 ua="Mozilla/5.0"

2024-06-27 10:06:46 WARN HTTP 429 GET /api/orders from 45.67.12.67 ua="Go-http-client/1.1"

2024-06-27 10:06:58 WARN HTTP 503 GET /api/orders from 45.67.12.49 ua="python-requests/2.31"

2024-06-27 10:07:19 WARN HTTP 503 GET /login from 45.67.12.36 ua="python-requests/2.31"

2024-06-27 10:07:20 WARN HTTP 429 GET /api/orders from 45.67.12.38 ua="Go-http-client/1.1"

2024-06-27 10:07:22 WARN HTTP 429 GET /login from 45.67.12.61 ua="Mozilla/5.0"

2024-06-27 10:07:26 WARN HTTP 503 GET /api/orders from 45.67.12.19 ua="Go-http-client/1.1"

2024-06-27 10:07:38 WARN HTTP 429 GET /login from 45.67.12.66 ua="Mozilla/5.0"

2024-06-27 10:07:58 WARN HTTP 503 GET /api/orders from 45.67.12.40 ua="curl/7.81.0"

2024-06-27 10:08:22 WARN HTTP 429 GET /login from 45.67.12.67 ua="Mozilla/5.0"

2024-06-27 10:08:24 WARN HTTP 429 GET /login from 45.67.12.99 ua="python-requests/2.31"

2024-06-27 10:08:32 WARN HTTP 503 GET /login from 45.67.12.24 ua="python-requests/2.31"

2024-06-27 10:08:36 WARN HTTP 503 GET /login from 45.67.12.8 ua="Go-http-client/1.1"

2024-06-27 10:08:38 WARN HTTP 429 GET /api/orders from 45.67.12.74 ua="Mozilla/5.0"

2024-06-27 10:08:40 WARN HTTP 429 GET /api/orders from 45.67.12.98 ua="curl/7.81.0"

2024-06-27 10:08:46 WARN HTTP 429 GET /api/orders from 45.67.12.11 ua="python-requests/2.31"

2024-06-27 10:09:07 WARN HTTP 429 GET /login from 45.67.12.98 ua="Mozilla/5.0"

2024-06-27 10:09:23 WARN HTTP 429 GET /api/orders from 45.67.12.85 ua="Go-http-client/1.1"

2024-06-27 10:09:25 WARN HTTP 429 GET /api/orders from 45.67.12.61 ua="python-requests/2.31"

2024-06-27 10:09:38 WARN HTTP 429 GET /login from 45.67.12.22 ua="Mozilla/5.0"

2024-06-27 10:09:54 WARN HTTP 429 GET /login from 45.67.12.68 ua="python-requests/2.31"
```


----

### Zadatak 8 (ddos.txt)

**Prebrojte koliko ima HTTP 429 odgovora (rate limit) u ddos.txt i sačuvajte broj u `Zadatak8_Ime_Prezime.txt`.**

**PowerShell komanda:**

```powershell
(Get-Content "$env:USERPROFILE\Desktop\ddos.txt" | Where-Object { $_ -match "HTTP 429" }).Count |
Out-File "$env:USERPROFILE\Desktop\Zadatak8_ime_prezime.txt"
```


**Odgovor:**

```
35
```


----


### Zadatak 9 (ddos.txt)

**Prebrojte koliko ima HTTP 503 odgovora (server overloaded/unavailable) u ddos.txt i sačuvajte broj u `Zadatak9_Ime_Prezime.txt`.**

**PowerShell komanda:**

```powershell
(Get-Content "$env:USERPROFILE\Desktop\ddos.txt" | Where-Object { $_ -match "HTTP 503" }).Count | Out-File "$env:USERPROFILE\Desktop\Zadatak9_ime_prezime.txt"
```

**Odgovor:**

```
27
```

----

### Zadatak 10 (ddos.txt)

**Prebrojte koliko ima jedinstvenih (unique) HTTP 503 odgovora u ddos.txt fajlu (ignorisanjem identičnih duplikat redova) i sačuvajte samo broj u `Zadatak9_Ime_Prezime.txt`.**

**PowerShell komanda:**

```powershell
( Get-Content "$env:USERPROFILE\Desktop\ddos.txt" | Where-Object {$_ -match "HTTP 429"} | Sort-Object -Unique).Count | Out-File "$env:USERPROFILE\Desktop\Zadatak10_ime_prezime.txt"
```

**Odgovor:**

```
33
```

-----
