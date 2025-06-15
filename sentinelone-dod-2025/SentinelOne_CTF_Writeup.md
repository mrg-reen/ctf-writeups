
# SentinelOne DoD CTF Write-Up
https://www.correlation-one.com/dod-cyber-sentinel

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
- `robots.txt` can leak hidden or sensitive paths.
- Crawlers operate recursively and follow explicit paths unless told otherwise.
- Useful tools: `gobuster`, `ffuf`, `dirb`, `wget -r`, `curl`, `Burp Suite`.

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

### Lessons:
- Media files can carry hidden data in metadata fields.
- Always check metadata using `exiftool` or similar tools.

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

### Lessons:
- Reverse image search can be powerful but unreliable depending on access.
- RIFF/WebP formats may not be compatible with default Linux tools.
- Manual tools like `tesseract-ocr` and `findimagedupes` can reveal embedded or visual anomalies.

---

## Challenge: `Packet Whisperer` – 75 pts

### Steps:
- Downloaded and analyzed `login.pcap` with Wireshark.
- Applied HTTP filter: `http`
- Located POST request and extracted login credentials.

### Lessons:
- HTTP traffic in PCAPs often reveals credentials due to lack of encryption.
- Filter by protocol and inspect POST bodies carefully.
- Always sanitize packet captures in real-world environments.

---

## Challenge: `Hardcoded Lies` – 75 pts

### Steps:
- Downloaded binary.
- Used:
  - `file` – identified file type.
  - `strings` – extracted printable characters.
- Flag found within `strings` output.

### Lessons:
- Never trust binaries blindly — run `file` and `strings` before executing.
- `strings` can reveal hardcoded credentials, flags, or debug info.

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

### Lessons:
- Basic encoding (like base64) is often used to obscure payloads or data.
- Learn to identify and decode base64 and hex strings on sight.

---

## Challenge: `Hidden in Plain Sight` – 75 pts

### Steps:
- Downloaded `selfie.png`.
- Ran:
  ```bash
  exiftool selfie.png | grep -i "C1"
  ```

### Lessons:
- Flags can be embedded in metadata that may not appear visibly or via image viewers.
- Pattern searching (`grep`) helps streamline metadata inspection.

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

### Lessons:
- IDOR (Insecure Direct Object Reference) vulnerabilities are common in parameterized URLs.
- Analyze how parameters change and test ranges using automated scripts.

---

## Challenge: `None Shall Pass` – 200 pts

### Steps:
- Logged into web portal and received a JSON Web Token (JWT).
- Decoded JWT manually (Base64 segments).
- Discovered user, role, `iat`, and `exp` fields in payload.

### Lessons:
- JWTs can leak role-based or time-based access information if not properly secured.
- Always inspect and decode JWTs; never assume they are tamper-proof without signature validation.

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
