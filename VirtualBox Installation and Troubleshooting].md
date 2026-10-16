## VirtualBox Installation

Downloaded VirtualBox 7.2.20 for Windows hosts from the [official VirtualBox website](https://www.virtualbox.org/).

---

### SHA-256 Checksum Verification

To verify the downloaded installer's checksum, I located the `.exe` file in the Downloads folder and opened PowerShell by entering `powershell` in File Explorer's address bar.

I ran the following command to calculate the installer's SHA-256 hash:

```powershell
Get-FileHash ".\VirtualBox-7.2.20-175154-Win.exe" -Algorithm SHA256
```
<img width="959" height="201" alt="Installer&#39;s SHA-256 hash" src="https://github.com/user-attachments/assets/12d11614-8038-44d8-b709-92c7960bf491" />

I compared the calculated hash with the checksum listed in Oracle's [official SHA-256 checksums](https://download.oracle.com/virtualbox/7.2.20/SHA256SUMS). Both values matched.

<img width="811" height="332" alt="published SHA-256 checksum" src="https://github.com/user-attachments/assets/ee8a53c7-61e2-4178-8b5a-860116488f8c" />

---

### Installation Issue: Microsoft Visual C++ Redistributable

**Issue:** Encountered an installation error related to the Microsoft Visual C++ Redistributable, a runtime component required by some Windows applications.

<img width="275" height="164" alt="Microsoft Visual C++ Redistributable Error" src="https://github.com/user-attachments/assets/46e21914-bdfc-424c-a222-08b70411d132" />

**Resolution:**

1. Downloaded `vc_redist.x64.exe` from Microsoft's [official Visual C++ Redistributable downloads page](https://learn.microsoft.com/en-us/cpp/windows/latest-supported-vc-redist).
2. Installed the redistributable package.
3. Restarted the computer to complete the process.
