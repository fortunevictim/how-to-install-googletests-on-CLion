# Как установить Gtests на Clion и сделать аналог Solution от VS. #

- Для начала создаем в CLion проект Upper_project, внутри которого будут находиться два проекта, которые мы будем связывать между собой: **проект с тестами** и сам **проект с кодом** который мы хотим протестировать.

![](https://sun9-23.userapi.com/impf/tXDVbPOQB0fHe7tgoBopolfVxhkTb6F6yfSR5g/p89bH6OnQzk.jpg?size=515x494&quality=96&proxy=1&sign=e2ef83fb7dfb7f5cbd7cc2aa8432dd31 )

- Проект Upper_project выполняет здесь роль, аналогичную роли Solution в Visual Studio. Немного позже мы подключим в него необходимые нам проекты, но пока что это всего лишь обычный проект.
- Теперь создаем два проект внутри папки Upper_project: назовем их gtestset и lab1_src. Делаем это, конечно же, через File - New Project :) . Выглядеть ваш Upper_project будет так:

![](https://sun9-67.userapi.com/impf/QCLLmEJh3xhazxdmluJZOw5igolaQaeMEr_yqA/m_yWgw6AXOs.jpg?size=514x365&quality=96&proxy=1&sign=1e968e88963bfb474d6789eaded46185)
![](https://sun9-44.userapi.com/impf/D5JYewe3vx_haByaRvTfx8OLHXJYXY7EBMkVIA/B557AYwHMFc.jpg?size=273x175&quality=96&proxy=1&sign=8c3441618fe14c1b56a3dd853538c33b)
- Далее это видео станет очень полезным и наглядным пособием как именно подключить googletest к Вашему проекту, либо можете продолжить читать:
https://www.youtube.com/watch?v=M067vFQG7ZA&t=263s
>По видео: если Вам далее не понадобится header файл от класса, который создавал автор видео на 3:32, то можете смело удалять его. Видео стоит смотреть до того момента, когда он начинает редактировать .cpp файл с тестами (4:33).

- По запросу google tests Вам необходимо скачать репозиторий с github.
- Внутри папки проетка gtestset надо создать папки tests и lib. В папку lib вы просто копируете googleteset-master.
- В CLion внутри папки tests Вам надо создать .cpp файл, назовем его TestForSet.cpp:

![](https://sun9-44.userapi.com/impf/zfMABEgAhXkVyhqJoavYYbMs0zf5Nswjc8SFlg/9xJaLiUm1po.jpg?size=494x219&quality=96&proxy=1&sign=4251e88baa2fc6156b01dc44404198bd)

- Теперь отредактируем файл (gtestset/main.cpp), чтобы он выглядел так(код ниже):

![](https://sun9-57.userapi.com/impf/z7RlntaeZ0YvJzB83XyQXu5snCpCJ6yNiKiBlw/w-wfFjbNDjw.jpg?size=873x314&quality=96&proxy=1&sign=b6cf3e87d82e136f525be7db52cbc711)

- Код для gtestset/main.cpp
```
include "gtest/gtest.h"
include "gmock/gmock.h"
int main(int argc, char *arg[]) {
    testing::InitGoogleTest(&argc, argv);
    return RUN_ALL_TESTS();
}
```
## Настройка связей ##
- Теперь будем настраивать связи между проектами.
> - Можно посмотреть здесь: https://stackoverflow.com/questions/36443085/is-there-a-way-to-work-on-multiple-projects-in-clion

- Здесь Upper_project соответствующее имя проекта, которое вы создали, а директории идущие после слеша в последних двух строках - названия ваших проектов, которые Вы создали внутри Upper_project'а . # 1
- Файл CMakeLists.txt у Upper Project должен выглядеть так(код ниже):

![](https://sun9-10.userapi.com/impf/xJug5YxYF-RyK87oGynHQl4X43kw2qbqNAhgkg/83V6lgAU4xI.jpg?size=844x204&quality=96&proxy=1&sign=cbf6b9f254a80a277b8c948299ba4a38)

- Код для CMake:

```
cmake_minimum_required(VERSION 3.5)

project(Upper_project)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=gnu++0x")

add_subdirectory("${PROJECT_SOURCE_DIR}/lab1_src")
add_subdirectory("${PROJECT_SOURCE_DIR}/gtestset")

```


- Теперь отредактируем CMakeList.txt у этих внутренних проктов. Начнем с репозитория с кодом, который будет проверять тестами (lab1_src/CMakeLists.txt) . Файл должен выглядеть так (код ниже):

![](https://sun9-22.userapi.com/impf/gipzDDcvh4STA0-7Qk95ty114MBWllTKZlcQjg/5_Z_072lYKI.jpg?size=884x356&quality=96&proxy=1&sign=fc2a19dd3252c52309f1b3a8ab6b3c1e)

- Заметьте, значение project(*name*) сменилось с lab1_src на Upper_project. Это нужно для того, чтобы открывать оба проекта как один - это и есть аналогия с Soultion, которой мы хотим добиться. Строка add_executable отсутствует, поскольку Ваш код будет являться константной библиотекой, которую мы создаем последними двумя строками. Во второй строке set должны быть все файлы кроме main.cpp, а первым ее параметром является обобщающее имя для всех следом указанных файлов.

```
cmake_minimum_required(VERSION 3.5)

project(Upper_project)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=gnu++0x")

set(lab1_src_files Trit.cpp Trit.h TritSet.cpp TritSet.h)

add_library(lab1_src ${lab1_src_files})
```

- Теперь открываем CMakeList.txt у проекта, в котором будут реализованы тесты (gtestset/CMakeLists.txt). Аналогично меняем на данные строки (код ниже):

![](https://sun9-8.userapi.com/impf/LyNKymbJz1l3qhLJFpLQHXHwdX3JrwEN--G6Xw/N19KYkQhfdA.jpg?size=919x407&quality=96&proxy=1&sign=84512286c7fa116dd0e303387108cca6)

```
cmake_minimum_required(VERSION 3.5)

project(Upper_project)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=gnu++0x")

add_subdirectory(lib/googletest-master)
include_directories(lib/googletest-master/googletest/include)
include_directories(lib/googletest-master/googlemock/include)
include_directories(tests/include)
#1
include_directories(../lab1_src/include)
#2
link_directories(../lab1_src/include)

set(SOURCE_FILES main.cpp tests/TestForSet.cpp)
add_executable(gtestset ${SOURCE_FILES} tests/TestForSet.cpp)
#3
target_link_libraries(gtestset gtest gtest_main lab1_src) 
```

- Строками с 7, 8, 9 и 19(о последнем параметре этой строки далее) мы подключаем googletests. Теперь нам интересны строки, находящимися под номерами(при копировании их можно убрать): двумя мы подключаем саму директорию с кодом, а в последней прописываем библиотеку, ранее созданную в другом CMake файле (от lab1_src).

- Таким образом ваш проект будет выглядеть примерно таким образом:
![](https://sun9-73.userapi.com/impf/4gqrk4JwrFHSXPA1EBkt95bQS32oOHNJanvMyA/e0tX5zg35_g.jpg?size=513x619&quality=96&proxy=1&sign=e6b34396586f2f0eba3402dc16f5c7e3)

- Далее подключим Ваш рабочий код к тестам с помощью #include, чтобы проверить его (правда тесты тоже написать придется).
![](https://sun9-45.userapi.com/impf/eKqyub0UMPSW-bPuNc98X2xzF2_S1hhOyAKAKQ/_Drz3hmqbes.jpg?size=741x323&quality=96&proxy=1&sign=f86882020d17b4124316640670d165b3)

- В тот момент, когда вы захотите проверить работоспособность своего кода, Вам нужно будет открыть с помощью CLion Upper_project, и либо дойти до файла TestForSet.cpp и в единичном порядке запускать тесты, либо дойти до (gtestset/main.cpp) и собрать его. Здесть RUN_ALL_TESTS сам найдет все написанные Вами тесты и прогонит их. Пример успешной отработки теста будет ниже :)
![](https://sun9-20.userapi.com/impf/wK6ymqxbRe8mJ7IOLFJk4yep4ZoVM3is-YP5BA/sP8A9iJGvEA.jpg?size=1600x984&quality=96&proxy=1&sign=fecf2c6310a5dc9b1241657875d2d8e9)

## Возможные ошибки ##
1) Во время сборки тестов вы видите что-то такое:
![](https://sun9-2.userapi.com/impf/CRJol4CMH8Oqx_Me7XiR9JDsQV4LBVZfZds_xw/NSN7e7h1J1I.jpg?size=1533x407&quality=96&proxy=1&sign=ba469729a195d304aeb96216d644cfe3)
 В этом случае проблема в компиляторе. Лично мне помогла смена mingw32 на mingw64, поскольку простое переключение с -std=gnu++0x на -std=c++0x под mingw32 не помогло.
2) В выведенной информации в консоль вы видите нечто такое:
![](https://sun9-7.userapi.com/impf/tjxrWuhBY7hpWz1pPlsJXmtLQWKtBCincLHY8Q/7cl1u9jQZw0.jpg?size=1026x144&quality=96&proxy=1&sign=90a405f804231ba3cfdedc34fc2bf298)
Во-первых, проверьте из какого именно проекта вы пытаетесь собрать тесты. Это должен быть именно Upper_project.
Во-вторых, проверьте, совпадают ли имена библиотеки которую вы создали в (lab1_src/CMakeLists.txt) и имя то, что вы указали в (gtestset/CMakeLists.txt) в последней строке.

Если заметили ошибки или хотите увеличить точность руковоства, пишите мне: vk.com/fortune_victim
