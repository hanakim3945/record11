# record11

**Proof of Concept: Apple Activation Record Generation via 11-Digit Serial Numbers**

This repository explores the concept of generating Apple activation records using arbitrary 11-digit serial numbers (SNs).

Apple's system currently accepts a wide range of SN formats, including non-standard combinations.

###  Update: 28th August 2025

Apple patched the ticket generation method for all devices including iPhone 5S - 16 Pro Max.

---

## Usage

Install the php script on a webserver of your choice and run it with the required parameters in a get request.

```jsx
<URL>/ticket.php?sn=<SerialNumber>&udid=<UniqueDeviceID>&ucid=<UniqueChipID>
```

---

## Why This Works

Apple’s Albert activation server has to provide activation records for any apple device, no matter this device being a decade old or a very recent model. Older models no longer getting updates, such as iPad 2 `iPad2,1`, iPod touch 5 `iPod5,1` or `Watch2,1` or even more.

They stuck on iOS 9 with old mobileactivationd. While the newer devices using improved version of MobileActivation with apple drm and other protection mechanisms, these old devices use the legacy method of fetching activation records with just a simple request to `albert.apple.com/deviceActivation`. We can mess with any parameter in the ActivationInfoXML, sign it with a private-key matching the FairplayCertChain and send it to albert.

In our POC we use this weakness to generate activation records for all those SNs. The acquired records can be used to manually activate devices by placing the activation records directly in the filesystem. This works up to iOS 18 just fine.  

---

## 🔍 Current Findings

### ✅ Valid Serial Number Formats

Apple’s activation server accepts **nearly any 11-digit SN** with the following characteristics:

- **Characters allowed:** `A-Z`, `0-9`, and selected Chinese characters (e.g., 桃 *tao*).
- **12-digit SNs** also work, inheriting structures from older legitimate Apple SNs.
- UDID and ECID can be chosen randomly. In case you want to activate a device manually with it, you obviously need to generate a ticket with the matching parameters.
- ActivationRandomness is indeed random, it can represent any string

### Serial Number Patterns

The following patterns have been successfully tested for activation record generation:

```
...DVD1
...DVD2
...F196
...F194
...F193
```

*(These endings are commonly found in legitimate Apple SNs.)*

### Special Serial Numbers with valid records

`XXXXXXXXXXX` → `xxxxxxxxxxx_activation_record.plist`

`桃桃桃桃桃桃桃桃桃桃桃` → `Peach桃_activation_record.plist`

`哈哈哈哈哈哈哈哈哈哈哈` → `Haha哈哈_activation_record.plist`

`00000000000` → `00000000000_activation_record.plist`

`11111111111` →  not working for some unknown reason

`88888888888` → seems to work, but it has icloud lock :)

There are many more funny possibilities !

---

## Deprecated

A few months ago, this method also worked with **iPhone 2G serial numbers** (11-digit format) and SNs of other iPhones. The returned record did not contain the wildcard information, which is required to activate cellular functionality on iPhones and cellular iPads. However, for activation the given record is enough.

**Apple has since patched this loophole for iPhones and cellular iPads**, but it may remains functional for other devices.

---

## Disclaimer

This POC is for **educational purposes only**.

- Do **not** use this for illegal activities.
- Apple may patch this behavior at any time.
