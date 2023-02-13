Source: https://superdanby.github.io/Blog/signing-kernel-modules-for-secure-boot.html
Accessed: 2023-02-13

---

# Preparation
Make sure your Secure Boot is off.
In order to sign the modules, they should **exist** in the current environment.
Turning on Secure Boot will block the modules to load in the first place.

# Key Creation

## Create Signing Keys
`openssl req -new -x509 -newkey rsa:2048 -keyout MOK.priv -outform DER -out MOK.der -nodes -days 36500 -subj "/CN=Descriptive name/"`
*Descriptive name* is the name of the key. E.g. `"/CN=Yee~"`

## Sign the Module
`sudo /usr/src/kernels/$(uname -r)/scripts/sign-file sha256 ./MOK.priv ./MOK.der $(modinfo -n ` *MODULE_NAME* ` )`
Note that you can sign multiple modules at a time. Just repeat this step until all modules are signed.

## Register the Keys with Machine Owner Key
`sudo mokutil --import MOK.der`
You'll be prompt to enter a password for later use in MOK.

## Reboot
`reboot`

# MOK Enrollment

1.  Press anykey to enter MOK within 10 seconds.
2.  Enroll MOK
3.  Continue
4.  Insert the password you just created.
5.  OK

**[More with MOK](https://superdanby.github.io/Blog/managing-signed-keys-with-machine-owner-key.html)**

# Check if the Module is Signed
`mokutil --list-enrolled`

# Done!
You can now turn Secure Boot back on!
