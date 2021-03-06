# Installing

On OS X 10.10 (Yosemite) and up, to load `HookCase.kext` you'll need
to turn off Apple's protection against loading unsigned or
"inappropriately" signed kernel extensions.  (From Apple's point of
view, the only "appropriately" signed kernel extensions are those
signed with a special kernel extension signing certificate -- in
practice almost exclusively Apple's own kernel extensions.)  On OS X
10.11 (El Capitan) and up, to do this you'll need to turn off
"rootless mode".  Rootless mode will also prevent you from copying the
`HookCase.kext` binary to its final destination.

## On OS X 10.10:

1. From the command line run `nvram boot-args` to see if you already
   have some boot-args.  Then run the following command:

        sudo nvram boot-args="<existing-boot-args> kext-dev-mode=1"

2. Reboot your computer.

## On OS X 10.11 and up:

1. Boot into your Recovery partition by restarting your computer and
   pressing `Command-R` immediately after you hear the Mac startup
   sound.  Release these keys when you see the Apple logo.

2. Choose Utilties : Terminal, then run the following at the command
   line:

        csrutil disable

3. Quit Terminal and reboot your computer.

Now copy `HookCase.kext` to the `/usr/local/sbin/` directory.  One way
to do this is with the following command:

`sudo cp -R HookCase.kext /usr/local/sbin`

To load `HookCase.kext` into the kernel, do the following on the
command line:

`sudo kextutil /usr/local/sbin/HookCase.kext`

Because it won't have been signed using a kernel extension signing
certificate, you'll see the following error (or something like it):

        Diagnostics for HookCase.kext:
        Code Signing Failure: code signature is invalid
        kext-dev-mode allowing invalid signature -67050 0xFFFFFFFFFFFEFA16
          for kext "HookCase.kext"
        kext signature failure override allowing invalid signature -67050
          0xFFFFFFFFFFFEFA16 for kext "/usr/local/sbin/HookCase.kext"

Run `kextstat` to see that it did load.

To unload `HookCase.kext` from the kernel, run the following command:

`sudo kextunload -b org.smichaud.HookCase`

HookCase supports the release, development and debug kernels.  But if
you use it with the debug kernel, we recommend increasing the kernel
stack size.  One way to do this is as follows.  `kernel_stack_pages`
defaults to 4.

1. Copy `kernel.debug` (from the appropriate Kernel Debug Kit) to
   `/System/Library/Kernels`.

        sudo nvram boot-args="kcsuffix=debug kernel_stack_pages=6"

2. Reboot your computer.

Without this change, you sometimes get kernel panics using the debug
kernel, at least on OS X 10.11 (El Capitan).  These are usually
double-faults with `CR2` set to an address on the stack (indicating a
stack underflow).
