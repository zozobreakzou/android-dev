develop android appication with c++ and java in vs2017

1. use gyp-ninja-winndk to build the c++ library.
2. use gyp-msvs-ninja to generate the c++ library's vcxproj and solution.
3. open the generated solution and add a new android gradle project, marked as android_app.
4. put the native lib in the path 'app/src/main/jniLibs/armeabi-v7a/'.
    this can be done with gyp copies.
5. add dependcy on build c++ library project for android_app.
6. develop android java component in vs2017's android gradle project.
7. edit the android_app's project property page's debug item,
   add symbol search path with unstriped c++ library folder.
8. now, you can directly coding->build->debug loop fluently.



9. vs2017 now use gradle 2.8 and use a .gradle template, thats different from android studio.
   if need migrate an existing android studio gradle project to vs2017,
   copy and replace all the files in android studio project in to 'android_app', and modify .androidproj & Android.common.targets in MSBUILD.

   in '.androidproj', add below snipcode to the root project element.
      <Target Name="CopyGradleTemplates">
      </Target>
      <PropertyGroup>
        <ShowAllFiles>true</ShowAllFiles>
      </PropertyGroup>

   in Android.common.targets apply the target.patch