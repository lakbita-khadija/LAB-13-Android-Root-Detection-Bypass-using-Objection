#  LAB 13 — Android Root Detection Bypass using Objection

---

##  Objective

The objective of this lab is to **analyze and bypass root detection mechanisms** implemented in an Android application using **dynamic instrumentation tools** such as **Frida** and **Objection**.

This lab demonstrates the **limitations of client-side security controls** and highlights how runtime manipulation can alter application behavior.

---

##  Tools & Technologies

| Tool                      | Purpose                    |
| ------------------------- | -------------------------- |
| ADB                       | Device communication       |
| Frida                     | Dynamic instrumentation    |
| Objection                 | Runtime mobile exploration |
| Android Emulator (API 33) | Testing environment        |
| UnCrackable-Level1        | Target application         |

---

##  Target Application

* **Name:** UnCrackable-Level1
* **Package:** `owasp.mstg.uncrackable1`
* **Source:** OWASP MSTG

---

##  Environment Setup

### Requirements

* Python installed
* ADB configured
* Android Emulator (API 33 - Google APIs)
* Frida (v16.5.9)
* Objection (v1.11.0 / v1.12.4)

---

###  Install Dependencies

```bash
pip install frida==16.5.9 frida-tools==13.6.0
pip install objection
```

---

###  Verify Setup

```bash
frida --version
adb devices
```

---

##  Lab Steps

---

### 🔹 Step 1 — Start Emulator

```bash
adb devices
adb shell getprop ro.build.version.sdk
```
<img width="819" height="99" alt="Capture d&#39;écran 2026-05-01 154542" src="https://github.com/user-attachments/assets/9913e82d-c9ea-44f2-a43b-690bbbe2b7a6" />

---

### 🔹 Step 2 — Install Application

```bash
adb install UnCrackable-Level1.apk
```
<img width="863" height="152" alt="Capture d&#39;écran 2026-05-01 154552" src="https://github.com/user-attachments/assets/dec67f0b-7420-45c6-95df-41094437dc12" />

---

### 🔹 Step 3 — Setup Frida Server

```bash
adb root
adb push frida-server /data/local/tmp/frida-server
adb shell chmod 755 /data/local/tmp/frida-server
```
<img width="872" height="149" alt="Capture d&#39;écran 2026-05-01 154523" src="https://github.com/user-attachments/assets/0a13113d-1aa6-41bb-99a5-29797537f86b" />

---

### 🔹 Step 4 — Launch Frida Server

```bash
adb shell "/data/local/tmp/frida-server &"
```
<img width="882" height="173" alt="Capture d&#39;écran 2026-05-01 154529" src="https://github.com/user-attachments/assets/f7ca466b-fff3-4cb0-92b4-1b7dd8621b22" />

Verify:

```bash
adb shell ps | grep frida
```
<img width="853" height="70" alt="Capture d&#39;écran 2026-05-01 154449" src="https://github.com/user-attachments/assets/11c0f7d2-d607-4995-ab5a-68e8be320e13" />

---

### 🔹 Step 5 — Verify Frida Connection

```bash
frida-ps -U
```
<img width="866" height="662" alt="Capture d&#39;écran 2026-05-01 154441" src="https://github.com/user-attachments/assets/b2babc68-21f4-41dc-93d4-fa4f0fca4cc7" />

---

### 🔹 Step 6 — Launch Application (Before Bypass)

```bash
adb shell monkey -p owasp.mstg.uncrackable1 1
```
<img width="889" height="356" alt="Capture d&#39;écran 2026-05-01 154728" src="https://github.com/user-attachments/assets/9799d2ba-570c-4de5-9f5d-65362e17e8f5" />


---

### 🔹 Step 7 — Start Objection

```bash
objection -g owasp.mstg.uncrackable1 explore
```
<img width="893" height="787" alt="Capture d&#39;écran 2026-05-01 154408" src="https://github.com/user-attachments/assets/2fae5b04-03d4-4123-bfcc-fda780640b70" />

---

### 🔹 Step 8 — Bypass Root Detection

```bash
android root disable
```
<img width="880" height="164" alt="image" src="https://github.com/user-attachments/assets/545e29c5-a19f-4177-8017-38db1db8022f" />

---

### 🔹 Step 9 — Relaunch Application

```bash
adb shell monkey -p owasp.mstg.uncrackable1 1
```
<img width="418" height="792" alt="Capture d&#39;écran 2026-05-01 154002" src="https://github.com/user-attachments/assets/024a4b38-94a1-463f-b079-5328cd003290" />

---


##  Notes & Observations

* Root detection may not trigger on **clean emulators (API 33)**
* Detection depends on:

  * Presence of `su` binary
  * System modifications
  * Debug tags
* Even if detection is not triggered, **bypass execution remains valid**

---

##  Key Findings

* Root detection is implemented at the **client-side**
* It can be **bypassed dynamically without modifying the APK**
* Runtime hooking allows manipulation of:

  * File checks
  * System properties
  * Command execution

---

##  Security Implications

* Client-side protections are **not reliable alone**
* Attackers can:

  * Hook methods
  * Modify runtime behavior
* Secure applications must include:

  * Server-side validation
  * Integrity checks
  * Anti-tampering mechanisms

---

##  Conclusion

This lab demonstrates how **dynamic instrumentation tools** can be used to bypass security mechanisms in Android applications.

It highlights the importance of **defense-in-depth strategies**, as client-side protections can be easily circumvented by skilled attackers.

---

