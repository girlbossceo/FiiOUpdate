## infodumps
infodumps that dont need their own dedicated place

### AndroidManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:sharedUserId="android.uid.system" android:versionCode="29" android:versionName="10" android:compileSdkVersion="29" android:compileSdkVersionCodename="10" coreApp="true" package="com.android.fiioupdate" platformBuildVersionCode="29" platformBuildVersionName="10">
    <uses-sdk android:minSdkVersion="29" android:targetSdkVersion="29"/>
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
    <uses-permission android:name="android.permission.WAKE_LOCK"/>
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
    <uses-permission android:name="android.permission.CHANGE_WIFI_MULTICAST_STATE"/>
    <uses-permission android:name="android.permission.CHANGE_WIFI_STATE"/>
    <uses-permission android:name="android.permission.CHANGE_NETWORK_STATE"/>
    <uses-permission android:name="android.permission.REBOOT"/>
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED"/>
    <uses-permission android:name="android.permission.WAKE_LOCK"/>
    <application android:theme="@style/AppTheme" android:label="@string/app_name" android:icon="@drawable/icon_fiioupdate" android:exported="true" android:allowBackup="true" android:extractNativeLibs="false" android:networkSecurityConfig="@xml/network_security_config" android:appComponentFactory="androidx.core.app.CoreComponentFactory" android:usesNonSdkApi="true" android:requestLegacyExternalStorage="true">
        <activity android:theme="@style/main_activity_style" android:label="@string/app_name" android:name="com.android.fiioupdate.activity.OtaUpdateActivity" android:excludeFromRecents="true" android:launchMode="singleTask">
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>
                <category android:name="android.intent.category.DEFAULT"/>
            </intent-filter>
        </activity>
        <activity android:theme="@style/main_activity_style" android:name="com.android.fiioupdate.activity.UpgradeActivity"/>
        <activity android:theme="@style/main_activity_style" android:name="com.android.fiioupdate.activity.BetaUpdateActivity"/>
        <receiver android:name="com.android.fiioupdate.online.receiver.SystemUpdateReceiver">
            <intent-filter>
                <action android:name="android.intent.action.LOCKED_BOOT_COMPLETED"/>
                <action android:name="android.intent.action.BOOT_COMPLETED"/>
            </intent-filter>
            <intent-filter>
                <action android:name="android.net.conn.CONNECTIVITY_CHANGE"/>
            </intent-filter>
        </receiver>
        <service android:name="com.android.fiioupdate.online.service.SystemUpdateService" android:permission="android.permission.BIND_JOB_SERVICE" android:exported="true"/>
        <service android:name="com.android.fiioupdate.online.service.DownloadService"/>
        <service android:name="com.android.fiioupdate.online.service.UpdateNetworkService"/>
        <uses-library android:name="javax.obex" android:required="true"/>
        <service android:name="androidx.room.MultiInstanceInvalidationService" android:exported="false"/>
    </application>
</manifest>
```

interesting takeaways from this:
- the updater app shares the android system UID (`android:sharedUserId="android.uid.system"`) which is quite bad and ugly..
- there's mentions of a beta update channel that we'll discover later on
- there's a network security config which you would ASSUME is for actual security purposes, but i have some bad news for you: that file is to declare `cleartextTrafficPermitted="true"` 😭

### network_security_config.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <base-config cleartextTrafficPermitted="true"/>
</network-security-config>
```

you'll discover why this is necessary

### APK signature (inc fingerprint)
```

APK signature verification result:

Signature verification succeeded

Valid APK signature v3 found

Signer 1

Type: X.509
Version: 3
Serial number: 0xb6b44832761ccdf3
Subject: EMAILADDRESS=pengweizhong@fiio.net, CN=Pengweizhong, OU=FiiO, O=FiiO, L=GuangZhou View, ST=GuangDong, C=CN
Valid from: Sun Jul 12 21:23:55 EDT 2020

Valid until: Thu Nov 28 20:23:55 EST 2047
Public key type: RSA
Exponent: 65537
Modulus size (bits): 2048
Modulus: 25313198632852982939958098622681209967522161608759932952402416040875542882742293757769532859209155599467879714179460274678408558214110862998357984877413926520899553826787628306966566490574106986193761295027753854125281597162486978832762692114859892522695423125681228614636519128480973158201882017685189023226416521678591404729421774178428367653014643843937067296811825114411517068318678426800832223788429810148891702893914490769287437185830373016400671221049089387939280934839603751491326847153595887766927974394131887777522942655883778620469477828216666038985323064971914640499151004492290879888130984996899653876777

Signature type: SHA256withRSA
Signature OID: 1.2.840.113549.1.1.11

MD5 Fingerprint: 11 EC 1D 4D F1 32 DD A8 A5 69 5E 75 7E 1A 25 34 
SHA-1 Fingerprint: FF B3 E5 AE 14 7A AB 8C DC 5B B0 27 AC B6 29 9A 9D 93 1A BE 
SHA-256 Fingerprint: A6 14 3C 4F 6B 48 32 B2 D8 FC E0 38 E5 2A 86 5E 86 22 61 80 98 E1 4E DC 64 73 36 2D 20 48 CB 9D 
```

we will never know who is pengweizhong@fiio.net, but an open google search does yield a mention at https://github.com/FiiOapp/FiiO_Kernel_M3K/blob/43f4271dcbc6aee9846de6818e7286722ffb852a/drivers/staging/dwc2/fiio_wakeup.c#L271 under the interesting github account "https://github.com/FiiOapp/".

there's some kernel sources for older devices, but nothing for the more modern devices like the M15 or M17. there's also literally zero useful commit history, but all seem to be committed by the user "willson@fiio.net".

the cited repos and user above can be confirmed that they are indeed owned by FiiO by this XDA post where someone contacted FiiO support regarding root: https://forum.xda-developers.com/t/custom-firmware-for-fiio-m6-fiio-m7-and-fiio-m9.4245917/page-3#post-85042749

however these kernel trees may not be complete: https://github.com/FiiOapp/FiiO_Kernel_Android_M6-M7-M9/issues/1

anyways enough of OSINT stuff