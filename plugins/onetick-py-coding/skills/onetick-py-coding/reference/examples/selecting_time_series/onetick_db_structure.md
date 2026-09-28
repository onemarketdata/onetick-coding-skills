# OneTick database structure

## Ticks

OneTick DB operates with ticks. Tick is a smallest entity that contains timestamp (it’s mandatory field a primary index since OneTick DB is a time series DB) with up to nanoseconds precision and other optional fields with different types.

Data is a set of ordered by timestamp ticks.

Tick schema is a set of fields and their types that belong to a tick. Generally the OneTick supports different tick schemas for different ticks.

## Daily archives

An OneTick DB stores data in daily archives on the file system. Physically it means data is splitted into days by timestamps into subfolders.

Users could query data from any time range intervals.

Write operation requires date to write ticks; all timestamps should belong to this date (there are some advanved options to overcome this limitatation but it will be discussed later).

## Tick type

Tick type is a type of data inside the day of an OneTick database. It allows to distinguish different types of data in the database.

For example, one day could contains quotes and trades with QTE and TRD tick types accordingly. It could be trades and quotes from one exchange that’s naturally store in single database.

Ticks between different tick types usually have different tick schema, ie set of fields.

Tick type name could be any string, and it’s specified when data is written as a required parameter.

## Symbol

The Symbol is an mandatory index that allows to store ticks inside the tick type and split by instruments. Technically ticks inside one instrument might have different schema but mostly they have the same schema. Moreover we strongly recommend to keep schema consistent between ticks inside a symbol, and even between different symbols for the same tick type

## Summary

Every tick identifies with the set:

- DB name
- Date / dates or query interval; they are just synonyms
- Tick type
- Symbol

OneTick users can’t read or write a tick if any part of the set is missed.

If you have an issue related to missing database, tick type or symbol then just need to check that all tick sources specify them properly.
There is another way of possible issues – a tick source defines something multiple times that contradicts each other. The OneTick allows to specify components of that set in different ways, and it might happen that a developer specifies symbol, for example, for one ticks source twice with different values, and therefor OneTick doesn’t understand how to deal with it.
