# Compiling BRDF with CMake

Currently only MSVC tested, but the extenral dependencies should work under any platform.

## Compilation Steps
1. Go to external directory: `cd external`
2. Run `cmake ./`. (If you can't download, try to read this [gist](https://gist.github.com/VicentChen/c1140872492d7134c511208179cda133#how-to-resolve-cmake-download-issues))
3. Open **Visual Studio Developer Command Prompt**, go to project root directory, and run `qmake -r -tp vc prefix=foo`
4. Open `main.sln` in project root directory, and compile the solution.
5. Change project `brdf` property: Change `Configuration Properties -> Debugging -> Working Directory` to `$(ProjectDir)..\`
6. Done!

## Dependencies
 - [zlib](https://github.com/madler/zlib.git) https://github.com/madler/zlib.git
 - [GLEW](https://github.com/nigels-com/glew/releases/download/glew-2.2.0/glew-2.2.0-win32.zip) https://github.com/nigels-com/glew/releases/download/glew-2.2.0/glew-2.2.0-win32.zip
 - [glut](http://prdownloads.sourceforge.net/freeglut/freeglut-3.2.1.tar.gz?download) http://prdownloads.sourceforge.net/freeglut/freeglut-3.2.1.tar.gz?download
