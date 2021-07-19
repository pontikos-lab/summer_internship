# Week 4-5
- `ssh -Y overdrive`
- `zless chr14_variants.vcf.gz`
- `R`

`library(data.table)`
3 ways to create one:
- `data.table()`
- `as.data.table()`
- `fread()`

General form of data.table syntax:
- `DT[i, j, by]` take DT, filter rows in i, compute j, grouped by by

example commands:
- `nrow()`, `ncol()`, `dim()`
- `x_dt <- data.table(id = 1:2, name = c("a","b"))`
- `head(batrips, 8)`
- `str(batrips)`
- `not_first_last <- batrips[-c(1, .N)]`
- `trips_mlk_1600 <- batrips[start_station == "MLK Library" & duration > 1600]`

%%
- `%like%` filters rows from data.tables that match a pattern, as opposed to exact matches. It can be used independently on a vector as well.
  - `end_markets <- batrips[end_station %like% "Market$"]` a $ sign at end means look for pattern at the end of a string
- `%in%` allows selecting rows that exactly matches one or more values.
  - `filter_trip_ids <- batrips[trip_id %in% c(588841, 139560, 139562)]`
- `%between%` works only on numeric columns and can be used to filter values in the closed interval [val1, val2].
- `%chin%` works only on character columns and is an efficient version of `%in%`. Use it to look for specific strings in a vector.
  - `duration_5k_6k <- batrips[duration %between% c(5000, 6000)]`
  - `two_stations <- batrips[start_station %chin% c("San Francisco City Hall", "Embarcadero at Sansome")]`

Columns
```
ans <- batrips[, c("trip_id", "duration")]
head(ans, 2)
```
- `ans <- batrips[, -c("start_date", "end_date", "end_station")]` to select all cols except
- `ans <- batrips[, list(trip_id, dur = duration)]` use a list of column names to select cols
  - but when selecting a single col, not wrapping the variable by list() returns a vector
- `.()` is the same as `list()`
  - `ans <- batrips[, .(trip_id, duration)]`
<br>

- `ans <- batrips[, mean(duration)]` instead of
- `ans <- mean(batrips[, "duration"])`
- `batrips[start_station == "Japantown", .N]` useful when filtering rows in i
- `median_duration_filter <- batrips[end_station == "Market at 10th" & subscription_type == "Subscriber", median(duration)]`
- `batrips[, .(mn_dur = mean(duration), med_dur = median(duration))]` computes multiple columns and returns a data.table
```
duration_stats <- batrips[start_station == "Townsend at 7th" & duration < 500, .(min_dur = min(duration), max_dur = max(duration))]
batrips[start_station == "Townsend at 7th" & duration < 500, hist(duration)]
```

Computations by groups
- `ans <- batrips[, .N, by = "start_station"]` when performing a grouping operation, `.N` contains the number of rows for each group
- The `list()` or `.()` expression in `by` allows for grouping variables to be computed on the fly
  - `mean_start_station <- batrips[, .(mean_duration = mean(duration)), by = .(start_station, month = month(start_date))]`
- E.g.:
  - `aggregate_min_max <- batrips[, .(min_duration = min(duration), max_duration = max(duration)), by = .(start_station, end_station, month = month(start_date))]`

Chaining data.table expressions
- `batrips[, .(mn_dur = mean(duration)), by = "start_station"][order(mn_dur)][1:3]`
- `uniqueN()` is a helper function that returns an integer value containing the number of unique values in the input object (accepts vectors, data.frames, data.tables)
  - `ans <- batrips[, uniqueN(bike_id), by = month(start_date)]`
  - `trips_dec <- batrips[, .(.N), by = .(start_station, end_station)]`
  - `top_5 <- batrips[, .N, by = end_station][order(-N)][1:5]` arrange total trips in decr order, then filter top 5 rows
  - `first_last <- batrips[order(start_date), .(start_date = start_date[c(1, .N)]), by = start_station]`
- `.SD` is a special symbol which stands for Subset of Data. Contains subset of data corresponding to each group; which itself is a data.table. By default, the grouping columns are excluded for convenience.
  - `x[, print(.SD), by = id]`
  - `x[, .SD[.N], by = id]`
- `.SDcols` holds the columns that should be included in `.SD`
  - `batrips[, .SD[1], by = start_station, .SDcols = - c("trip_id", "duration")]`
  - `shortest <- batrips[, .SD[which.min(duration)], by = month(start_date), .SDcols = relevant_cols]`
  - `unique_station_month <- batrips[, lapply(.SD, uniqueN), by = month(start_date), .SDcols = c("start_station", "zip_code")]` to find total number of unique start stations and zip codes per month

Adding and updating columns by reference
- internally, data.table updates columns by reference, so you don't need to assign the result back to a variable. No copy of any column is made while their values are changed. data.table uses a new operator `:=` to add/update/delete columns
- LHS := RHS form:
  - `batrips[, c("is_dur_gt_1hour", "week_day") := list(duration > 3600, wday(start_date))]`
  - `batrips[, is_dur_gt_1hour := duration > 3600]` When adding a single column quotes aren't necessary
- Functional form:
  - `batrips[, `:=`(is_dur_gt_1hour = NULL, start_station = toupper(start_station))]`
  - when using operators as functions, they need to be wrapped in backticks
-  `untidy[2, start_station := "San Francisco City Hall"]` fix spelling in the second row of start_station using the LHS := RHS form
- `untidy[duration < 0, duration := NA]` replace negative duration values with NA

Grouped aggregations
- `batrips[, n_zip_code := .N, by = zip_code][]` [] will print updated data.table
```
zip_1000 <- batrips[, n_zip_code := .N, by = zip_code][n_zip_code > 1000][, n_zip_code := NULL]`
```
- `batrips[, duration_mean := mean(duration), by = .(start_station, end_station)]` add new column for every start_station and end_station
- 

- nrow
- skip






