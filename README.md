## Как установить Gtests на Clion

- Для начала создаем в CLion проект, внутри которого будут находиться два проекта, которые мы будем связывать между собой: **проект с тестами** и сам **проект с кодом** который мы хотим протестировать.
![](https://sun9-23.userapi.com/impf/tXDVbPOQB0fHe7tgoBopolfVxhkTb6F6yfSR5g/p89bH6OnQzk.jpg?size=515x494&quality=96&proxy=1&sign=e2ef83fb7dfb7f5cbd7cc2aa8432dd31 )

- Теперь создаем два проект внутри Upper_project. Делаем это, конечно же, через File - New Project :)
![](https://sun9-67.userapi.com/impf/QCLLmEJh3xhazxdmluJZOw5igolaQaeMEr_yqA/m_yWgw6AXOs.jpg?size=514x365&quality=96&proxy=1&sign=1e968e88963bfb474d6789eaded46185)
- Далее это видео станет очень полезным и наглядным пособием как именно подключить googletest к Вашему проекту, либо можете продолжить читать:
https://www.youtube.com/watch?v=M067vFQG7ZA&t=263s
Если Вам далее не понадобится header файл от класса, который создавал автор видео на 3:32, то можете смело удалять его. Видео стоит смотреть до того момента, когда он начинает редактировать .cpp файл с тестами (4:33).

- По запросу google tests Вам необходимо скачать репозиторий с github.
- Внутри папки проетка gtestset надо создать папки tests и lib. В папку lib вы просто копируете googleteset-master.
- В CLion внутри папки tests Вам надо создать .cpp файл:

![](https://sun9-44.userapi.com/impf/zfMABEgAhXkVyhqJoavYYbMs0zf5Nswjc8SFlg/9xJaLiUm1po.jpg?size=494x219&quality=96&proxy=1&sign=4251e88baa2fc6156b01dc44404198bd)

- Теперь будем настраивать связи между проектами. Файл CMakeLists.txt у Upper Project должен выглядеть так:

--

cmake_minimum_required(VERSION 3.5)

project(Upper_project)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=gnu++0x")

add_subdirectory("${PROJECT_SOURCE_DIR}/lab1_src")
add_subdirectory("${PROJECT_SOURCE_DIR}/gtestset")

--

 - Чуть более подробно написано здесь: https://stackoverflow.com/questions/36443085/is-there-a-way-to-work-on-multiple-projects-in-clion

- Здесь Upper_project соответствующее имя проекта, которое вы создали, а директории идущие после / в последних двух строках - названия ваших проектов, которые Вы создали внутри Upper'а .
- Теперь отредактируем CMakeList.txt у этих внутренних проктов. Начнем в репозитория с кодом, который будет проверять тестами. Файл должен выглядеть так:

--

cmake_minimum_required(VERSION 3.5)

project(Upper_project)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=gnu++0x")

set(lab1_src_files Trit.cpp Trit.h TritSet.cpp TritSet.h)

add_library(lab1_src ${lab1_src_files})

--

- Заметьте, значение project(*name*) сменилось на Upper_project. Это нужно для того, чтобы открывать оба проекта как один. Строка add_executable отсутствует, поскольку Ваш код будет являться константной библиотекой, которую мы создаем последними двумя строками. В строке set должны быть все файлы кроме main.cpp.

-Теперь открываем CMakeList.txt у проекта, в котором будут реализованы тесты. Аналогично меняем на данные строки:

--

cmake_minimum_required(VERSION 3.5)

project(Upper_project)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=gnu++11")

add_subdirectory(lib/googletest-master)
include_directories(lib/googletest-master/googletest/include)
include_directories(lib/googletest-master/googlemock/include)
include_directories(tests/include)
include_directories(../lab1_src/include) #1
link_directories(../lab1_src/include) #2

set(SOURCE_FILES main.cpp tests/TestForSet.cpp)
add_executable(gtestset ${SOURCE_FILES} tests/TestForSet.cpp)

target_link_libraries(gtestset gtest gtest_main lab1_src) #3

--

- Часть строк здесь из прикрепленного ранее видео на youtube, нам интересуют строки с номерами. Первыми двумя мы подключаем саму директорию с кодом, а в последней прописываем библиотеку, ранее созданную в другом CMake файле.

-Таким образом ваш проект будет выглядеть примерно таким образом:
![](https://sun9-73.userapi.com/impf/4gqrk4JwrFHSXPA1EBkt95bQS32oOHNJanvMyA/e0tX5zg35_g.jpg?size=513x619&quality=96&proxy=1&sign=e6b34396586f2f0eba3402dc16f5c7e3)
- Далее подключим Ваш рабочий код к тестам, чтобы проверить его (правда тесты тоже написать придется):
![](https://sun9-45.userapi.com/impf/eKqyub0UMPSW-bPuNc98X2xzF2_S1hhOyAKAKQ/_Drz3hmqbes.jpg?size=741x323&quality=96&proxy=1&sign=f86882020d17b4124316640670d165b3)

Если заметили ошибки или хотите увеличить точность руковоства, пишите vk.com/fortune_victim
