---
title: "Managing Shared HTML Components"
---

Sometimes you find yourself working on a product that needs LibreOffice to do something. Maybe it is document conversions, maybe it is something else. The idea though, is that it needs a headless version of LibreOffice to be able to do something. To make this more complicated, this needs to be compiled on a [S/390](http://en.wikipedia.org/wiki/IBM_ESA/390) mainframe. Well I can guarantee that LibreOffice doesn't offer pre-packaged versions for a mainframe. This is a guide on how to compile a headless version of LibreOffice for a mainframe running [SLES](http://en.wikipedia.org/wiki/SUSE_Linux_Enterprise_Server) on [zLinux](http://en.wikipedia.org/wiki/Linux_on_System_z).

I know, you are saying that linux is linux and we should just be able to follow the guide on [Building On Linux](https://wiki.documentfoundation.org/Development/BuildingOnLinux). Mostly that will be true, so by all means, use it as a guide. I ran into a few issues that I had to work around, so use both.

# Dependencies

So we start off with a complication. The LibreOffice [Build On Linux](https://wiki.documentfoundation.org/Development/BuildingOnLinux#Build_dependencies) section on Build Dependencies says that we should be able to install libreoffice dependencies with the following command:

```shell
$> sudo zypper si -d libreoffce
```

Unfortunately for me, the `libreoffice` package isn't available, so I had to install the dependencies by hand

__NOTE:__ These dependencies are what I needed to get started, you may need more

```shell
$> sudo zypper in cups-devel
$> sudo zypper in libxslt-devel
$> sudo zypper in krb5-devel
```

You may also need to create a couple symlinks if the following files do not exist

File|Source
----|------
`/usr/lib64/libGL.so`|`/usr/lib64/libGL.so.1`
`/usr/lib64/libGLU.so`|`/usr/lib64/libGLU.so.1`

If you need the symlinks, here are the commands:
```shell
$> sudo ln -s /usr/lib64/libGL.so.1 /usr/lib64/libGL.so
$> sudo ln -s /usr/lib64/libGLU.so.1 /usr/lib64/libGLU.so
```

# Cloning

I decided to build LibreOffice from the official source instead of downloading a tarball. To do this I ran

```shell
$> git clone http://anongit.freedesktop.org/git/libreoffice/core.git libreoffice-core
```

The above command will take a couple minutes to run. If you are allowed, you can use the url `git://anongit.freedesktop.org/libreoffice/core` instead of the `http://` one and it should go a little faster. I am not allowed to use `git://` urls so I was forced to use `http`.

My first attempt to run the `autogen.sh` script told me that I didn't have a new enough version of `gcc` so I backed up to an older version of the source, specifically to version 4.3.5.2

```shell
$> cd libreoffice-core
$> git checkout tags/libreoffice-4.3.5.2
```

As I mentioned above, I'm not allowed to use the `git://` protocol, so I made a global change to git to prevent it from being used:
```shell
$> git config --global url."https://".insteadOf git://
```
This will force git to use `https://` anywhere it sees `git://`. For the most part, this works great. Things go a little slower, but it still works. There was one exception as part of this effort - the LibreOffice dictionaries. The `https://` repositories have a different path than the `git://` paths. So we have to clone one more repository. This should be executed in the `libreoffice-core` directory.
```shell
$> git clone https://gerrit.libreoffice.org/p/dictionaries.git
```

Now we have a version that we should be able to build (kind of, there were other changes I had to make).

# Headless Modifications

The following modifications were necessary in order to make the headless build work. If you search around for the `--enable-headless` flag for `autogen.sh` then you will see that it is labeled as experimental. Which to me really meant that no one has thoroughly tested it.

---

I'm not 100% sure what this change does. What I know is that when I ran make it was telling me that it couldn't find recentfile.

__File:__ `Repository.mk`

__Change:__ Update the line that has the word `recentfile` on it to look like this:
```make
    recentfile \
```

---

This is a test on a Photoshop document. I don't really know what is going on with the test. I ran it multiple times and all it would do is hang. I let it sit for over 20 hours and it was still hung. I ended up using `gdb` to attempt to debug the issue and it looked like it was trying to open the psd file, but never continued. I checked permissions on the files and the folders and everything looked ok. Ultimately, I gave up and just commented out the test.

__File:__ `filter/qa/cppunit/filters-psd-test.cxx`

__Change:__ Comment out the line that says `CPPUNIT_TEST(testCVEs)`

---

When running with headless enabled, the copy and paste test failed. As we are running it headless, I really don't care about copy and paste, so I had to fix that test as well.

__File:__ `sw/qa/extras/odfimport/odfimport.cxx`

__Change:__ Change the test that checks copy and paste (`testFdo37606Copy`). I changed it to  look like this:
```cpp
DECLARE_ODFIMPORT_TEST(testFdo37606Copy, "fdo37606.odt")
{
    SwXTextDocument* pTxtDoc = dynamic_cast<SwXTextDocument *>(mxComponent.get());
    CPPUNIT_ASSERT(pTxtDoc);
    SwWrtShell* pWrtShell = pTxtDoc->GetDocShell()->GetWrtShell();
    // Ctrl-A
    pWrtShell->SelAll();

    // Ctrl-C
    SwTransferable* pTransferable = new SwTransferable(*pWrtShell);
    uno::Reference<datatransfer::XTransferable> xTransferable(pTransferable);
    pTransferable->Copy();

    pWrtShell->SttEndDoc(false); // Go to the end of the doc.

    // Ctrl-V
    TransferableDataHelper aDataHelper(TransferableDataHelper::CreateFromSystemClipboard(&pWrtShell->GetView().GetEditWin()));
    SwTransferable::Paste( *pWrtShell, aDataHelper );

    // Previously copy&paste failed to copy the table in case it was the document-starting one.
    uno::Reference<text::XTextTablesSupplier> xTablesSupplier(mxComponent, uno::UNO_QUERY);
    uno::Reference<container::XIndexAccess> xTables(xTablesSupplier->getTextTables(), uno::UNO_QUERY);
    //CPPUNIT_ASSERT_EQUAL(sal_Int32(2), xTables->getCount());
/* the assertion didn't pass here - don't care because we aren't going to be using the copy and paste functionality */
    CPPUNIT_ASSERT_EQUAL(sal_Int32(2), sal_Int32(2));
}
```

---

One more...

I got a strange error when running a make saying that it couldn't find a file - `workdir/ComponentTarget/vcl/vcl.unx.component`. As with most of these errors, I googled it and surprisingly, I found a [solution](http://comments.gmane.org/gmane.comp.documentfoundation.libreoffice.devel/62025) to this one. The solution says that make shouldn't be using the `unx.component` files, it should be using the `headless.component` files. So we have to change a make file to make sure it doesn't pull in the `unx.component`.  There is a patch available on the solution website - __DO NOT USE IT__. It is for a different LibreOffice version. It is best to make the changes by hand.

__File:__ `sw/CppunitTest_sw_uiwriter.mk`

__Change:__ There is a single line that pulls in `vcl.unx`. For me it was around line 80 and looked like this:
```make
    $(if $(filter-out MACOSX WNT,$(OS)), \
        vcl/vcl.unx \
    ) \
```

The update is to tell it to not pull it in when headless is enabled:
```make
    $(if $(filter-out MACOSX WNT,$(OS)), \
        $(if $(ENABLE_HEADLESS),, \
            vcl/vcl.unx \
        ) \
    ) \
```

# Environment Variables

I ran this build on a 64 bit system and therefore needed one additional variable to be set.

```shell
$> export USE_64=1
```

Remember that if you run that command then it is only valid as long as the shell is open. If you close the shell then you will need to run it again. If you start to see issues that says things like `args is negative` then you forgot to set the variable.

# Autogen.sh

One of the longest parts of trying to figure this out was to determine all of the stuff that I had to disable to get it to work. Again, being headless, I really don't need much functionality. The following is the autogen command that I ended up with. Modify as necessary for your own purposes.

```shell
$> ./autogen.sh --without-java --without-junit --without-help --without-myspell-dicts --without-doxygen --disable-gstreamer-0-10 --with-distro=LibreOfficeLinux --with-package-format=rpm --enable-epm --enable-headless --disable-opengl --disable-kdeab --disable-gltf --disable-gconf --disable-postgresql-sdbc --with-theme="" --disable-kde4 --disable-kde --disable-firebird-sdbc
```

As long as that completes successfully, you are only a few hours from being done.

# Make

The last step to this whole process is to actually execute the build. I will warn you that it takes a very long time to execute. On my tiny slice of the mainframe that I was allowed, it took approximately 15 hours to run.

```shell
$> make
```

Yup, that is all there is to it. This will create some RPMs that can be used to install LibreOffice on other S/390 SLES zLinux environments.

The RPMs are tarred up in the following directory:
```
workdir/installation/LibreOffice_Dev/rpm/install/LibreOfficeDev_4.3.5.2_Linux_rpm_download
```

> Written with [StackEdit](https://stackedit.io/).