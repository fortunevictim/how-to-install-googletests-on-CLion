# Как установить Gtests на Clion и сделать аналог Solution от VS. #
## Дополнительно(внизу): Как запустить pipelines для ваших тестов на BitBucket ##

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

![](https://sun9-68.userapi.com/impg/xMhNghw8N5dR4dvjd51P2Nr_oTSmxxs-g-UePA/0fZ8f_go8Cg.jpg?size=880x318&quality=96&proxy=1&sign=4487f5931a49795ce7715f0964623395)

```

cmake_minimum_required(VERSION 3.5)

project(Upper_project)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=gnu++0x")

add_subdirectory(lib/googletest-master)
include_directories(lib/googletest-master/googletest/include)
include_directories(lib/googletest-master/googlemock/include)

set(SOURCE_FILES main.cpp tests/TestForSet.cpp)
add_executable(gtestset ${SOURCE_FILES} tests/TestForSet.cpp)
#1
target_link_libraries(gtestset gtest gtest_main lab1_src) 

```

- Строками с (lib/...) мы подключаем googletests. Теперь нам интересна строка, находящаяся под номером 1(при копировании цифру можно убрать):мы прописываем библиотеку, ранее созданную в другом CMake файле (от lab1_src), а также подключаем библиотеки от gtests.

- Таким образом ваш проект будет выглядеть примерно таким образом:
![](https://sun9-73.userapi.com/impf/4gqrk4JwrFHSXPA1EBkt95bQS32oOHNJanvMyA/e0tX5zg35_g.jpg?size=513x619&quality=96&proxy=1&sign=e6b34396586f2f0eba3402dc16f5c7e3)

- Далее подключим Ваш рабочий код к тестам с помощью #include, чтобы проверить его (правда тесты тоже написать придется).
![](https://sun9-45.userapi.com/impf/eKqyub0UMPSW-bPuNc98X2xzF2_S1hhOyAKAKQ/_Drz3hmqbes.jpg?size=741x323&quality=96&proxy=1&sign=f86882020d17b4124316640670d165b3)

- В тот момент, когда вы захотите проверить работоспособность своего кода, Вам нужно будет открыть с помощью CLion Upper_project, и либо дойти до файла TestForSet.cpp и в единичном порядке запускать тесты, либо дойти до (gtestset/main.cpp) и собрать его. Здесть RUN_ALL_TESTS сам найдет все написанные Вами тесты и прогонит их. Пример успешной отработки теста будет ниже :)
![](https://sun9-20.userapi.com/impf/wK6ymqxbRe8mJ7IOLFJk4yep4ZoVM3is-YP5BA/sP8A9iJGvEA.jpg?size=1600x984&quality=96&proxy=1&sign=fecf2c6310a5dc9b1241657875d2d8e9)

## Возможные ошибки ##
1) Во время сборки тестов вы видите что-то такое:
![](https://sun9-2.userapi.com/impf/CRJol4CMH8Oqx_Me7XiR9JDsQV4LBVZfZds_xw/NSN7e7h1J1I.jpg?size=1533x407&quality=96&proxy=1&sign=ba469729a195d304aeb96216d644cfe3)
 - В этом случае проблема в компиляторе. Лично мне помогла смена mingw32 на mingw64, поскольку простое переключение с -std=gnu++0x на -std=c++0x под mingw32 не помогло.
2) В выведенной информации в консоль вы видите нечто такое:
![](https://sun9-7.userapi.com/impf/tjxrWuhBY7hpWz1pPlsJXmtLQWKtBCincLHY8Q/7cl1u9jQZw0.jpg?size=1026x144&quality=96&proxy=1&sign=90a405f804231ba3cfdedc34fc2bf298)
- Во-первых, проверьте из какого именно проекта вы пытаетесь собрать тесты. Это должен быть именно Upper_project.
- Во-вторых, проверьте, совпадают ли имена библиотеки которую вы создали в (lab1_src/CMakeLists.txt) и имя то, что вы указали в (gtestset/CMakeLists.txt) в последней строке.


Если заметили ошибки или хотите увеличить точность руковоства, пишите мне: vk.com/fortune_victim


## Как запустить pipelines для ваших тестов на BitBucket ##
- На BitBucket зайдите в Ваш репозиторий, затем в настройки репозитория. Промотайте вниз и заметьте вкладку Pipelines. Далее settings и сдалайте ползунок зеленым, переключив его на enabled.
- Теперь Вам необходимо клонировать репозиторий в любое удобное для вас место. Мой репозиторий с названием lab1 был клонирован в одноименную папку на компьютере, но для наглядности далее я буду использовать Branch.
- Скопируйте в Branch вашу папку с Upper_project. 
- Начнем с редактирования CMakeLists.txt и добавления файла-инструкции в наш проект для Pipelines.
- Открываем проект Upper_project и находим там gtestset\CMakeLists.txt. (Нумерация по скриншоту ниже) Надо добавить строки 4, 12, в строке 16 и 18 изменить имя собираемого exe файла, чтобы при сборке название папки не совпадало с названием исполняемого файла(был gtestset.exe стал gtestsetexe.exe), в строку 18 дописать последние 2 аргумента. Полноценный код из файла ниже.

 - Обращу внимание, что ВО ВСЕХ Cmake файлах версия Cmake первой строкой должна быть 3.5 и не другая, поскольку на BitBucket это максимальная версия Cmake.

![](https://sun9-32.userapi.com/impg/DhhWtHdNDjYJzOjjSrnSFEPQ_56Mrrw6qU-1jQ/_B2ZyF1Z-Y4.jpg?size=771x405&quality=96&proxy=1&sign=bb508d26387e576695f182e79b820a70)

```

cmake_minimum_required(VERSION 3.5)
project(Upper_project)

find_package(GTest REQUIRED)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=gnu++0x")

add_subdirectory(lib/googletest-master)
include_directories(lib/googletest-master/googletest/include)
include_directories(lib/googletest-master/googlemock/include)

include_directories(${GTEST_INCLUDE_DIRS})

set(SOURCE_FILES main.cpp tests/TestForSet.cpp)

add_executable(gtestsetexe ${SOURCE_FILES})

target_link_libraries(gtestsetexe lab1_src gtest gtest_main ${GTEST_LIBRARIES} pthread)

```

- Не удивляйетесь тому, что сейчам Ваш скопированный в Branch проект Upper_project не запускается и выглядит это примерно так, itBucket:

![](https://sun9-58.userapi.com/impg/GQeYnbt9h8AdZHwuGYfsM7obBNbS_HQ6hiDRqg/mRGfK_P6LQI.jpg?size=1175x191&quality=96&proxy=1&sign=74d347931eb6ee1fcce4c9a63d5719e7)

- Теперь создадим инструкцию. Откройте Branch, где у Вас лежит Upper_project, и создайте файл с таким именем и разрешением: bitbucket-pipelines.yml.

![](https://sun9-51.userapi.com/impg/9yHFlMpvtaE9lZlW56s4huqHABNW3DPrCmGvNg/0x3cASd7gog.jpg?size=255x44&quality=96&proxy=1&sign=5ee5be1a209268ddbf0f60dc51732438)

- В нем с помощью любого текстового редактора пишем строки, приведенные ниже. Поскольку exe файл собирается по умолчанию в корневой директории проекта, то у нас он будет собираться в папке Upper_poject/gtestset . Значит, последняя строка будет такой: ./gtestset/gtestsetexe
- После build/ надо указать путь к вашему проекту, в котором будет все происходить. Надо указать имя папки которую мы будем пушить на сам BitBucket, то есть Upper_project. Поэтому пишем  build/Upper_project.

![](https://sun9-53.userapi.com/impg/rmoNlgDnMMdyhxbt1iKnqAWF-Y6r8choUpd_OQ/ODb-Sa39h9c.jpg?size=616x315&quality=96&proxy=1&sign=ed8c709c40ab979020bd9ac32228278e)

```

image: atlassian/default-image:2

pipelines:
  default:
    - step:
        script:
          - apt-get update -y
          - apt-get install -y cmake
          - apt-get install -y libgtest-dev
          - cd /usr/src/gtest
          - cmake .
          - make
          - cp *.a /usr/lib
          - cd /opt/atlassian/pipelines/agent/build/lab1/
          - cmake .
          - make
          - ./gtestset/gtestsetexe

```

- С редактированием мы разобрались, теперь пушим. Выглядеть ваша ветка в репозитории должна выглядеть примерно так: Проект Upper_project (У меня это lab1) и файл .yml

![](https://sun9-34.userapi.com/impg/QiO2HJIedRIX7gzKehOg9XQR1KCGbkF10PTQiA/muyQKHzOQhc.jpg?size=533x244&quality=96&proxy=1&sign=b50ac124314b2abd92f3154e3dd591fb)

- Теперь ваш Pipeline должен автоматически начать собираться во вкладке Pipelines.

- Еслс все сделано верно и все тесты проходились локально до изменеения CMakeLiests, то Вы увидите примерно это :)

![](https://sun9-43.userapi.com/impg/xVzqN6ebGQ0QpmpNWH8Vzl_tGXkB2QnB5CwWmQ/PNzdRq_-r44.jpg?size=1318x92&quality=96&proxy=1&sign=e965e8ee7a0cf30a9e6ab12f1e8badb1)

Если заметили ошибки или хотите увеличить точность руковоства, пишите мне: vk.com/fortune_victim
