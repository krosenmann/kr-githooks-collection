
# Table of Contents

1.  [Проверка на брейкпоинты и временный код.](#orgab939ff)
2.  [Контроль тестирования\\сборки](#org54130cc)
3.  [Интерактивная сборка](#org7501be7)
4.  [Установка из emacs](#org62cff85)

Хуки, облегчающие трудовыебудни.

    GETTEXT='gettext "kr-githooks"'


<a id="orgab939ff"></a>

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
            echo -e $tc_check "\e[31m$REJECT_MESSAGE\e[0m"
            exit 1
    else
            echo -e $tc_check "\e[36m$CLEAR_STATUS_MESSAGE\e[0m"
    fi


<a id="org54130cc"></a>

# Контроль тестирования\\сборки

Перед коммитов выполняется команда на контроль качества. Прогон
тестов, сборка.

    
    $QCONTROL_COMMAND
    TEST_PASSED=$($GETTEXT "\e[36mAll Test Passed\e[0m")
    TEST_FAILED=$($GETTEXT "\e[31mCOMMIT REJECTED: Test's failed!\e[0m")
    RESULT=$?
    if [ $RESULT -ne 0 ]
    then
        echo -e $TEST_FAILED
        exit 1
    else
        echo -e $TEST_PASSED
    fi


<a id="org7501be7"></a>

# Интерактивная сборка

В блок кода ниже вставить имена чанков в noweb-синтаксисе и
выполнить `org-babel-tanle` (по-умолчанию `C-c C-v t`)

    #!/bin/bash
    GETTEXT='gettext "kr-githooks"'
    REGXP_TC="; Tracer()()\|.set_trace()\|petuch"
    F_MASK="*.py"
    QCONTROL_COMMAND="./manage.py test"
    
    REJECT_MESSAGE=$($GETTEXT "COMMIT REJECTED: commit contains code with break points. Please remove before commiting.")
    CLEAR_STATUS_MESSAGE=$($GETTEXT "Code contains no break points")
    tc_check=$(git grep -i "$REGXP_TC" -- "$F_MASK")
    if [ ${#tc_check} -gt 0 ]
    then
            echo -e $tc_check "\e[31m$REJECT_MESSAGE\e[0m"
            exit 1
    else
            echo -e $tc_check "\e[36m$CLEAR_STATUS_MESSAGE\e[0m"
    fi
    
    $QCONTROL_COMMAND
    TEST_PASSED=$($GETTEXT "\e[36mAll Test Passed\e[0m")
    TEST_FAILED=$($GETTEXT "\e[31mCOMMIT REJECTED: Test's failed!\e[0m")
    RESULT=$?
    if [ $RESULT -ne 0 ]
    then
        echo -e $TEST_FAILED
        exit 1
    else
        echo -e $TEST_PASSED
    fi


<a id="org62cff85"></a>

# Установка из emacs

    REPO=$PROJECT/.git/hooks/
    chmod +x pre-commit
    mv pre-commit $REPO
