1. `#include <ctime>`
2. `#include <iomanip>`
3. `std::tm` -> struct -> months since January – [0, 11] -> 1980 - 1900 -> tm_isdst
4. `std::time_t` -> class -> from 1970-01-01-00-00-00 UTC -> ignore leap seconds
5. `std::time()` -> function
6. `std::mktime()` -> function
7. `std::localtime()` -> function
8. `std::gmtime()` -> function
9. GMT vs UTC vs GPS time (https://www.ipses.com/eng/In-depth-analysis/Standard-of-time-definition) (https://confluence.qps.nl/qinsy/en/utc-to-gps-time-correction-32245263.html)
10. GPS Week Number Rollover (http://www.usno.navy.mil/USNO/time/gps/gps-week-number-rollover)
