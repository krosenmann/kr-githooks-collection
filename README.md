
# Table of Contents

1.  [Проверка на брейкпоинты и временный код.](#org9b393de)
2.  [Контроль тестирования\\сборки](#org704414b)
    1.  [Интерактивная сборка](#orgcc18b7c)
3.  [Вставка имени ветки в сообщение коммита](#org2371fd6)
4.  [Настройка и добавление хуков в репы](#orgbeb461e)

Хуки, облегчающие трудовыебудни.

    GETTEXT='gettext "kr-githooks"'


<a id="org9b393de"></a>

# Проверка на брейкпоинты и временный код.

Помечать временный код &#x2013; хорошее решение. Помечайте временный
код. Называйте специфично временные переменные, или добавляйте
соответствующий комментарий. Я, например, для помечания временного кода
использую слово `Petuch` в данных, комментариях или как имя
временной переменной, используемой для отладки.
Хук отлавливает помеченный временный код, забытые бряки гнутых
дебаггеров и прерывает коммит при их нахождении.

    REJECT_MESSAGE=$($GETTEXT "COMMIT REJECTED: commit contains code with break points. Please remove before commiting.")
    CLEAR_STATUS_MESSAGE=$($GETTEXT "Code contains no break points")
    tc_check=$(git grep -i "$REGXP_TC" -- "$F_MASK")
    if [ ${#tc_check} -gt 0 ]
    then
            echo -e $tc_check "\e[31m$REJECT_MESSAGE\e[0m" | cowsay -f dragon
            exit 1
    else
            echo -e $tc_check "\e[36m$CLEAR_STATUS_MESSAGE\e[0m" | cowsay
    fi


<a id="org704414b"></a>

# Контроль тестирования\\сборки

Перед коммитов выполняется команда на контроль качества. Прогон
тестов, сборка.

    TEST_PASSED=$($GETTEXT "\e[36mAll Test Passed\e[0m")
    TEST_FAILED=$($GETTEXT "\e[31mCOMMIT REJECTED: Test's failed!\e[0m")
    $QCONTROL_COMMAND
    RESULT=$?
    if [ $RESULT -ne 0 ]
    then
        echo -e $TEST_FAILED | cowsay -f dragon
        exit 1
    else
        echo -e $TEST_PASSED | cowsay 
    fi


<a id="orgcc18b7c"></a>

## Интерактивная сборка

В блок кода ниже вставить имена чанков в noweb-синтаксисе и
выполнить `org-babel-tangle` (по-умолчанию `C-c C-v t`)

    #!/bin/bash
    {{{l10n}}}
    REGXP_TC="; Tracer()()\|.set_trace()\|petuch"
    F_MASK="*.py"
    QCONTROL_COMMAND="./manage.py test"
    {{{Проверка на наличие нежелательного\временного кода}}}
    {{{тесты перед коммитом}}}
    exit 0


<a id="org2371fd6"></a>

# Вставка имени ветки в сообщение коммита

Всякие ЖИРО-образные системы любят, чтобы в сообщении коммита
писались имена задач, чтобы их можно было вставлять в историю
задач. И чтобы еще и ветки так же назывались. 
По жиротрадиции, это что-то вроде FOO-1337.

    #!/bin/bash 
    BRANCH_NAME=$(git rev-parse --abbrev-ref HEAD | grep "[[:upper:]].*-[[:digit:]].*")
    echo "$BRANCH_NAME" >> $1


<a id="orgbeb461e"></a>

# Настройка и добавление хуков в репы

При работе с литературным исходником можно выполнить настройку и
сборку непосредственно из самого Emacs. Подробнее &#x2013; в литературном
исходнике. 

