# BlockChainPilot
Piloto de pruebas de BlockChain
========================

## Descripción del proyecto ##

El voluntario desarrollará una API (Application programming interface) o solución alterna para registrar contratos y documentos electrónicos por medio de tecnología blockchain. Aplicando las herramientas y los sistemas necesarios para desarrollar una solución a la necesidad planteada. Con este proyecto se promoverá la implementación del blockchain en casos y soluciones reales. Una vez seleccionados los o las voluntari@s, se comenzará un online hackathon. Durante la primer semana el equipo legal de Fundación Enyx proporcionará una introducción al tema de legal tech y derecho de las nuevas tecnologías. Posteriormente se designarán tareas por equipo y diseñará en conjunto la ruta de trabajo junto con la programación de las etapas del proyecto. Se contará con un lider de proyecto por parte de la fundación, quien coordinará el trabajo en equipo y a los voluntarios. Habrá juntas en línea semanales entre todos los participantes y seguimiento diario por medio de una plataforma en línea. 

![Histogram](https://www.onlinevolunteering.org/sites/default/files/unv-logo-es_0.png)

[link to onlinevlunteering!](https://www.onlinevolunteering.org/es/fundaci%C3%B3n-enyx-ac/apoyar-en-el-desarrollo-de-una-api-para-registrar-documentos-electr%C3%B3nicos-en)
## Installation ##
1. Clone this repo
2. Build the OpenCV.Binding project
3. Reference "OpenCV.dll" from your project; found under `"Xamarin.Android.OpenCV\src\OpenCV.Binding\bin\[Debug|Release][-$ABI]\OpenCV.dll"`

## Reducing the `.dll` size ##
OpenCV is huge; building the OpenCV binding project for the AnyCPU platform results in a .dll which is ~64MB! If this is too large for your Android application (and it should be) here are some methods to reduce the size:

### 1. Using platform specific build configurations ###
There are platform specific build configurations for each ABI supported by both Xamarin.Android and OpenCV: `arm64-v8a`, `armeabi`, `armeabi-v7a`, `x86` & `x86_64`. Building for one ABI only includes the native libraries for that platform and the Java wrappers. This reduces OpenCV.dll to ~15MB. The down-side of using this method is that it may complicate your final build process if you wish to [support multiple platforms](https://developer.xamarin.com/guides/android/advanced_topics/build-abi-specific-apks/).

### 2. Removing unnecessary native libraries
It's very likely your project won't need *all* the native libraries included in the `OpenCV.Binding\Jars\[Abi]\` folders. You can delete any unnecessary `.so` files, but make sure to test your app *thoroughly*. If you remove a native library file that is later needed your users will get a nasty runtime error! Combining this with the method above we can reduce our `.dll` size to between 5-10MB. Much better.

### 3. Using the `None` configuration ###
If the `None` platform configuration is used no native libraries are bundled in the .dll, only the Java wrappers. The result is a .dll of ~1.5MB. However, now the native libraries must be installed at runtime. This is done using [OpenCVManager](https://play.google.com/store/apps/details?id=org.opencv.engine), a separate application the user installs from the GooglePlay store, which installs the native OpenCV libraries once which every application that uses OpenCV on the device can use. The `OpenCVLoader` class provides a utility method for detecting if the OpenCV libraries are provided by the application, or the OpenCVManager apk.

            if (!OpenCVLoader.InitDebug())
            {
                Log.Debug(TAG,
				"Internal OpenCV library not found. Using OpenCV Manager for initialization");
                OpenCVLoader.InitAsync(OpenCVLoader.OpencvVersion300, this, mLoaderCallback);
            }
            else
            {
                Log.Debug(TAG, "OpenCV library found inside package. Using it!");
                mLoaderCallback.OnManagerConnected(LoaderCallbackInterface.Success);
            }

OpenCV can now be used when `OnManagerConnected(...)` has been called with the `Success` status. See the SDKDemo applications for working examples.

This is the ideal solution if you have many separate applications that use the OpenCV library. To see what the installation process involves, build the SDK demo with the `None` configuration and follow the instructions when starting it up.

More tricks to reduce the final size have been suggested by [@MarcorOnline](https://github.com/MarcorOnline) [here](https://github.com/TrekDev/Xamarin.Android.OpenCV/issues/14).

## Documentation ##
Documentation is available from [OpenCV](http://docs.opencv.org/2.4/doc/tutorials/introduction/android_binary_package/dev_with_OCV_on_Android.html). As with other Xamarin.Android binding projects, get/set method pairs have been replaced with C# properties and Events have been added to correspond with callback interface methods. Namespaces drop the `org.` prefix and are CamelCased.

## Native application development ##
It is possible to use C/C++ in your mobile OpenCV applications. See the facial recognition and mixed processing examples for how to do this. The C++ code is not compiled in the SDK demo project; it is only included for reference. The C++ code was compiled with eclipse and the ADT/NDK plugins, the resulting `.so` files are then included in this project in the /lib folder.

Cross compiling C++ libraries in the same solution is possible with Visual Studio 2015, see [this  Xamarin blog post for details](https://blog.xamarin.com/build-and-debug-c-libraries-in-xamarin-android-apps-with-visual-studio-2015/).

## Demo App ##
The OpenCV4Android demo apps have been merged into a single application for simplicity

![Histogram](docs/Screenshots/Histogram.png)
![EdgeDetection](docs/Screenshots/EdgeDetection.png)
![Zoom](docs/Screenshots/Zoom.png)
![Puzzle](docs/Screenshots/Puzzle.png)

## Contributing ##
Contributions are very welcome, the OpenCV library is huge and any help is appreciated. If you notice an error, either with the Bindings (eg. a missing class) or the SDK demo [please raise an issue](https://github.com/TrekDev/Xamarin.Android.OpenCV/issues).

Alternatively, you can fix it yourself and raise a pull request. Do this by forking the repo and creating a feature branch from `develop`.

## License ##
The Bindings and SDKDemo are provided under the MIT license. See LICENSE for details. OpenCV is provided under the [BSD licence](http://opencv.org/license.html).
