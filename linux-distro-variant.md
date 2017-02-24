Thank you for your contribution and time.

Unfortunately this is not something we want to support and maintain. For most users, just running "<insert-image-name-here>" at a specific version fulfills all their requirements. They don't need to care about the underlying distribution it is running within. If they are running their own application, in python for example, and they are unable to run it in our Debian, Alpine, or Windows Server Core based python images, then they are free to install python and their required dependencies within CentOS, Ubuntu, or other base distribution image.

For many images here are the variants that we maintain with short descriptions and why we have them:

 - `debian`
   - what: the default image that is based on [Debian](https://www.debian.org/) but doesn't usually say `debian` in the name
   - why: the smallest mainline distro using glibc; stable set of libraries provided though `apt`; we have familiarity with Debian; it is similar enough to Ubuntu for most users
 - `slim`:
   - what: a smaller version of the Debian based image; to make it really easy to use, many of the language images include commonly used libraries and development headers; this variant doesn't install them
 - `stretch` (or `wheezy`)
   - what: Debian based image built on the next (or previous) stable [release of Debian](https://www.debian.org/releases/)
   - why: to help transition major base image bumps without affecting users drastically
 - `alpine`
   - what: based on the [Alpine Linux](https://alpinelinux.org/) image (which itself is only about 4-5MB)
   - why: gives users an even smaller image to base their software on; caveat is that not everything is libc agnostic and may not work with musl libc
 - `windowsservercore`
   - what: Windows based image that only runs on Windows; based on [`microsoft/windowsservercore` image](https://hub.docker.com/r/microsoft/windowsservercore/)
   - why: allow users to run software written for Windows in a container
 - `nanoserver`
   - what: smaller Windows based image that only runs on Windows; based on [`microsoft/nanoserver`](https://hub.docker.com/r/microsoft/nanoserver/) (base image ~333MB vs ~5GB)
