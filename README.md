# OverTheWire: Bandit Master Reference Guide (Levels 0 → 34)

A comprehensive technical reference, cheat sheet, and study guide covering Linux command-line operations, file system navigation, network security, privilege escalation, and Git analysis for the OverTheWire Bandit wargame.

---

## Technical Core Competencies Covered

* **Linux System Administration:** Command-line traversal, regular expressions, permission structures, SUID binaries, and cron automation.
* **Network Operations:** TCP sockets, SSL/TLS handshake inspection, port scanning, and SSH key authentication.
* **Data Inspection & Decoding:** Hex dumps, binary string extraction, Base64 decoding, ROT13 ciphers, and nested compression unpacking.
* **Version Control Security:** Git internal mechanics, commit log traversal, branch inspection, and tag analysis.

---

## Phase 1: File Navigation & Inspection (Levels 0 → 5)

* **Level 0 (Start): Log in via SSH**
```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220

```


*Concept:* Connects to the remote server over SSH on a custom port (`2220`).
* **Level 0 → 1: Standard File**
```bash
cat readme

```


*Concept:* Standard file inspection; reads plain text directly using `cat`.
* **Level 1 → 2: Dash Filename**
```bash
cat ./-

```


*Concept:* Relative paths stop `cat` from mistaking `-` as standard input or a command option.
* **Level 2 → 3: Spaces in Filename**
```bash
cat "spaces in this filename"

```


*Concept:* Wraps target paths containing spaces in quotes or escapes spaces with `\`.
* **Level 3 → 4: Hidden Files**
```bash
cat inhere/.hidden

```


*Concept:* Reveals hidden dotfiles (`.filename`) using `ls -la`.
* **Level 4 → 5: File Inspection**
```bash
file inhere/*
cat inhere/-file07

```


*Concept:* Scans unknown files with `file` to isolate human-readable ASCII text.

---

## Phase 2: Searching, Filtering & Encoding (Levels 5 → 11)

* **Level 5 → 6: Find by Attributes**
```bash
find inhere/ -type f -size 1033c ! -executable -exec cat {} +

```


*Concept:* Combines `find` filters for exact byte size (`1033c`), regular file type (`-type f`), and non-executable status (`! -executable`).
* **Level 6 → 7: System-wide Search**
```bash
find / -user bandit7 -group bandit6 -size 33c 2>/dev/null

```


*Concept:* Searches system-wide (`/`) by owner/group, sending permission errors to `/dev/null`.
* **Level 7 → 8: Grep Filtering**
```bash
grep "millionth" data.txt

```


*Concept:* Searches text files line-by-line for exact pattern matches.
* **Level 8 → 9: Unique Lines**
```bash
sort data.txt | uniq -u

```


*Concept:* Sorts lines alphabetically so `uniq -u` can filter out repeated lines to locate the single unique line.
* **Level 9 → 10: Strings in Binaries**
```bash
strings data.txt | grep "="

```


*Concept:* Extracts human-readable text strings buried inside non-text/binary files.
* **Level 10 → 11: Base64 Decoding**
```bash
base64 -d data.txt

```


*Concept:* Decodes Base64-encoded strings back to clear text.
* **Level 11 → 12: ROT13 / Caesar Cipher**
```bash
cat data.txt | tr 'A-Za-Z' 'N-ZA-Mn-za-m'

```


*Concept:* Uses `tr` to shift letters back by 13 positions to reverse a ROT13 cipher.

---

## Phase 3: Archive Unpacking & Networking (Levels 12 → 17)

* **Level 12 → 13: Nested Compression**
```bash
mkdir /tmp/myname && cd /tmp/myname
xxd -r ~/data.txt > decompressed
file decompressed
# Use mv, tar -xf, gunzip, bunzip2 based on file output

```


*Concept:* Reverses hex dumps via `xxd -r` and recursively decompresses multi-layered archives (`tar`, `gzip`, `bzip2`).
* **Level 13 → 14: SSH Key Authentication**
```bash
ssh -i sshkey.private bandit14@localhost -p 2220

```


*Concept:* Authenticates via SSH using a private RSA key file instead of a password.
* **Level 14 → 15: Netcat Port Transmission**
```bash
nc localhost 30000

```


*Concept:* Establishes a raw TCP network socket connection using `nc` (Netcat).
* **Level 15 → 16: SSL/TLS Connection**
```bash
openssl s_client -connect localhost:30001

```


*Concept:* Connects to encrypted network services over SSL/TLS using OpenSSL.
* **Level 16 → 17: Port Scanning & SSL Verification**
```bash
nmap -p 31000-32000 localhost
openssl s_client -connect localhost:<open_port>

```


*Concept:* Scans local ports using `nmap` and connects via SSL to extract credentials.
* **Level 17 → 18: Diffing Files**
```bash
diff passwords.old passwords.new

```


*Concept:* Compares two files line-by-line using `diff` to spot changes.

---

## Phase 4: Privilege Escalation & Cron Jobs (Levels 18 → 23)

* **Level 18 → 19: Bypassing Forced Commands**
```bash
ssh bandit18@bandit.labs.overthewire.org -p 2220 "cat readme"

```


*Concept:* Executes commands directly over SSH to bypass custom log-in scripts or automatic logouts.
* **Level 19 → 20: SUID Executables**
```bash
./bandit20-do cat /etc/bandit_pass/bandit20

```


*Concept:* Uses Set-owner-User-ID binaries to execute commands under higher-privilege account permissions.
* **Level 20 → 21: Netcat Listeners**
```bash
# Terminal 1: nc -lvp 1234
# Terminal 2: ./suconnect 1234

```


*Concept:* Sets up a local port listener using `nc` to receive incoming authentication connections from a SUID binary.
* **Level 21 → 22: Scheduled Cron Jobs**
```bash
cat /etc/cron.d/cronjob_bandit22
cat /usr/bin/cronjob_bandit22.sh

```


*Concept:* Inspects system cron configurations (`/etc/cron.d/`) to trace scheduled background scripts.
* **Level 22 → 23: Cron Script Analysis**
```bash
echo I am bandit23 | md5sum
cat /tmp/<calculated_hash_string>

```


*Concept:* Re-executes hash functions (`md5sum`) locally to predict dynamic filenames generated by cron jobs.
* **Level 23 → 24: Writing Shell Scripts**
```bash
echo "cat /etc/bandit_pass/bandit24 > /tmp/myname/pass" > /var/spool/bandit24/foo/script.sh
chmod 777 /var/spool/bandit24/foo/script.sh

```


*Concept:* Writes a custom shell script into an automated cron spool directory to execute background commands as another user.

---

## Phase 5: Brute-Forcing & Git Repositories (Levels 24 → 31)

* **Level 24 → 25: Pin Brute-Force Scripting**
```bash
for i in {0000..9999}; do echo "PASS $i"; done | nc localhost 30002

```


*Concept:* Pipes generated PIN combinations directly into a network socket to brute-force authentication.
* **Level 25 → 26: Restricted Shell / More Pager Escape**
```vim
:e /etc/bandit_pass/bandit26

```


*Concept:* Truncates terminal height to force output into `more`, then hits `v` to break out into `vim`.
* **Level 26 → 27: Custom Shell Breakout**
```vim
:set shell=/bin/bash
:shell

```


*Concept:* Redefines the shell variable inside `vim` to break out of restricted shells into standard Bash.
* **Level 27 → 28: Git Clone**
```bash
git clone ssh://bandit27-git@localhost:2220/home/bandit27-git/repo /tmp/myrepo

```


*Concept:* Clones local Git repositories over local SSH connections.
* **Level 28 → 29: Git Log Inspection**
```bash
git log -p

```


*Concept:* Reviews full commit histories and diffs to recover credentials removed from current files.
* **Level 29 → 30: Git Branching**
```bash
git branch -a
git checkout dev

```


*Concept:* Lists remote git branches and checks out alternate branches containing hidden files.
* **Level 30 → 31: Git Tags**
```bash
git tag
git show secret

```


*Concept:* Inspects Git tags where passwords or notes may be saved off-branch.
* **Level 31 → 32: Git Push / Hooks**
```bash
echo "May I have the password?" > key.txt
git add -f key.txt && git commit -m "push" && git push origin master

```


*Concept:* Pushes modified commits back to origin repositories to trigger server-side hook events.

---

## Phase 6: Upper Shell Escapes (Levels 32 → 34)

* **Level 32 → 33: Restricted Uppercase Shell Escape**
```bash
$0

```


*Concept:* Invokes positional parameter `$0` (the execution command itself) to escape restricted shell wrappers into standard Bash.
* **Level 33 → 34: Final Verification**
*Concept:* Completion of the Bandit wargame challenges!
