how to debug external built c++ code in android studio.

1. build the c++ library with ldflags '--build-id' with ndk and copy the built output library to jniLibs.
   (use gpy-ninja eg.), write a bat script to do the build.
    this ldflags allow android-lldb load the unstriped c++ library automaticaly.

2. hook the native build step into android gradle build process.

    task buildDebugCLib(type:Exec) {
        group 'build'
        description 'build jni libs'
        workingDir 'g:/code/git/mgv-ng-4.1'
        commandLine 'cmd', '/c', 'g:/code/git/mgv-ng-4.1/builddebugtest.bat Debug'
    }

    task buildReleaseCLib(type:Exec) {
        group 'build'
        description 'build jni libs'
        workingDir 'g:/code/git/mgv-ng-4.1'
        commandLine 'cmd', '/c', 'g:/code/git/mgv-ng-4.1/builddebugtest.bat Release'
    }

    afterEvaluate {
        assembleDebug.dependsOn(buildDebugCLib)
        assembleRelease.dependsOn(buildReleaseCLib)
    }

3. use the built c++ library with symbol as the android application's jniLibs.
    put the lib in the path 'app/src/main/jniLibs/armeabi-v7a/'
 or specify the jinLibs path by gradle explicitly
    sourceSets {
        main {
            // let gradle pack the shared library into apk
            jniLibs.srcDirs = ['../distribution/lib']
        }
    }

4. add a fake CMakeList.txt to cheat android studio we have native code, so when debug,
   it will setup the lldb for us and the debugger type 'auto' can work with java&c++ meanwhile.
   in CMakeLists.txt just add a simple static c library so it won't be packed in to apk.
    externalNativeBuild {
        cmake {
            path "CMakeLists.txt"
        }
    }

5. make the native c++ code display in the android studio project view
    use the menu file->open open the file you want to debug.
    add the c++ code as asset in to android studio project by
        sourceSets {
            main {
                // let gradle pack the shared library into apk
                jni.srcDirs = ['path-to-native-c++-source-root']
            }
        }
    just put the c++ source root directory in to the android studio project root directory.