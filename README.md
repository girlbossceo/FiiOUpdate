so a while ago i bricked my FiiO M17 because i didn't do backups and i rooted it and did stuff. there's no TWRP.

i was really mad their OTA and factory images are in some weird proprietary format that i can't flash using fastboot to recover easily from a factory image. decided to get a replacement, but i was allowed to keep the "broken" one.

now i wanted to get it fixed so i have two M17s!!!!. i decided to look into how i can get the real flashable factory images. since there's very little research on the M17, mainly due to its price and how new it is, i decided to publish some stuff. part of this journey was reverse engineering the stock FiiO updater app (com.android.fiioupdate) that appears to be shared with the M11 and M17.

you can also reference my data dumps of the M17 here i did some months ago: https://github.com/r3g-5z/fiio-m17-data

the app id for this package is `com.android.fiioupdate` which they decided to use a domain they don't own as part of their package name.