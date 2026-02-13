# ImaginaryCTF 2025 – Weird App (Reverse Engineering) 🧩

**Team:** ExploitExperts  
**Challenge:** Weird-App (100 pts, Difficulty 4/10)  
**Category:** Reverse Engineering  
**Tool used:** JADX, Python, ChatGPT (reference only)  
**Flag:** `ictf{1l0v3@ndr0idstud103}`

---

## 📌 Overview

This repository contains our team's full write-up for the **weird-app** challenge from ImaginaryCTF 2025.  
The challenge provided an Android APK (`app-debug.apk`) containing an obfuscated flag hidden inside a transformation function.

We performed static analysis using **JADX-GUI**, located the `transformFlag` function, reversed the obfuscation logic using **Python**, and successfully retrieved the flag.

---

## 🧠 Our Approach

### 1. Initial Recon
- Downloaded `weird.zip` → extracted `app-debug.apk`
- Loaded APK into **JADX-GUI** for decompilation
- Searched for keywords: `flag`, `REDACTED`, `transform`, `encrypt`

### 2. Identifying the Logic
- Found `MainActivity.kt` → contained `transformFlag("REDACTED")`
- The function applied **position-based character mapping**:
  - Lowercase letters: rotated by index
  - Digits: rotated by `(index * index)`
  - Symbols: rotated by `(index * index * index)`

### 3. Reversing the Obfuscation
- Extracted the transformed string:  
  `idvi+1{s6e3{)arg2zv[moqa905+`
- Wrote a Python script to **invert** the transformation per character
- Recovered the original flag

### 4. Flag Submission
✅ `ictf{1l0v3@ndr0idstud103}`

---

## 🛠️ Tools Used

| Tool      | Purpose |
|-----------|---------|
| **JADX-GUI** | Decompile APK to readable Java/Kotlin |
| **Python 3** | Write reverse transformation script |
| **Kali Linux** | Analysis environment |
| **GitHub** | Reference to official solve.py (post-event) |
| **ChatGPT** | Brainstorming and code explanation (reference only) |

---

## 🧪 Python Decoder (Simplified)

```python
def reverse_transform(encoded):
    alphabet = 'abcdefghijklmnopqrstuvwxyz'
    digits = '0123456789'
    symbols = '!@#$%^&*()_+-={}:<>?[]'
    result = []

    for i, ch in enumerate(encoded):
        if ch in alphabet:
            idx = alphabet.index(ch)
            orig = (idx - i) % 26
            result.append(alphabet[orig])
        elif ch in digits:
            idx = digits.index(ch)
            orig = (idx - (i * i)) % 10
            result.append(digits[orig])
        elif ch in symbols:
            idx = symbols.index(ch)
            orig = (idx - (i * i * i)) % len(symbols)
            result.append(symbols[orig])
        else:
            result.append(ch)
    return ''.join(result)

encoded_flag = "idvi+1{s6e3{)arg2zv[moqa905+"
print(reverse_transform(encoded_flag))
# Output: ictf{1l0v3@ndr0idstud103}
