# simulink-example

Данный репозиторий сожержит в себе пример интеграции Simulink модели в Python.

<image
  src="model.png"
  alt="Модель"
  caption="Simulink Модель">

# Интеграция Simulink модели

1. Определить входные и выходные параметры при помощи блоков In1/Out1

2. В настройках симулинк модели выбрать Code Generation/System target file -> ert_shrlib.tlc

3. Сгенерировать C код комбинацией `ctrl+B`

4. Зайти в папку MODEL_NAME_ert_shrlib_rtw и сгенерировать so файл при помощи gcc compiler 

```
gcc -shared -o model.so -fPIC *.c
```

5. Опишите интерфейс взаимодействия

    Интерфейс взаимодействия описывается для входных и выходных параметров при помощи ctypes.Structure и преобразователя типов rtwtypes

    ``` python

    class ExtY(ctypes.Structure):

    _fields_ = [
        ("name1", type_from_rtwtypes),
        ("name2", type_from_rtwtypes),
    ]
    ```
    Имя и тип можно посмотреть в сгенерированном С файле. Файл должен называться MODEL_NAME.h. В данном файле найдите описание External inputs, External outputs

Для работы с so файлом потребуются 3 функции

* MODEL_NAME_initialize - служит для инициализации модели

* MODEL_NAME_step - служит для расчета модели на следующем шаге модели
    шаг модели равен dt, определенном в параметрах Simulink модели
    
* MODEL_NAME_terminate - служит для освобождении ресурсов модели

Пример работы находиться в example_your_sim.ipynb