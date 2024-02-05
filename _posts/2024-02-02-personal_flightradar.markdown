---
layout: post
title:  "Personal Flightradar: Show the aircraft you are tracking with Fr24"
date:   2024-02-03 17:09:00 +0100
categories: personal_projects aviation
---

<div
    style="
        max-height: 300px;
        max-width: 100%;
        overflow: hidden;
    "
>
<img img src="/resources/post_flightradar/flightradar.png" alt="Flightradar pic">
</div>
<br>

### Referenced projects:
- [Fr24Proxy](https://github.com/benedictweichselbaum/fr24proxy)
- [Flightradar Visualizer](https://github.com/benedictweichselbaum/flightradar_visualizer)

#### Share your flight data!
Many people know the popular website [Flightradar24](https://www.flightradar24.com/).
The website gathers flight data from many data sources to deliver its service. One possible data source for the site is private ADS-B receivers. Using one of these receivers (they generally do not cost more than 40 €) and a Raspberry Pi it is possible to share data with Flightradar. For that, a custom image (Fr24) for the Pi is provided. For your data, Flightradar gifts you a Business Subscription of their service that gives you more insight into all tracked flights. You can also see how much data you actually receive and, therefore, share. This can vary a lot depending on the position of your receiver. 

#### Where are MY aircraft?
One of my main problems now was: **What are actually the aircraft I track that can be seen on the map?** Flightradar does not show you this information. The only possibility to view the currently tracked flights of your receiver is a rather ugly textual representation in the integrated Fr24-GUI:

<div
    style="
        max-height: 300px;
        max-width: 100%;
        overflow: hidden;
    "
>
<img img src="/resources/post_flightradar/ugly_feed.png" alt="Ugly Fr24 Feed">
</div>
<br>

But when there is an ugly GUI, at least the data available via an API. This inspired me to write a very simple "private" flight radar. I had two main problems ahead of me:
- Converting the horrible data from the Fr24 API.
- Not getting CORS-errors when accessing the data from the API.

For both problems, I created a proxy service written with Spring Boot (total overkill, I know, but it's what I know well, and it was just a quick "on the weekend" project.). The service offers an endpoint that returns the currently tracked files with the following information:
- Call Sign
- Altitude
- Squawk Code
- Position

A possible JSON can look like this:

```json
[
    {
        "call_sign": "",
        "altitude": 39025,
        "latitude": 50.18,
        "longitude": 10.43,
        "squawk": "0512"
    },
    {
        "call_sign": "ETH707",
        "altitude": 25175,
        "latitude": 48.98,
        "longitude": 10.21,
        "squawk": "2563"
    },
    {
        "call_sign": "DLH4AC",
        "altitude": 21000,
        "latitude": 48.75,
        "longitude": 11.22,
        "squawk": "6661"
    },
    ...
]
```

The outgoing data from Fr24 itself also delivers a JSON. But every flight in the single JSON object is just an ordered array. No information is given which data entry means what. This looks like this:

```json
{
     "3007f2": [
        "3007f2",
        0,
        0,
        112.80102,
        30300,
        510,
        "1000",
        0,
        "",
        "",
        1707168050,
        "",
        "",
        "",
        false,
        1024,
        "DLA95N"
    ],
    ...
}
```
Thankfully, the order within the array does not change. So if you know the position, mapping to a nice object is pretty easy. The proxy service that also resolved my CORS problems when I tried to call the Fr24 backend directly can be found [here](https://github.com/benedictweichselbaum/fr24proxy) (Fr24Proxy).

Finally, the prepared data needs to be displayed. For that, I quickly wrote a small Angular frontend. This frontend gets the Fr24Proxy-data and shows it on a map. To achieve that, the application has to be configured with the server URL. After that is set, the data gets regularly polled. The used map is a Leaflet map (Open Street Map). To display the planes, the marker feature of Leaflet is used. It is also possible to click on each plane and get all the fetched information and a link to the plane directly on Flightradar24 to get even more information.

<div
    style="
        max-height: 300px;
        max-width: 100%;
        overflow: hidden;
    "
>
<img img src="/resources/post_flightradar/flight_map_popup.png" alt="Ugly Fr24 Feed">
</div>
<br>

The frontend can be found [here](https://github.com/benedictweichselbaum/flightradar_visualizer).

If you want to deploy the applications yourself modification of the code surely is needed. Feel free to do so! :)