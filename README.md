# Brasil's Workdays and Holidays Helper (PHP)

Just to save some time. ;)

```PHP
<?php

namespace LemosLuan;

class BrasilHolidays
{
    public static function  getBrasilHolidays($year = null, $dateFormat = 'd/m'): array
    {
        if ($year === null) {
            $year = intval(date('Y'));
        }

        $easter     = easter_date($year); // http://www.php.net/manual/pt_BR/function.easter-date.php
        $easterDay = date('j', $easter);
        $easterMonth = date('n', $easter);
        $easterYear = date('Y', $easter);

        $holidays = array(
            // Fixed holidays
            'Ano Novo' => mktime(0, 0, 0, 1,  1,   $year), // Confraternização Universal - Lei nº 662, de 06/04/49
            'Tiradentes' => mktime(0, 0, 0, 4,  21,  $year), // Tiradentes - Lei nº 662, de 06/04/49
            'Dia do Trabalhador' => mktime(0, 0, 0, 5,  1,   $year), // Dia do Trabalhador - Lei nº 662, de 06/04/49
            'Independência do Brasil' => mktime(0, 0, 0, 9,  7,   $year), // Dia da Independência - Lei nº 662, de 06/04/49
            'Nossa Senhora Aparecida' => mktime(0, 0, 0, 10,  12, $year), // N. S. Aparecida - Lei nº 6802, de 30/06/80
            'Finados' => mktime(0, 0, 0, 11,  2,  $year), // Todos os santos - Lei nº 662, de 06/04/49
            'Proclamação da República' => mktime(0, 0, 0, 11, 15,  $year), // Proclamação da republica - Lei nº 662, de 06/04/49
            'Natal' => mktime(0, 0, 0, 12, 25,  $year), // Natal - Lei nº 662, de 06/04/49

            // Dates that depend on easter date
            'Segunda de Carnaval' => mktime(0, 0, 0, $easterMonth, $easterDay - 48,  $easterYear),
            'Terça de Carnaval' => mktime(0, 0, 0, $easterMonth, $easterDay - 47,  $easterYear),
            'Sexta-feira da Paixão' => mktime(0, 0, 0, $easterMonth, $easterDay - 2,  $easterYear),
            'Páscoa' => mktime(0, 0, 0, $easterMonth, $easterDay,  $easterYear),
            'Corpus Christi' => mktime(0, 0, 0, $easterMonth, $easterDay + 60,  $easterYear),
        );

        asort($holidays);

        $holidays = array_map(function ($item) use ($dateFormat) {
            return date($dateFormat, $item);
        }, ($holidays));

        return $holidays;
    }

    public static function getQtyWorkingDays(string $startDate, string $endDate): int
    {
        $begin = strtotime($startDate);
        $end   = strtotime($endDate);
        if ($begin > $end) {
            echo "Hmm... Start date is in the future. Check your entries";
            return 0;
        } else {
            $holidays = array_values(static::getBrasilHolidays(date('Y', $begin)));
            $weekends = 0;
            $no_days = 0;
            $holidayCount = 0;
            while ($begin <= $end) {
                $no_days++; // no of days in the given interval
                if (in_array(date("d/m", $begin), $holidays)) {
                    $holidayCount++;
                }
                $what_day = date("N", $begin);
                if ($what_day > 5) { // 6 and 7 are weekend days
                    $weekends++;
                };
                $begin += 86400; // +1 day
            };
            $working_days = $no_days - $weekends - $holidayCount;

            return $working_days;
        }
        return 0;
    }

    public static function test()
    {
        $startDate = date("Y-m-01");
        $endDate = date("Y-m-t");

        echo "Quantity of workdays between $startDate and $endDate: " . static::getQtyWorkingDays($startDate, $endDate) . "\n\n";

        $holidays = static::getBrasilHolidays(date("y"));

        foreach ($holidays as $key => $item) {
            echo "$key => $item\n";
        }
    }
}

```

#Try it

-   `composer require lemosluan/ptbr-holidays`
-   `\LemosLuan\BrasilHolidays::test();`
