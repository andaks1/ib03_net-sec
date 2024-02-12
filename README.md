# Домашнее задание к занятию «Защита сети»

------

### Подготовка к выполнению заданий

1. Подготовка защищаемой системы:

- установите **Suricata**,
- установите **Fail2Ban**.

2. Подготовка системы злоумышленника: установите **nmap** и **thc-hydra** либо скачайте и установите **Kali linux**.

Обе системы должны находится в одной подсети.

------

### Задание 1

Проведите разведку системы и определите, какие сетевые службы запущены на защищаемой системе:

**sudo nmap -sA < ip-адрес >**

**sudo nmap -sT < ip-адрес >**

**sudo nmap -sS < ip-адрес >**

**sudo nmap -sV < ip-адрес >**

По желанию можете поэкспериментировать с опциями: https://nmap.org/man/ru/man-briefoptions.html.


*В качестве ответа пришлите события, которые попали в логи Suricata и Fail2Ban, прокомментируйте результат.*

#### Ответ на задание 1.

<details>
<summary>Лог событий при проведении сканирования:</summary>

```BASH
nmap -sT 10.254.239.11

02/12/2024-10:31:33.191586  [**] [1:2010937:3] ET SCAN Suspicious inbound to mySQL port 3306 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.254.239.13:43668 -> 10.254.239.11:3306
02/12/2024-10:31:33.215510  [**] [1:2002911:6] ET SCAN Potential VNC Scan 5900-5920 [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 10.254.239.13:60240 -> 10.254.239.11:5902
02/12/2024-10:31:33.216237  [**] [1:2010936:3] ET SCAN Suspicious inbound to Oracle SQL port 1521 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.254.239.13:52676 -> 10.254.239.11:1521
02/12/2024-10:31:33.240451  [**] [1:2002910:6] ET SCAN Potential VNC Scan 5800-5820 [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 10.254.239.13:59388 -> 10.254.239.11:5802
02/12/2024-10:31:33.243287  [**] [1:2010935:3] ET SCAN Suspicious inbound to MSSQL port 1433 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.254.239.13:40430 -> 10.254.239.11:1433
02/12/2024-10:31:33.255537  [**] [1:2010939:3] ET SCAN Suspicious inbound to PostgreSQL port 5432 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.254.239.13:34986 -> 10.254.239.11:5432

Сканирование с вызовом системного вызова connect. Как правило, с этим вызовом работают высокоуровневые приложения, типа браузера и у nmap не так много власти в использовании вызова. Собственно и сканирование более заметное, и записей в логах больше.



nmap -sS 10.254.239.11

02/12/2024-10:32:01.651464  [**] [1:2010937:3] ET SCAN Suspicious inbound to mySQL port 3306 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.254.239.13:63909 -> 10.254.239.11:3306
02/12/2024-10:32:01.664995  [**] [1:2010936:3] ET SCAN Suspicious inbound to Oracle SQL port 1521 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.254.239.13:63909 -> 10.254.239.11:1521
02/12/2024-10:32:01.677664  [**] [1:2010939:3] ET SCAN Suspicious inbound to PostgreSQL port 5432 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.254.239.13:63909 -> 10.254.239.11:5432
02/12/2024-10:32:01.703787  [**] [1:2010935:3] ET SCAN Suspicious inbound to MSSQL port 1433 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.254.239.13:63909 -> 10.254.239.11:1433

Сканирование полуоткрытым методом. Не такое заметное, плюс nmap может формировать tcp пакет самостоятельно, выставляя различные флаги (в данной опции SYN), что дает ему большую свободу действий. Тем не менее, мы видим как IDS все равно ловит это сканирование.

nmap -sV 10.254.239.11

02/12/2024-10:32:10.944210  [**] [1:2010937:3] ET SCAN Suspicious inbound to mySQL port 3306 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.254.239.13:46034 -> 10.254.239.11:3306
02/12/2024-10:32:10.955018  [**] [1:2010936:3] ET SCAN Suspicious inbound to Oracle SQL port 1521 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.254.239.13:46034 -> 10.254.239.11:1521
02/12/2024-10:32:10.976727  [**] [1:2010939:3] ET SCAN Suspicious inbound to PostgreSQL port 5432 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.254.239.13:46034 -> 10.254.239.11:5432
02/12/2024-10:32:10.981072  [**] [1:2010935:3] ET SCAN Suspicious inbound to MSSQL port 1433 [**] [Classification: Potentially Bad Traffic] [Priority: 2] {TCP} 10.254.239.13:46034 -> 10.254.239.11:1433
02/12/2024-10:32:17.144313  [**] [1:2009358:5] ET SCAN Nmap Scripting Engine User-Agent Detected (Nmap Scripting Engine) [**] [Classification: Web Application Attack] [Priority: 1] {TCP} 10.254.239.13:50970 -> 10.254.239.11:80
02/12/2024-10:32:17.144313  [**] [1:2024364:3] ET SCAN Possible Nmap User-Agent Observed [**] [Classification: Web Application Attack] [Priority: 1] {TCP} 10.254.239.13:50970 -> 10.254.239.11:80
02/12/2024-10:32:17.144775  [**] [1:2009358:5] ET SCAN Nmap Scripting Engine User-Agent Detected (Nmap Scripting Engine) [**] [Classification: Web Application Attack] [Priority: 1] {TCP} 10.254.239.13:50986 -> 10.254.239.11:80
02/12/2024-10:32:17.144775  [**] [1:2024364:3] ET SCAN Possible Nmap User-Agent Observed [**] [Classification: Web Application Attack] [Priority: 1] {TCP} 10.254.239.13:50986 -> 10.254.239.11:80
02/12/2024-10:32:17.149524  [**] [1:2009358:5] ET SCAN Nmap Scripting Engine User-Agent Detected (Nmap Scripting Engine) [**] [Classification: Web Application Attack] [Priority: 1] {TCP} 10.254.239.13:51006 -> 10.254.239.11:80
02/12/2024-10:32:17.149524  [**] [1:2024364:3] ET SCAN Possible Nmap User-Agent Observed [**] [Classification: Web Application Attack] [Priority: 1] {TCP} 10.254.239.13:51006 -> 10.254.239.11:80
02/12/2024-10:32:17.150660  [**] [1:2009358:5] ET SCAN Nmap Scripting Engine User-Agent Detected (Nmap Scripting Engine) [**] [Classification: Web Application Attack] [Priority: 1] {TCP} 10.254.239.13:51016 -> 10.254.239.11:80
02/12/2024-10:32:17.150660  [**] [1:2024364:3] ET SCAN Possible Nmap User-Agent Observed [**] [Classification: Web Application Attack] [Priority: 1] {TCP} 10.254.239.13:51016 -> 10.254.239.11:80

Сканирование с определением версий и софта, слушающего найденные порты. Хорошо видно, как сканер "нашумел" в логах IDS.

```
</details>

---

### Задание 2

Проведите атаку на подбор пароля для службы SSH:

**hydra -L users.txt -P pass.txt < ip-адрес > ssh**

1. Настройка **hydra**: 
 
 - создайте два файла: **users.txt** и **pass.txt**;
 - в каждой строчке первого файла должны быть имена пользователей, второго — пароли. В нашем случае это могут быть случайные строки, но ради эксперимента можете добавить имя и пароль существующего пользователя.

Дополнительная информация по **hydra**: https://kali.tools/?p=1847.

2. Включение защиты SSH для Fail2Ban:

-  открыть файл /etc/fail2ban/jail.conf,
-  найти секцию **ssh**,
-  установить **enabled**  в **true**.

Дополнительная информация по **Fail2Ban**:https://putty.org.ru/articles/fail2ban-ssh.html.



*В качестве ответа пришлите события, которые попали в логи Suricata и Fail2Ban, прокомментируйте результат.*

#### Ответ на задание 2.

Логи fail2ban:
```BASH
2024-02-12 11:13:40,712 fail2ban.filter         [3157]: INFO    [sshd] Found 10.254.239.13 - 2024-02-12 11:13:40
2024-02-12 11:13:40,715 fail2ban.filter         [3157]: INFO    [sshd] Found 10.254.239.13 - 2024-02-12 11:13:40
2024-02-12 11:13:40,741 fail2ban.filter         [3157]: INFO    [sshd] Found 10.254.239.13 - 2024-02-12 11:13:40
2024-02-12 11:13:40,767 fail2ban.actions        [3157]: NOTICE  [sshd] 10.254.239.13 already banned
2024-02-12 11:13:40,767 fail2ban.actions        [3157]: NOTICE  [sshd] 10.254.239.13 already banned
2024-02-12 11:13:40,771 fail2ban.filter         [3157]: INFO    [sshd] Found 10.254.239.13 - 2024-02-12 11:13:40
2024-02-12 11:13:40,840 fail2ban.filter         [3157]: INFO    [sshd] Found 10.254.239.13 - 2024-02-12 11:13:40
2024-02-12 11:13:40,847 fail2ban.filter         [3157]: INFO    [sshd] Found 10.254.239.13 - 2024-02-12 11:13:40
2024-02-12 11:13:40,847 fail2ban.filter         [3157]: INFO    [sshd] Found 10.254.239.13 - 2024-02-12 11:13:40
2024-02-12 11:13:40,888 fail2ban.filter         [3157]: INFO    [sshd] Found 10.254.239.13 - 2024-02-12 11:13:40
2024-02-12 11:13:40,893 fail2ban.filter         [3157]: INFO    [sshd] Found 10.254.239.13 - 2024-02-12 11:13:40
2024-02-12 11:13:40,897 fail2ban.filter         [3157]: INFO    [sshd] Found 10.254.239.13 - 2024-02-12 11:13:40
2024-02-12 11:13:40,969 fail2ban.actions        [3157]: NOTICE  [sshd] 10.254.239.13 already banned
2024-02-12 11:13:43,145 fail2ban.filter         [3157]: INFO    [sshd] Found 10.254.239.13 - 2024-02-12 11:13:43
```
При правильной настройке тюрьмы сервиса для атакующего хоста в логах fail2ban будем видеть блокировки активности.


Логи suricata:
```BASH
02/12/2024-11:09:21.860290  [**] [1:2001219:20] ET SCAN Potential SSH Scan [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 10.254.239.13:54540 -> 10.254.239.11:22
02/12/2024-11:09:21.860290  [**] [1:2003068:7] ET SCAN Potential SSH Scan OUTBOUND [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 10.254.239.13:54540 -> 10.254.239.11:22
02/12/2024-11:09:21.861571  [**] [1:2003068:7] ET SCAN Potential SSH Scan OUTBOUND [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 10.254.239.13:54576 -> 10.254.239.11:22
02/12/2024-11:09:21.863102  [**] [1:2003068:7] ET SCAN Potential SSH Scan OUTBOUND [**] [Classification: Attempted Information Leak] [Priority: 2] {TCP} 10.254.239.13:54630 -> 10.254.239.11:22
```

Дефолтные наборы правил сурикаты позволяют определить подобного рода сканирования и атаки подбора паролей к определенным сервисам.


---
