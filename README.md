# Image Converter

Программа выполняет конвертацию изображений из одного формата в другой.

Поддерживаемые форматы: BMP, PPM, JPEG.

Для выполнения конвертации необходимо передать в программу две строки. Первая — путь к исходному файлу, вторая — путь для сохранения результата конвертации. Форматы файлов определяются из расширения файлов.

Пример:
```sh
imgconv.exe path/to/source.bmp path/to/target.jpg
```

## Инструкция по сборке

Для сборки понадобится [CMake](https://cmake.org/) и компилятор C++. Инструкция проверена при использовании компилятора GCC и CMake из пакета MinGW под Windows.

1. Собрать LibJPEG из исходного кода, скачать который можно с [официального сайта](https://www.ijg.org/).

	1.1. Скачайте `jpegsr.zip` для сборки под Windows или `jpegsr.tar.gz` для остальных систем. Распакуйте архив в директорию, не содержащую пробелов и русских символов.

	1.2. Перейдите в папку с исходным кодом. Выполните Autoconf-скрипт для debug-сборки:
	```sh
	mkdir debug
	cd debug
	../configure --enable-shared=no CPPFLAGS="-DDEBUG -g -O0" CFLAGS="-g -O0"
	```
	Для сборки под MinGW укажите дополнительный параметр:
	```sh
	MAKE="mingw32-make"
	```

	1.3. Соберите библиотеку с помощью `make` или `mingw32-make`:
	```sh
	make 
	```

	1.4. Выполните release-сборку:
	```sh
	cd ..
	mkdir release
	cd release
	../configure --enable-shared=no CPPFLAGS="-O3" CFLAGS="-O3"
	make 
	```

	1.5. Создайте пакет 'libjpeg', правильно разместив полученные файлы по каталогам:
	```
	libjpeg/
	├── include/
	│   ├── jconfig.h
	│   ├── jerror.h
	│   ├── jmorecfg.h
	│   └── jpeglib.h
	└── lib/
		├── Debug
		│   └── libjpeg.a
		└── Release
			└── libjpeg.a
	```

	Файл 'jconfig.h' должен сгенерироваться автоматически. Если он не был сгенерирован, то он не нужен.

	1.6. Разместите пакет 'libjpeg' в корневой директории проекта, рядом с ImgConverter и ImgLib.

2. Собрать Image Converter

	2.1. Создайте папку `build` в корне проекта:
	```sh
	mkdir build
	```

	2.2. Выполните сборку, используя следующие команды:
	```sh
	cmake ../ImgConverter -DCMAKE_BUILD_TYPE=Debug -DLIBJPEG_DIR=../libjpeg -G "MinGW Makefiles"
	cmake --build .
	```

	Если вы не используете MinGW для сборки, то параметр `-G "MinGW Makefiles"` указывать не нужно.
