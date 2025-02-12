#### Date/time durations

All date/time durations in the configuration files are specified as defined by ISO 8601.

Durations are represented by the format `P[n]Y[n]M[n]DT[n]H[n]M[n]S`. In these representations, the `[n]` is replaced by the value for each of the date and time elements that follow the `[n]`. Leading zeros are not required. The capital letters 'P', 'Y', 'M', 'D', 'T', 'H', 'M', and 'S' are designators for each of the date and time elements and are not replaced.

- P is the duration designator (historically called "period") placed at the start of the duration representation.
- Y is the year designator that follows the value for the number of years.
- M is the month designator that follows the value for the number of months.
- D is the day designator that follows the value for the number of days.
- T is the time designator that precedes the time components of the representation.
- H is the hour designator that follows the value for the number of hours.
- M is the minute designator that follows the value for the number of minutes.
- S is the second designator that follows the value for the number of seconds.

For example, `P3Y6M4DT12H30M5S` represents a duration of "three years, six months, four days, twelve hours, thirty minutes, and five seconds". Date and time elements including their designator may be omitted if their value is zero, and lower order elements may also be omitted for reduced precision. For example, "P23DT23H" and "P4Y" are both acceptable duration representations.

Exception: A year or month vary in duration depending on the current date. For OpenDNSSEC, we assume fixed values:

 - One month is assumed to be 31 days.
 - One year is assumed to be 365 days.
