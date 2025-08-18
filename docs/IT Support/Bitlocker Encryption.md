
# BitLocker Encryption

## 1. **Enable Full-Disk Encryption with BitLocker**

- Open **Manage BitLocker** via **Control Panel**.
- Select the OS drive (`C:`) and click **Turn on BitLocker**.
- Choose an unlock method (password or USB key).
- Save the **recovery key** securely.
- Select **"Encrypt entire drive"** and **"New encryption mode"**.
- Start the encryption process.

## 2. **Forgetting the Encryption Key**

- **Delete** the BitLocker recovery key:
  - From Microsoft account or printout.
  - From USB storage (if applicable).
- **Forget the password**:
  - Once the key is lost, data becomes **irrecoverable**.

## 3. **Securely Erase the Encrypted Disk**

### Option 1: **Delete Recovery Key**
- Remove from your **Microsoft account** or **USB drive**.

### Option 2: **Lock BitLocker Without Decrypting**
- Open **Command Prompt as Administrator**.
- Run:
  ```powershell
  manage-bde -forcerecovery C:
  ```

### Option 3: **Wipe the Disk**
- Boot from **Windows Installation Media**.
- Open **Command Prompt** from recovery options.
- Use **DiskPart**:
  ```powershell
  diskpart
  list disk
  select disk 0
  clean
  ```
  This will **erase** the data from the disk completely.

### Option 4: **Reformat the SSD**
- After wiping, create new partitions and format the SSD:
  ```powershell
  create partition primary
  format fs=ntfs quick
  assign
  exit
  ```

## 4. **Check BitLocker Encryption Status**

### Option 1: **Control Panel**
- Open **Manage BitLocker** to check encryption status.

### Option 2: **Command Prompt**
- Open **Command Prompt as Administrator**.
- Run:
  ```powershell
  manage-bde -status
  ```

### Option 3: **File Explorer (Non-OS Drives)**
- Look for a **lock icon** on encrypted drives.

### Option 4: **Unlock Drive (If Required)**
- If prompted for a **password or USB key**, provide it to unlock the drive.

## 5. **Disable BitLocker without TPM (if applicable)**

### Enable Group Policy for BitLocker without TPM
- Open **Group Policy Editor**:
  ```shell
  gpedit.msc
  ```
- Navigate to:
  ```
  Computer Configuration > Administrative Templates > Windows Components > BitLocker Drive Encryption > Operating System Drives
  ```
- Enable **"Require additional authentication at startup"** and check **"Allow BitLocker without a compatible TPM"**.
- Run:
  ```powershell
  manage-bde -on C: -password
  ```

## 6. **Important Notes**
- Once BitLocker is **enabled and the key is deleted**, the data is **irrecoverable**.
- The drive can be **reused** after formatting.
- **Backup the recovery key** before starting the encryption process.
