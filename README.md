
# Table of Contents

1.  [Проверка на брейкпоинты и временный код.](#orgfb1dbfb)
2.  [Контроль тестирования\\сборки](#orgf6132e6)
3.  [Интерактивная сборка](#org2a68789)
4.  [Установка из emacs](#org840d9e9)

Хуки, облегчающие трудовыебудни.

    GETTEXT='gettext "kr-githooks"'


<a id="orgfb1dbfb"></a>

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


<a id="orgf6132e6"></a>

# Контроль тестирования\\сборки

Перед коммитов выполняется команда на контроль качества. Прогон
тестов, сборка.

    TEST_PASSED=$($GETTEXT "\e[36mAll Test Passed\e[0m")
    TEST_FAILED=$($GETTEXT "\e[31mCOMMIT REJECTED: Test's failed!\e[0m")
    $QCONTROL_COMMAND
    RESULT=$?
    if [ $RESULT -ne 0 ]
    then
        echo -e $TEST_FAILED
        exit 1
    else
        echo -e $TEST_PASSED
    fi


<a id="org2a68789"></a>

# Интерактивная сборка

В блок кода ниже вставить имена чанков в noweb-синтаксисе и
выполнить `org-babel-tangle` (по-умолчанию `C-c C-v t`)

    #!/bin/bash
    {{{l10n}}}
    REGXP_TC="; Tracer()()\|.set_trace()\|petuch"
    F_MASK="*.py"
    QCONTROL_COMMAND="./manage.py test"
    {{{Проверка на наличие нежелательного\временного кода}}}
    {{{тесты перед коммитом}}}


<a id="org840d9e9"></a>

# Установка из emacs

Переопределить PROJECT и выполнить

    REPO=$PROJECT/.git/hooks/
    chmod +x pre-commit
    mv pre-commit $REPO

