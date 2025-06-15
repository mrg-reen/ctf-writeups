
# SentinelOne DoD CTF Write-Up

## Challenge: `Secret.txt Society` – 75 pts

**Objective**: Explore web structure via `robots.txt`

### Steps:
- Navigated to the root of the website.
- Accessed `robots.txt` via `https://<site>/robots.txt`.
- Found:
  ```txt
  Disallow: /juchejaguar/
  ```
- Visited `/juchejaguar/` and retrieved the flag:
  ```
  C1{r0b0ts_arent_4lways_p0lit3}
  ```

### Lessons:
- `robots.txt` exposes non-indexable paths—often revealing sensitive directories.
- Crawlers operate recursively: collect → follow → repeat.
- Tools used for directory enumeration:
  - `gobuster`, `dirb`, `ffuf`
  - `curl`, `wget -r`
  - `Burp Suite` (crawler, repeater, interceptor)
- Always check `/robots.txt` and `sitemap.xml`.

---

## Challenge: `Field Reports Mayhem` – 150 pts

### Steps:
- Installed EXIF tool:  
  ```bash
  sudo apt install libimage-exiftool-perl
  ```
- Ran:
  ```bash
  exiftool file.mp3
  ```
- Discovered flag under `Encoded by:`:
  ```
  C1{metadata_tells_more}
  ```

---

## Challenge: `Cafe Confidential` – 75 pts

### Steps:
- Analyzed image metadata using `exiftool` — nothing useful.
- Compared image headers using `file` — found differing formats.
- Installed `gwenview` to view RIFF/WebP on Linux.
- Attempted Yandex/Google Lens reverse image search — blocked.
- Used `findimagedupes` and `tesseract-ocr`.

### Findings:
- Cake photo geotag revealed:  
  ```
  21 Lowndes St, London
  ```
- Harrods photo likely related.
- Investigative process was key; no direct flag retrieved from this section.

---

## Challenge: `Packet Whisperer` – 75 pts

### Steps:
- Downloaded and analyzed `login.pcap` with Wireshark.
- Applied HTTP filter: `http`
- Located POST request and extracted login credentials.
- Flag located in POST payload.

---

## Challenge: `Hardcoded Lies` – 75 pts

### Steps:
- Downloaded binary.
- Used:
  - `file` – identified file type.
  - `strings` – extracted printable characters.
- Flag found within `strings` output.

---

## Challenge: `Encoded Evidence` – 75 pts

### Steps:
- Downloaded `invoice.vbs`.
- Ran:
  ```bash
  cat invoice.vbs
  ```
- Extracted base64-encoded URL.
- Decoded using:
  ```bash
  echo "<base64string>" | base64 -d
  ```
- Retrieved flag from decoded content.

---

## Challenge: `Hidden in Plain Sight` – 75 pts

### Steps:
- Downloaded `selfie.png`.
- Ran:
  ```bash
  exiftool selfie.png | grep -i "C1"
  ```
- Flag found within metadata fields.

---

## Challenge: `Field Reports Mayhem (Web)` – 150 pts

### Steps:
- Logged in and reached `/dashboard.php`.
- Monitored browser behavior:
  - Network tab: observed parameter changes in URLs.
  - Discovered:  
    ```
    dashboard.php?id=1234&code=...
    ```
- Performed IDOR attack by scripting curl requests with varying `id=` values.
- Retrieved flag from dashboard with `id=1337`.

---

## Challenge: `None Shall Pass` – 200 pts

### Steps:
- Logged into web portal and received a JSON Web Token (JWT).
- Decoded JWT manually (Base64 segments).
- Discovered user, role, `iat`, and `exp` fields in payload.
- Insight: potential for JWT forgery or privilege escalation (future vector).

---

## Final Notes

### Tools Summary:
- Recon & Enumeration:
  - `gobuster`, `ffuf`, `dirb`, `curl`, `wget -r`, `Burp Suite`
- Metadata & File Analysis:
  - `exiftool`, `file`, `strings`, `findimagedupes`, `tesseract-ocr`
- Packet Analysis:
  - `Wireshark`
- JWT Analysis:
  - `jwt.io`, `base64`, manual decoding

### Takeaways:
- Always check common misconfigurations (`robots.txt`, IDOR, metadata).
- Automate what you can, but know when to pivot to manual inspection.
- The essence of CTF is not just solving—but decoding intent and improving intuition.
