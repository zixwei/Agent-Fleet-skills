# Mobile reverse engineering

Mobile work splits into static inspection and runtime confirmation. Static work finds the surface: exported components, deep links, pinning, storage, keys. Runtime work proves which branch runs and what the server accepts.

Use a lab device or emulator with snapshots. Keep one clean device for "normal user" traffic and one instrumented device for hooks.

## Android

### Unpack

```bash
unzip -l app.apk
apktool d app.apk -o app-src
jadx-gui app.apk
```

`apktool` gives resources and smali. `jadx` gives Java-like source. Read jadx first, fall back to smali when obfuscated.

Check the manifest:

```bash
apktool d app.apk -o app-src
xmllint --format app-src/AndroidManifest.xml | less
```

Look for:

- `android:debuggable="true"`
- `android:allowBackup="true"`
- `android:usesCleartextTraffic="true"`
- exported activities, services, receivers, providers
- deep links and intent filters
- permissions, especially SMS, contacts, location, overlay, all files
- network security config
- backup rules

### DEX and native code

```bash
jadx -d out app.apk
grep -RIn "http://\|https://\|api_key\|secret\|BEGIN RSA" out/
unzip -l app.apk | grep -E "lib/|assets/"
```

Native libraries live under `lib/<abi>/`. Treat them as ELF binaries. A JNI bridge is a common place to find weak validation.

Flutter apps hide Dart code in `libapp.so`. React Native hides the bundle in `assets/index.android.bundle`. Both need dedicated tooling:

- Flutter: `flutter_elt`, `reFlutter`, Blutter.
- React Native: the JS bundle is often readable, or Hermes bytecode needs `hbctool`.

### Runtime

```bash
frida-ps -U
frida -U -f com.example.app -l hook.js --no-pause
objection -g com.example.app explore
```

Objection commands:

```text
android root detect
android sslpinning disable
android hooking list activities
android hooking watch class_method com.example.app.Login.checkPassword --dump-args --dump-return
```

Hook templates:

```javascript
Java.perform(function () {
  var Login = Java.use("com.example.app.Login");
  Login.checkPassword.implementation = function (user, pass) {
    console.log("user=" + user + " pass=" + pass);
    var r = this.checkPassword(user, pass);
    console.log("result=" + r);
    return r;
  };
});
```

### Storage and crypto

```bash
adb shell run-as com.example.app ls -R /data/data/com.example.app
adb backup -f app.ab com.example.app
```

Look for SQLite databases, shared preferences, files, and the keystore. A key in `strings.xml` or a Dart snapshot is not necessarily a secret; a keystore alias with no user auth is.

Crypto checks:

- ECB mode with repeated blocks.
- Static IV.
- Hardcoded key or password.
- `SecureRandom` seeded with time.
- Custom XOR that claims to be encryption.
- Certificate pinning implemented in Java, native, or both.

### Deep links

```bash
adb shell am start -W -a android.intent.action.VIEW -d "app://path?x=1" com.example.app
```

Test path traversal, open redirect in a WebView, token in the URL, and exported components that accept a `PendingIntent` or a `FileProvider` URI.

### Android pitfalls

- Split APKs need all parts installed; a base APK alone is incomplete.
- Obfuscation moves the bug to native code or reflection. Use jadx xrefs and Frida.
- Pinning can be in native code. Hook the verifier, not only the Java layer.
- Emulator checks are common. Use a device profile that passes or hook the check.
- A debuggable build is not a finding by itself.

## iOS

### Static

```bash
unzip -l app.ipa
plutil -p Payload/App.app/Info.plist
otool -hL Payload/App.app/App
class-dump Payload/App.app/App > classes.h
```

Check:

- `NSAppTransportSecurity` exceptions
- URL schemes
- `UIFileSharingEnabled`
- keychain access groups
- entitlements
- embedded frameworks
- Swift symbols and Objective-C class names

Decrypt the binary on a jailbroken device with `frida-ios-dump` or `bagbak` before deep static work. App Store binaries are encrypted.

### Runtime

```bash
frida -U -f com.example.app -l hook.js --no-pause
objection -g com.example.app explore
```

Objection:

```text
ios sslpinning disable
ios keychain dump
ios nsuserdefaults get
ios hooking list classes
```

Hook Objective-C methods with the `-` and `+` prefixes for instance and class methods. Swift methods need the mangled name or a Swift-aware tool.

### iOS pitfalls

- Pinning uses `SecTrustEvaluate`, `URLSession` delegates, or a third-party library.
- Keychain items survive app deletion; a stale token can still work.
- ATS exceptions on one domain do not cover another.
- Swift name mangling makes hooks brittle. Prefer the class and selector when available.

## Cross-platform storage

| Store | Android | iOS |
|---|---|---|
| Preferences | SharedPreferences | NSUserDefaults |
| Database | SQLite, Room | Core Data, SQLite |
| Files | Internal and external | Documents, Caches |
| Secrets | Keystore | Keychain, Secure Enclave |
| Cookies | WebView cookie store | WKWebsiteDataStore |

A token in SharedPreferences is readable on a rooted device. A token in the keystore with user authentication is stronger.

## API testing from mobile

Copy the mobile request into a proxy and replay it without the app.

- Keep the mobile headers: `X-Platform`, `X-App-Version`, device ID, attestation token.
- Test whether server-side authorization still holds when the app checks are removed.
- Check older API versions used only by old app builds.
- Test deep-link parameters against the API directly.

Play Integrity and App Attest are server-side gates. A replayed request with a stale attestation token may pass; a scripted request may not.

## Reporting

Include:

- app version and build number;
- APK or IPA hash;
- device and OS version;
- the component or method;
- the hook or `am start` command;
- the server response;
- what an attacker gains.

A debuggable flag is low. An exported provider that leaks files is high. A hardcoded key is only as good as what it unlocks.

## Sources

- OWASP MASTG
- `mukul975/anthropic-cybersecurity-skills` jadx, Frida, iOS skills
- `SimoneAvogadro/android-reverse-engineering-skill`
- MobSF documentation
- Frida and objection documentation
- Android security model documentation
