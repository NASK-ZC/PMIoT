# Preparation Phase for IoT Penetration Testing


This document defines the preparation phase activities required before starting the penetration testing of an IoT device.
## 1. Main Testing Equipment and Software

The main equipment and software used during IoT penetration testing should include:

| Category | Examples | What we used
|---|---|---|
| Computer | Laptop or workstation used for analysis, interception, scanning, and documentation. | Various
| Radio equipment | Hardware appropriate to the tested interfaces, for example Wi-Fi, Bluetooth, RF433, Zigbee, or Z-Wave adapters. | HackRF, RTL-SDR, nRF52840, OM15080-K32W, TP-LINK TL-WN722N v2, Flipper Zero |
| Smartphone | Mobile device used for pairing, configuration, and testing the vendor application. | Asus ZenFone 8 (Rooted)|
| Operating system and tools | Kali Linux or equivalent testing environment with relevant security tools described in next sections. | Kali Linux

### 1.1. Workstation Preparation

Recommended workstation:

| Component | Recommendation |
|---|---|
| Operating system | Kali Linux, Ubuntu, Debian, or similar Linux system |
| RAM | Minimum 8 GB, recommended 16 GB |
| Disk space | Minimum 50 GB free space |
| Network | Access to isolated test Wi-Fi or lab network |
| USB access | Required for physical Android device testing |
| Docker | Recommended for MobSF |

Install system dependencies according to the official documentation of the selected operating system. At minimum, the workstation should include:

-   Java Runtime / JDK,
-   Python 3 and pip,
-   Docker,
-   Wireshark / tshark,
-   Git,
-   unzip / zip tools.

Additional specific tools and equipment needed during testing the individual PMIoT layers are listed below.
## 2. Radio layer

The tool selection for the radio layer depends on the protocols used by the tested device as well as intended scope of the test. In this document, we assume only tests in unlicensed ISM bands are to be performed.

### 2.1. Proposed Hardware Tools

| Tool               | Purpose    | Notes |
| -----------        | ---------- | ----- |
| Wi-Fi router | Testing IEEE 802.11 connectivity | Needs to support 3rd party firmware (e.g. OpenWRT) and intended range of IEEE 802.11 protocols |
| ZigBee Transceiver | Testing ZigBee protocol | For example NXP OM15080-K32W or TI CC2531 |
| BLE Transceiver    | Testing BLE protocol | For example NXP OM15080-K32W |
| NFC/RFID Reader    | Testing near field connectivity | For example Proxmark 3 |
| Other dedicated hardware | e.g. for Z-Wave, Thread, LoRaWan | Depending on specific needs, less frequent in consumer devices |
| SDR device         | Reconnaisence and testing uncommon protocols | RTL-SDR for receiving, HackRF for receiving and transmitting signals |

As optional additions one can consider standalone devices, such as Flipper Zero or HackRF with Porta Pack.
Dedicated transceiver dongles usually have built-in antennas. These that do not, and SDR devices, require external antennas for proper operation. For EU ISM ranges, we suggest obtaining 433 MHz, 868 MHz and 2.4 GHz antennas.

### 2.2. Proposed Software Tools

| Tool | Purpose |
| ---- | ------- |
| Radioconda | Collection of open source software radio packeges |
| Universal Radio Hacker | Investigating custom or unknown protocols |
| Wireshark | Analyzing captured traffic |

Beside these open source tools, some hardware, such as dedicated protocol dongles, require additional software provided by their vendors.

#### 2.2.1. Radioconda

Radioconda is a collection of open-source SDR packages [Download and install](https://github.com/ryanvolz/radioconda)

It includes drivers for previously listed SDR devices, RTL-SDR and HackRF, as well as related software, including Gqrx (receiver, spectrum analyzer) and GNURadio (radio software development kit). The latter can be used to implement receivers and transmitters for any custom protocol.


#### 2.2.2. Universal Radio Hacker

Universal Radio Hacker is a tool used to demodulate and reverse-engineer unknown radio protocols. [Download and install](https://github.com/jopohl/urh#Installation)

It can be used to analyze and modify captures from SDR devices, including live capture capability and ability to transmit modified signals.

#### 2.2.3. Wireshark and tshark

Install Wireshark and tshark according to the official Wireshark installation instructions or the operating system package manager. [Download and install](https://www.wireshark.org/download.html)

Use Wireshark or tshark to capture and analyze:

- Wi-Fi traffic,
- BLE, ZigBee etc. traffic captured by dedicated hardware,
- Traffic captured by GNURadio using RFTap.

#### 2.2.4. Vendor specific software

Some tools require proprietary vendor software to operate. For example, the NXP OM15080-K32W dongle uses [NXP Connectivity Tool Suite](https://www.nxp.com/design/design-center/development-boards-and-designs/frdm-development-boards/connectivity-tool-suite:CONNECTIVITY-TOOL-SUITE), and Proxmark 3 has a dedicated [GitHub Repository](https://github.com/RfidResearchGroup/proxmark3).
Each case is unique and requires following vendor recommendations and license agreements.


## 3. IP layer

This section lists tools proposed in relation to the IP network layer. Selection of tools depends on the environment and devices being tested. 

### 3.1. Proposed Hardware Tools

| Tool               | Purpose    | Example |
| -----------        | ---------- | ----- |
| external Wi-Fi adapter | Performing traffic capture and sending generated traffic | TP-LINK TL-WN722N v2 |

Traffic capture can be performed using a hardware Wi-Fi adapter, such as the TP-LINK TL-WN722N v2 (an inexpensive and widely available device). For version 1 of this adapter, no additional steps are necessary, but for versions 2 or 3, additional drivers must be installed to enable the adapter in monitor mode.

In Kali Linux, it is need to download alternative drivers, install them, and block the use of default drivers. To do this, run the following commands:

```
sudo apt update
sudo apt install bc
sudo rmmod r8188eu.ko
git clone https://github.com/aircrack-ng/rtl8188eus
cd rtl8188eus
sudo -i
echo "blacklist r8188eu" > "/etc/modprobe.d/realtek.conf"
exit
make
sudo make install
sudo modprobe 8188eu
```


Monitor mode can be enabled as follows:
```
ifconfig wlan0 down
airmon-ng check kill
iwconfig wlan0 mode monitor
ifconfig wlan0 up
iwconfig

```



Sometimes, when trying to enable monitor mode, you may encounter problems, such as Error for wireless request "Set Mode" (8B06): SET failed on device wlan0; Operation not permitted., then you must enter additional commands:
```
sudo ifconfig wlan0 up
sudo rmmod r8188eu.ko
sudo modprobe 8188eu
sudo iwconfig wlan0 mode auto
sudo ifconfig wlan0 down
sudo iwconfig wlan0 mode monitor
sudo ifconfig wlan0 up
```


### 3.2. Proposed Software Tools

As most of the tools are well known, we omit further descriptions and installation procedures, we only list tools and their purpose in the context of IP network layer pentesting.

| Tool                | Purpose                                           |
| ------------------- | ------------------------------------------------- |
| airmon-ng, airbase-ng, kismet | various tools used for Wi-Fi network sniffing and hacking |
| Wireshark                     | a network protocol analyzer that can capture and inspect network traffic, including IoT communications, for troubleshooting and security analysis |
| IoTShark                      | a sniffer and traffic analyzer, dedicated to IoT environments |
| sslsniff, sslsplit, sslstrip  | various tools to enable SSL/TLS decryption                    |
| ettercap                      | a sniffer, which enables performing Man in the Middle attacks in the IP networks |
| Burp Suite | a web application security testing platform consisting of multiple specialized modules, in the context of the network layer used as an HTTP proxy tool |
| p0f | a traffic analyzer with a capability of services’ or devices’ fingerprinting |
| Nmap | a network scanner capable of services and version identification, with basic vulnerability scanner functionalities |
| legion | a network penetration testing framework |
| scapy | interactive packet manipulation library in Python able to forge, decode, send, and capture packets of many protocols |
| Hydra, medusa, ncrack | example tools for performing online password attacks |
| sfuzz | Simple Fuzzer - simple to use, but powerful and flexible black box testing utility for fuzzing |
| HomePwn | a framework to audit and pentest devices, it can find devices, and exploit vulnerabilities to read or send data to those devices. It has a modular architecture and two main components: discovery modules to find devices and specific modules for the technology to be audited |
| PENIOT | a framework for targeting IoT devices with protocol-based security attacks, it can perform active security attacks like consuming system resources, replaying valid communication, and also perform passive security attacks such as breaching the confidentiality of important information or conducting traffic analysis |
| IoT-Inspector | an open-source tool for capturing, analyzing, and visualizing the network activities of smart home devices |
| IoTSeeker | a scanner for specific types of IoT devices to detect if they are using the default, factory-set credentials; it focuses on HTTP/HTTPS services only |
| IoTVAS | a service that, based on the banners of certain services available on the device and the device’s MAC address, provides information about the device’s manufacturer, model, and version, as well as known vulnerabilities. It also provides information about default passwords, cryptographic keys used in the firmware, active and expired certificates, and other configuration issues. The tool is only able to identify and assess devices that have been previously analyzed and added to the fingerprint database. |
| Metasploit and Armitage | a vulnerability scanner and a framework for conducting penetration testing, which allows for exploiting vulnerabilities. While not specifically designed for IoT devices, it does include a few IoT-related modules. Armitage is a graphical interface for Metasploit, created by the user community. |
| GCE | Greenbone Community Edition is a vulnerability management framework, which includes the OpenVAS general-purpose vulnerability scanner. While free to use, the utility of this tool in the context of IoT device vulnerability scanning is limited, primarily due to the limited number of reported IoT device vulnerabilities and their specific nature |


## 4. Web application layer
This section describes the preparation of the web application testing workstation and testbed.
### 4.1. Required Tools

| Tool                | Purpose                                           |
| ------------------- | ------------------------------------------------- |
| Wappalyzer          | technology stack identification                   |
| Nmap                | service enumeration                               |
| Burp Suite          | HTTP/HTTPS traffic interception and manipulation  |
| Nikto               | web server vulnerability scanning                 |
| ffuf                | web content and endpoint fuzzing                  |
| sqlmap              | SQL injection detection and exploitation          |
| Dalfox              | XSS detection and analysis                        |
| Hydra               | brute‑force testing of  authentication mechanisms |
| SecLists            | wordlists for security testing                    |
| PayloadAllTheThings | attack payload reference and examples             |

### 4.2. Wappalyzer
Install Wappalyzer as a browser extension using the official Web Store for the selected browser (e.g., Chrome Web Store or Firefox Add-ons).

Use Wappalyzer for:
- identification of web application frameworks,
- detection of server-side technologies,
- detection of third‑party libraries and services (e.g., analytics, CDN, authentication providers).

To use Wappalyzer, navigate to the target web interface in the browser and activate the extension. The detected technologies are displayed automatically based on passive analysis of HTTP headers, cookies, and client-side resources. The results can be used to guide further, technology-specific testing steps in the web application layer.

### 4.3. Nmap
Install Nmap using the official installation instructions for the target operating system.
https://nmap.org/download.html

Use Nmap for:
- discovery of open ports,
- service and version identification,
- preliminary scoping of the web application attack surface.

### 4.4. Burp Suite
Download Burp Suite directly from the vendor.
https://portswigger.net/burp/communitydownload

The Community Edition is sufficient for interception, manual analysis and basic dictionary attacks.

Burp Suite is used by configuring it as an HTTP/HTTPS proxy between the browser and the target web interface. The client is configured to route its traffic through Burp’s local proxy listener, allowing all requests and responses to pass through the tool for inspection.

Use Burp Suite for:
- interception of HTTP and HTTPS requests and responses,
- inspection of request structure, headers, and parameters,
- controlled manipulation of requests prior to forwarding them to the server,
- observation of server‑side behavior in response to modified inputs
- basic dictionary attacks.

### 4.5. Nikto
Install Nikto according to the official installation instructions provided in the project repository.
https://github.com/sullo/nikto

Nikto is executed as a command‑line tool and requires no additional configuration for basic operation.

Use Nikto for:

- automated scanning of web servers,
- identification of common web server misconfigurations,
- detection of outdated server components,
- identification of known vulnerabilities and insecure default settings.

Nikto performs a series of checks based on known signatures, configuration issues, and publicly documented vulnerabilities associated with web servers and related components. The results provide an overview of server-side weaknesses that may warrant further manual validation. Findings from Nikto scans help prioritize manual testing activities and guide focused analysis of specific server behaviors, rather than serving as a sole source of vulnerability identification.

### 4.6. ffuf
Install ffuf according to the official installation instructions provided in the project repository.
https://github.com/ffuf/ffuf

Use ffuf for:
- discovery of hidden web resources and endpoints,
- fuzzing of request parameters and input locations,
- systematic testing based on predefined input dictionaries.

ffuf provides multiple options to control request execution and avoid unintended impact on the target system. Throttling mechanisms allow limiting the number of requests sent in a given time interval or restricting the level of parallelism during testing. These controls are used to ensure that testing remains stable, reproducible, and does not overwhelm the target service.

### 4.7. sqlmap
Install sqlmap according to the official installation instructions provided in the project repository.
https://github.com/sqlmapproject/sqlmap

Use sqlmap for:
- detection of SQL injection vulnerabilities,
- controlled confirmation of suspected injection points.

Within the proposed methodology, sqlmap is primarily used to validate potential injection points identified during earlier manual analysis. Rather than scanning entire applications blindly, it is applied to specific requests that have already been identified as relevant through interception and inspection.

sqlmap supports initiating scans based on captured HTTP requests stored in a request file. Such request files can be generated by saving intercepted traffic from Burp Suite during manual testing.

### 4.8. Dalfox
Install Dalfox according to the official installation instructions provided in the project repository.
https://github.com/hahwul/dalfox

Use Dalfox for:
- identification of cross-site scripting (XSS) vulnerabilities,
- analysis of reflected and dynamic input handling,
- confirmation of suspected XSS issues identified during manual testing.

### 4.9. Hydra
Install Hydra according to the official installation instructions provided in the project repository.
https://github.com/vanhauser-thc/thc-hydra

Use Hydra for:
- testing the robustness of web‑based authentication mechanisms,
- validation of credential handling logic,
- assessment of protections against repeated authentication attempts.

Hydra should be applied selectively to authentication endpoints that have already been identified through earlier reconnaissance and traffic inspection. Its use is limited to validating whether appropriate controls, such as rate limiting or account lockout mechanisms, are enforced by the web application.

### 4.10. SecLists

Download SecLists from the official project repository.
https://github.com/danielmiessler/SecLists

Use SecLists for:
- input dictionaries for endpoint and resource discovery,
- parameter and value fuzzing,
- authentication and credential testing.

Within the methodology, SecLists is used as a supporting resource for dictionary-based testing performed with other tools. Wordlists should be selected according to the testing context.

### 4.11. PayloadsAllTheThings

Download PayloadsAllTheThings from the official project repository.
https://github.com/swisskyrepo/PayloadsAllTheThings

Use PayloadsAllTheThings for:
- manual validation of input handling behavior,
- dictionary-based testing by supplying structured payload sets,
- support for confirmatory testing across different vulnerability classes.

## 5. Mobile application layer
Some tools mentioned below are specific for Android application testing. For iOS testing, a similar setup is required using platform-specific tools such as Xcode and class-dump or Hopper Disassembler to enable reverse engineering, code analysis, and debugging at a comparable level of detail.

This section describes the preparation of the mobile application testing workstation and Android testbed.

---
### 5.1. Required Tools

| Tool | Purpose |
|---|---|
| MobSF | Static and dynamic mobile application analysis |
| mitmproxy | HTTP/HTTPS traffic interception |
| Frida | Runtime instrumentation and function manipulation |
| Apktool | APK decompilation and recompilation |
| zipalign | APK alignment before signing |
| apksigner | APK signing and signature verification |
| Android Studio | Android SDK, emulator, ADB, debugging |
| jadx / jadx-gui | Decompiled source code review |
| Wireshark / tshark | Packet capture and packet analysis |
| Burp Suite | HTTP/HTTPS interception and API testing |
| OWASP ZAP | HTTP/HTTPS interception and API testing |
| TruffleHog | Scanning code for credentials |
| LinkFinder | Extracting URLs and endpoints from code |
| DeepLinkTester | Testing the app behaviour with deeplinks |
---

### 5.2. Android Studio, SDK and ADB
Install Android Studio using the official Android Studio installation instructions.  [Download and install](https://developer.android.com/studio/install)

During installation, include:

-   Android SDK Platform-Tools,
-   Android SDK Build-Tools,
-   Android Emulator,
-   Android command-line tools,
-   at least one Android system image.

ADB should be available from Android SDK Platform-Tools.

### 5.3. Android Test Device or Emulator

Dynamic testing requires one of the following environments:

|  Option| Notes |
|--|--|
| Physical non-rooted Android device | Closest to real user environment |
|Physical rooted Android device|Useful for Frida, system certificate installation, and filesystem access|
|Android Studio emulator|Easy to reset, snapshot, and reproduce|
|Genymotion emulator|Convenient for some dynamic analysis workflows|


Prepare the selected Android environment:

1.  Enable Developer Options.
2.  Enable USB Debugging.
3.  Connect the device to the workstation or start the emulator.
4.  Confirm that ADB detects the device.
5.  Record device and system details.

*Notes:*
- *Some emulators start with superuser privileges by default. This may affect test validity because some applications behave differently or refuse to run on rooted systems.*
- *The newest Android version working in emulator supported by MobSF is 11.0*

### 5.4. MobSF

Install MobSF according to the official MobSF installation instructions. [Official repository](https://github.com/mobsf/mobile-security-framework-mobsf)

Recommended deployment method:

-   Docker-based local installation.

Use MobSF for:

-   static APK analysis,
-   manifest review,
-   permission review,
-   exported component review,
-   hardcoded secret detection,
-   network endpoint identification,
-   dynamic analysis if a compatible Android environment is available.

*Note: MobSF dynamic analysis depends on the supported Android environment. Compatibility must be verified before testing. For this moment the newest Android version working in emulator supported by MobSF is 11.0*

### 5.5. Proxy Tools

At least one HTTP/HTTPS interception proxy must be prepared.

Recommended tools:

-   Burp Suite [Download and install](https://portswigger.net/burp/documentation/desktop/getting-started/download-and-install),
-   OWASP ZAP [Download and install](https://www.zaproxy.org/download/),
-   mitmproxy [Download and install](https://docs.mitmproxy.org/stable/overview/installation/).

Install each tool according to its official installation instructions.

Proxy listener requirements:
| Setting | Recommendation |
|--|--|
| Listener address | Workstation IP address or all interfaces |
|Listener port|Commonly `8080`|
|Android proxy mode|Manual Wi-Fi proxy or VPN/transparent routing|
|CA certificate|Installed on Android where applicable|

Proxy preparation steps:

1.  Start the selected proxy tool.
2.  Configure the proxy listener.
3.  Configure Android Wi-Fi proxy settings.
4.  Install the proxy CA certificate on Android.
5.  Confirm browser HTTP traffic is visible.
6.  Confirm browser HTTPS traffic is visible.
7.  Start the target application.
8.  Confirm whether target application traffic is visible.

*Notes:*
- *Newer Android applications may not trust user-installed CA certificates. Some applications also use certificate pinning, which can prevent HTTPS interception.*
- *Authors were using all three tools, but the most universal and useful was Burp Suite*

### 5.6. Frida

Install Frida tools according to the official Frida installation instructions. [Download and install](https://frida.re/docs/installation/)

Frida is used for:

-   runtime instrumentation,
-   function tracing,
-   observing application behavior,
-   modifying application behavior during testing,
-   supporting analysis of root detection, emulator detection, and certificate validation logic.

For rooted devices or compatible emulators, install the matching `frida-server` binary for the Android CPU architecture.

*Note: The simplest Frida setup requires a rooted device or emulator. Non-root setups are possible with Frida Gadget, but they require APK modification and may affect application behavior.*

### 5.7. Apktool, jadx, zipalign and apksigner

Install the following tools according to their official installation instructions:
 - Apktool [Download and install](https://apktool.org/docs/install/)
- jadx [Official repository](https://github.com/skylot/jadx)

| Tool | Purpose |
|--|--|
| Apktool | Decode and rebuild APK resources |
| jadx / jadx-gui | Review decompiled Java/Kotlin-like source code |
| zipalign | Align APK before signing |
| apksigner | Sign and verify APK files |

Tools `zipalign` and `apksigner` are included in Android SDK Build-Tools.

Use these tools to prepare for:

-   AndroidManifest.xml review,
-   resource review,
-   decompiled code review,
-   APK rebuilding if modification is required,
-   APK signing for installation on the test device.


### 5.8. Wireshark and tshark

Install Wireshark and tshark according to the official Wireshark installation instructions or the operating system package manager. [Download and install](https://www.wireshark.org/download.html)

Use Wireshark or tshark to capture and analyze:

-   DNS traffic,
-   HTTP/HTTPS metadata,
-   MQTT traffic,
-   CoAP traffic,
-   UDP keep-alive traffic,
-   local device communication,
-   cloud communication.

Capture targets may include:

| Interface | Purpose |
|--|--|
| Wi-Fi interface | Mobile device or IoT network traffic |
| Emulator interface | Emulator traffic |
| Lab gateway interface | Full network capture |
| Loopback interface | Local proxy or local tool traffic |

### 5.9. Android Logs

Prepare Android log collection using ADB logcat.

Use logs to support analysis of:

-   API errors,
-   certificate validation errors,
-   root detection,
-   emulator detection,
-   pairing issues,
-   Bluetooth or Wi-Fi errors,
-   local communication errors,
-   application crashes.

Before each test scenario:

1.  Clear existing logs.
2.  Start log capture.
3.  Perform the test scenario.
4.  Stop log capture.
5.  Save logs in the project notes directory.

### 5.10. TruffleHog
Install TruffleHog according to the official TruffleHog installation instructions. [Official repository](https://github.com/trufflesecurity/trufflehog)

TruffleHog is used to scan application-related files for exposed credentials and secrets.

Use TruffleHog to check:

- decompiled source code,
- extracted APK resources,
- configuration files,
- bundled assets,
- JavaScript files,
- documentation included in the application package,
- repository content, if source code access is in scope.

*Note: Findings from secret scanning must be verified carefully. Some values may be test data, public identifiers, inactive credentials, or false positives.*

### 5.11. LinkFinder

Install LinkFinder according to the official LinkFinder installation instructions.  [Official repository](https://github.com/GerbenJavado/LinkFinder)

LinkFinder is used to extract URLs, routes, and endpoints from application code and bundled files.

Use LinkFinder against:

- decompiled Java/Kotlin-like code,
- JavaScript files,
- HTML files,
- configuration files,
- assets extracted from the APK,
- MobSF or jadx output directories.

*Note: Extracted endpoints should not be tested unless they are covered by the engagement authorization.*

### 5.12. DeepLinkTester

Install DeepLinkTester from the Play Store or source apk. [Play Store](https://play.google.com/store/apps/details?id=com.app.deeplinktester)

DeepLinkTester is used to test how the Android application handles deeplinks, custom URL schemes, Android App Links, and intent-based entry points.

Use DeepLinkTester to verify:

- custom scheme handling,
- Android App Links handling,
- exported activities reachable through deeplinks,
- authentication requirements for deeplinked actions,
- parameter validation,
- unsafe redirects,
- account takeover or session manipulation risks,
- unexpected application state changes.

*Note: Deeplink testing may trigger application actions such as login flows, device pairing, account linking, or configuration changes. Use only dedicated test accounts and authorized test devices.*

### 5.13. Troubleshooting and possible limitations

Common limitations and challanges:
| Limitation | Possible Impact |
|--|--|
| Rooted device | Application may block execution or behave differently|
| Emulator environment | Application may detect emulator and refuse to run |
| User-installed CA certificate | Application may not trust the certificate |
| Certificate pinning | HTTPS interception may fail |
| APK re-signing | Application integrity checks may fail |
| Unsupported Android version | Dynamic analysis may not work |
| Obfuscated code | Decompiled source code may be difficult to analyze |
| Non-HTTP protocols | Proxy tools may not show complete communication |



## 6. Cloud and internal servers layer

The tools are similar to those presented in [Section 3](#3-ip-layer) concerning IP networks and [Section 4](#4-web-application-layer) converning web applications, because of that, here we provide only those that require additional attention.

### 6.1. Proposed Tools

| Tool                | Purpose                                           |
| ------------------- | ------------------------------------------------- |
| Scout Suite         | a cloud security auditing tool that supports various cloud providers to highlight misconfigurations, it can be used to perform tests of self-deployed servers for IoT rather than for tests of cloud deployed by IoT devices’ vendors |
| Postman | a tool for API creation and testing, it allows for crafting and sending custom API requests, enabling exploration of undocumented endpoints, testing authentication flows, and verifying input validation |
| Burp Suite, OWASP ZAP | in the context of this layer, can be used to intercept and manipulate API requests, allowing data tampering, to intercept, modify, and replay HTTP requests |