# Setup a local account during Windows setup

(Tested on Windows 10)

When setting up a fresh install of Windows 10, by default, and without a way to bypass, it will try and set you up a computer account directly connected to your Microsoft Account. This might not be what you want, if for example setting up a GitHub Actions Runner. If setting Windows up in a VM, or on a physical machine, you need to disconnect your internet connection before getting to the account setup step - so best to do this at the start. In a VM, disconnect the cable, or on your main machine, enable airplane mode, or disable WiFi. You will then be prompted to setup a "local" account, which just asks for the name of the user and a password.
