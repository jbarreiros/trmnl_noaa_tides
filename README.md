# trmnl_noaa_tides

![](assets/screenshot.png)

A [TRMNL plugin](https://usetrmnl.com) that shows upcoming low and high tides. On weekdays, it also highlights the upcoming weekend high tides.

To identify your station ID, go to https://tidesandcurrents.noaa.gov/.

## How It Works

When this recipe polls, it fetches three datasets.

(1) Get 8 days of tide predictions, starting from yesterday.

```txt
https://api.tidesandcurrents.noaa.gov/api/prod/datagetter?
  begin_date={{ "now" | date: "%s" | minus: 86400 | date: "%Y%m%d" }}
  &range=192
  &interval=hilo
  &station={{ station_id }}
  &product=predictions
  &datum={{ datum }}
  &time_zone=gmt
  &units={{ units }}
  &format=json
```

- This plugin displays the current tide and _n_ upcoming tides.
- Due to the "Polling URL" not having access to `trmnl.user.utc_offset`, the `begin_date` may actually be a day ahead. To work around that, start with yesterday.
- 8 days of predictions are fetched so that on Monday, the dataset includes the next upcoming weekend.

(2) the station's `name` (city/town) and `state`.

```txt
https://api.tidesandcurrents.noaa.gov/mdapi/prod/webapi/stations/{{ station_id }}.json
```

(3) Get the latest 5-min tides.

```txt
https://api.tidesandcurrents.noaa.gov/api/prod/datagetter?
  date=latest
  &station={{ station_id }}
  &product=predictions
  &datum=MLLW
  &time_zone=gmt
  &units=english
  &format=json
```

This fetch does not provide any data for display. Its purpose is to supply frequently updated data, ensuring TRMNL refreshes the screen at your selected interval. This is necessary because the tide predictions API always covers an entire day, meaning the data is only considered fresh once a day.

## Development

Used [trmnlp](https://github.com/usetrmnl/trmnlp) for local development. That's what the `bin/trmnlp` symlink is pointing to.
