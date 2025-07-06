# trmnl_noaa_tides

A Plugin for the TRMNL device that shows upcoming low and high tides. On weekdays, it also highlights the upcoming weekend high tides.

To identify your station ID, go to https://tidesandcurrents.noaa.gov/.

## How It Works

When this recipe polls, it fetches two datasets.

1. Get the station's location information.

```txt
https://api.tidesandcurrents.noaa.gov/mdapi/prod/webapi/stations/{{ station_id }}.json
```

2. Get the upcoming tide predictions.

```txt
https://api.tidesandcurrents.noaa.gov/api/prod/datagetter?
  begin_date={{ "now" | date: "%Y%m%d" }}
  &range=192
  &interval=hilo
  &station={{ station_id }}
  &product=predictions
  &datum={{ datum }}
  &time_zone=gmt
  &units={{ units }}
  &format=json
```

Fetches 192 hours (8 days) to ensure the dataset includes the next upcoming weekend.
