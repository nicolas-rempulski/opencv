# Releasing a New Version

## Version Notes

### 3.0.0

* OS X Tests are currently failing due to https://github.com/Itseez/opencv/issues/5030
* Linux 32 bit build failed due to IPP - seems to have been corrected after build 64 bit version.
* Linux 32 bit tests failed similar to OS X.
* Linux 64 bit tests crash the VM.
* Windows 64 bit tests fail in multiply.

## Prepare

1. Download OpenCV Linux/OSX source and Windows binary release to opencv/. Should be a ZIP and a EXE.
2. Unzip source ZIP to create opencv/opencv-VERSION.
3. Run ./create-targets.sh VERSION (e.g. ./create-targets 3.0.0) to create all of the target directories under opencv/opencv-VERSION/target.
4. Update pom.xml, OpenCV.java and README.md with new versions.

## Build OSX and Java Targets

1. Make sure Java is 1.7: `java -version`, `export JAVA_HOME=$(/usr/libexec/java_home -v1.7)` 
2. `cd opencv/opencv-VERSION/target/osx/x86_64`
3. `cmake -DBUILD_SHARED_LIBS=OFF -DEIGEN_INCLUDE_PATH=/usr/local/include/eigen3 ../../.. > cmake.log`
4. `make -j8`
5. `./copy-resources VERSION`. It won't finish, but it should get far enough.
6. `mvn clean test`.

## Build Linux Targets

### 32 bit

1. Start the 32 bit Linux VM. It needs default-jdk, maven and ant installed.
2. `cd /mnt/hgfs/opencv/opencv/opencv-VERSION/target/linux/x86_32`
3. `cmake -DBUILD_SHARED_LIBS=OFF ../../.. > cmake.log`
4. `make -j8`
5. In the host OS, `./copy-resources VERSION`
6. Back in the VM, `cd /mnt/hgfs/opencv`
7. `mvn clean test`

### 64 bit
 
1. Start the 64 bit Linux VM. It needs default-jdk, maven and ant installed.
2. `cd /mnt/hgfs/opencv/opencv/opencv-VERSION/target/linux/x86_64`
3. `cmake -DBUILD_SHARED_LIBS=OFF ../../.. > cmake.log`
4. `make -j8`
5. In the host OS, `./copy-resources VERSION`
6. Back in the VM, `cd /mnt/hgfs/opencv`
7. `mvn clean test`

## Unpack Windows Binaries

1. In a Windows VM, double click the Windows EXE downloaded previously. It will
ask where to extract. Choose opencv/opencv-VERSION/target/windows.
2. In the host OS, `./copy-resources VERSION`.
3. Back in the VM, `mvn clean test`

## Finish

1. Commit the updates to develop.
2. Merge develop into master.
3. Push to Sonatype:
	1. Read http://central.sonatype.org/pages/apache-maven.html
	2. `mvn clean deploy -P release-sign-artifacts`
	3. Check it's good at https://oss.sonatype.org/
	4. Release with `mvn nexus-staging:release`
	5. Or reset with `mvn nexus-staging:drop` 
4. `git tag vVERSION-RELEASE` (e.g. `git tag v3.0.0-1`)

