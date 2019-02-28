# Pačios svarbiausias `<chrono>` bibliotekos funkcijas atsiradusios nuo cpp 20

## CALENDAR:

#### `std::chrono::last_spec (nuo C++ 20)` - last_spec is an empty tag type that is used to indicate the last thing in a sequence. [Daugiau](https://en.cppreference.com/w/cpp/chrono)
```cpp
struct last_spec
{
    explicit last_spec() = default;
};
```
> Depending on context, it may indicate the last day of a month (as in [2018y/February/last],
for last day of February 2018, i.e., 2018-02-28) or the last day of the week in a month (as in [2018/February/Sunday[last]],
for last Sunday of February 2018, i.e., 2018-02-25).


#### `std::chrono::day` - the class day represents a day in a month. Its normal range is [1, 31], but it may hold any number in [0, 255].
```cpp
class day;
```
Member functions:
1. `operator+= / operator-=` - adds or subtracts a number of days 

2. `operator unsigned` - retrieves the stored day value 

3. `ok` - checks if the stored day value is in the normal range 


#### `std::chrono::month` - the class month represents a month in a year. Its normal range is [1, 12], but it may hold any number in [0, 255]. Twelve named constants are predefined in the std::chrono namespace for the twelve months of the year.
```cpp
inline constexpr std::chrono::month January{1};
inline constexpr std::chrono::month February{2};
```
Member functions:
1. `operator+= / operator-=` - adds or subtracts a number of months

2. `operator unsigned` - retrieves the stored day value 

3. `ok` - hecks if the stored month value is in the normal range 


#### `std::chrono::year` - the class year represents a year in the proleptic Gregorian calendar. Its range is [-32767, 32767].
```cpp
class year;
```
Member functions:
1. `operator+= / operator-=` - adds or subtracts a number of years from a year

2. `operator+ / operator-` - applies unary operators to year 

3. `is_leap` - determines if the year is a leap year 

4. `min/max` - returns the smallest/largest possible year value 
	
	
#### `std::chrono::weekday` - the class weekday represent a day of the week in the Gregorian calendar. 
```cpp
inline constexpr std::chrono::weekday Sunday{0};
inline constexpr std::chrono::weekday Monday{1};
```
> Its normal range is [0, 6], for Sunday through Saturday, but it can hold any value in the range [0, 255]. Seven named constants are predefined in the `std::chrono namespace` for the seven days of the week.

Member functions:
1. `operator+= / operator-=` - adds or subtracts a number of days 

2. `operator unsigned` - retrieves the stored weekday value 

3. `ok` - checks if the stored weekday value is in the normal range 


#### `std::chrono::weekday_indexed` - the class weekday_indexed combines a weekday, representing a day of the week in the Gregorian calendar, with a small index n in the range [1, 5]. It represents the first, second, third, fourth, or fifth weekday of some month.
 ```cpp
 class weekday_indexed;
 ```
 Member functions:
 1. `weekday` - access the stored weekday 
 
 2. `index` - access the stored index 
 
 3. `ok` - checks if the weekday and index are both valid
 
 
 #### `std::chrono::weekday_last` - the class weekday_last represents the last weekday of some month.
 ```cpp
 class weekday_last;
 ```
 Member functions:
 1. `weekday` - accesses the stored weekday 
 
 
 ## TIME ZONE:
 
 #### `std::chrono::tzdb` - the class tzdb represents a copy of the IANA time zone database. 
 ```cpp
 struct tzdb;
 ```
 > Users cannot construct a tzdb and can only obtain read-only access to one via the free functions `std::chrono::get_tzdb_list` and `std::chrono::get_tzdb`.
 
 Member objects:
 1. `version` - `std::string` that contains the version of the database
 
 2. `zones` - sorted `std::vector<std::chrono::time_zone>` containing description of time zones
 
 3. `links` - `std::vector<std::chrono::link>` containing description of alternative names of time zones (links)
 
 4. `leaps` - `std::vector<std::chrono::leaps>` containing description of leap seconds
 
 Member functions:
 1. `locate_zone` - locate a time zone with the given name 

2. `current_zone` - return the local time zone


#### `std::chrono::tzdb_list` - tzdb_list is a singleton list of std::chrono::tzdbs, each of which represents a copy of the IANA time zone database. 
```cpp
class tzdb_list;
```
> Users cannot construct a tzdb_list and can only obtain access to one via the `std::chrono::get_tzdb_list` free function.

Member functions:
1. `front` - access the first element 

2. `erase_after` - erases an element after an element 

3. `begin / cbegin` - returns an iterator to the beginning of the list 

4. `end / cend` - returns an iterator past the end of the list 


#### `std::chrono::get_tzdb_list, std::chrono::get_tzdb, std::chrono::remote_version, std::chrono::reload_tzdb`. 
1. `std::chrono::tzdb_list& get_tzdb_list();` - Returns a reference to the global `std::chrono::tzdb_list` singleton. If this is the first access to the database, initialize the database. 
> After the initialization, the database will hold a single initialized `std::chrono::tzdb` object. This function is thread-safe: concurrent 
calls to this function from multiple threads do not introduce a data race.

2. `const std::chrono::tzdb& get_tzdb();` - Returns a reference to the first `std::chrono::tzdb` object held by the `tzdb_list` singleton. Equivalent to `std::chrono::get_tzdb_list().front()`.

3. `std::string remote_version();` - Returns a string containing the latest remote database version.

4. `const std::chrono::tzdb& reload_tzdb();` - If `remote_version() != get_tzdb().version`, pushes a new tzdb object representing the remote database to the front of the `tzdb_list` 
singleton referenced by `get_tzdb_list()`. Otherwise there are no effects. No references, pointers or iterators are invalidated. 
Calling this function concurrently with `get_tzdb_list().front()` or `get_tzdb_list().erase_after()` does not introduce a data race.

Exception:
> `std::runtime_error` if for any reason a reference to a `tzdb_list` containing one or more valid tzdb cannot be returned.


#### `std::chrono::locate_zone` - locate a time zone with the given name.
> Convenience function for locating a time zone in the time zone database. Equivalent to `std::chrono::get_tzdb().locate_zone(tz_name)`.
```cpp
auto tz = locate_zone("Lithuania/Vilnius");
```

Exception:
> `std::runtime_error` if the specified time zone cannot be found, or if this is the first reference to the time zone database and the time zone database cannot be initialized.


#### `std::chrono::current_zone` - return the local time zone.
> Convenience function for obtaining local time zone from the time zone database. Equivalent to `std::chrono::get_tzdb().current_zone()`.
```cpp
auto tz = current_zone();
```

Exception:
> `std::runtime_error` if this is the first reference to the time zone database and the time zone database cannot be initialized.


#### `std::chrono::time_zone` - the class time_zone represents all time zone transitions for a specific geographic area.
```cpp
class time_zone;
```
> Users cannot construct time_zone objects. The library implementation creates time_zone objects when 
it initializes the time zone database and provides const access to these objects.

Member functions:
1. `name` - obtains the name of this time_zone 

2. `get_info` - obtain information associated with a sys_time or local_time 

3. `to_sys` - converts a local_time in this time zone to a sys_time 

4. `to_local` - converts a sys_time to a local_time in this time zone 



# AČIŪ UŽ DĖMESĮ :)

