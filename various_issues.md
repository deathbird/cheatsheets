Error: "ImportError: libffi.so.6: cannot open shared object file: No such file or directory"

When trying to manually/auto pip install a python dependency. In my case error was triggered when executing inside a pyenv virtual environment
the `git commit` command which subsequently triggers pre-commit checks which tried to be installed (pip) in the virtual env.

Solution:
This happens because my Ubuntu 20.04.LTSv3 has installed libffi.so.7 while the extension expects libffi.so.6. Following
[this guide](https://github.com/microsoft/vscode-postgresql/issues/77), from [this answer](https://github.com/microsoft/vscode-postgresql/issues/77#issuecomment-818506089)
I understood libffi6 is a PPA package. So I also followed [this article](https://askubuntu.com/questions/639493/how-to-find-which-ppa-contains-a-specific-package)
guidelines to locate in PPA (Launchpad) the package:

1. Search in launchpad for the package: https://launchpad.net/ubuntu/+search?text=libffi6. It returns package libffi.
2. Open the search result for libffi and locate in the downloadable packages the latest Ubuntu version that contains the libffi6 package we want (for my machine architecture wich is amd64). This is Bionic Beaver (Ubuntu 18.04) https://launchpad.net/ubuntu/bionic/+source/libffi
3. Under Bionic Beaver, Binary packages locate the architecture link (amd64) for libffi6 and click it to download. Since it's a deb file it is downloaded under /tmp.
4. Install it `sudo apt install /tmp/libffi6_3.2.1-8_amd64.deb` and verify with `sudo apt info libffi6`
5. After that upgrade pip `pip install --upgrade pip`, it works like a charm as does `git commit` which failed initially!
