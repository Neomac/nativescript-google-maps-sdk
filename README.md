NativeScript plugin for Google Maps SDK
================
This is a cross-platform (iOS & Android) Nativescript plugin for the [Google Maps API](https://developers.google.com/maps/)

[![NPM version][npm-image]][npm-url] [![Dependency status][david-dm-image]][david-dm-url]
[npm-url]: https://npmjs.org/package/nativescript-google-maps-sdk
[npm-image]: http://img.shields.io/npm/v/nativescript-google-maps-sdk.svg
[david-dm-url]:https://david-dm.org/dapriett/nativescript-google-maps-sdk
[david-dm-image]:https://david-dm.org/dapriett/nativescript-google-maps-sdk.svg

[![NPM](https://nodei.co/npm/nativescript-google-maps-sdk.png?downloads=true&downloadRank=true&stars=true)](https://npmjs.org/package/nativescript-google-maps-sdk)

Prerequisites
===
*iOS* - Cocoapods is [installed](https://guides.cocoapods.org/using/getting-started.html#getting-started)

*Android* - Latest Google Play services SDK [installed](https://developer.android.com/sdk/installing/adding-packages.html)

*Google Maps API Key* - Go to the [Google Developers Console](https://console.developers.google.com), create a project, and enable the `Google Maps Android API` and `Google Maps SDK for iOS` APIs.  Then under credentials, create an API key.

Installation
===

## Install the plugin using the NativeScript CLI tooling

```
tns plugin add nativescript-google-maps-sdk
```

Setup Google Maps API
===

See demo code included [here](https://github.com/dapriett/nativescript-google-maps-sdk/tree/master/demo)
 
## Setup Android API Key

First copy over the template string resource files for Android

```
cp -r node_modules/nativescript-google-maps-sdk/platforms/android/res/values app/App_Resources/Android/
```

Next modify the file at `app/App_Resources/Android/values/nativescript_google_maps_api.xml`, uncomment `nativescript_google_maps_api_key` string and replace `PUT_API_KEY_HERE` with your api key.

The plugin will default to latest available version of the Android `play-services-maps` SDK.  If you need to change the version, you can add a project ext property `googlePlayServicesVersion` like so:

```
//   /app/App_Resources/Android/app.gradle

project.ext {
    googlePlayServicesVersion = "+"
}
```

## Setup iOS API Key

In the main script of your app `app.js`, use the following to add the API key (providing your key in place of `PUT_API_KEY_HERE`)

```
if(application.ios) {
  GMSServices.provideAPIKey("PUT_API_KEY_HERE");
}
```

##  Adding the MapView

Modify your view by adding the namespace `xmlns:maps="nativescript-google-maps-sdk"` to your page, then using the `<maps:mapView />` tag to create the MapView.

```
 <!-- /app/main-page.xml -->
 <Page 
	xmlns="http://www.nativescript.org/tns.xsd"
	xmlns:maps="nativescript-google-maps-sdk"
	>
  <GridLayout>
    <maps:mapView latitude="{{ latitude }}" longitude="{{ longitude }}" 
    								zoom="{{ zoom }}" bearing="{{ bearing }}" 
    								tilt="{{ tilt }}" padding="{{ padding }}" mapReady="onMapReady"  
   								markerSelect="onMarkerSelect" markerBeginDragging="onMarkerBeginDragging"
   								markerEndDragging="onMarkerEndDragging" markerDrag="onMarkerDrag"
   								cameraChanged="onCameraChanged" />
  </GridLayout>
</Page>
```

The following properties `latitude`, `latitude`, `zoom`, `bearing`, `tilt` and `padding` are available to you for adjusting camera view.

The following events are available:

Event          | Description
-------------- |:---------------------------------
`mapReady`     | Called when Google Map is ready for use
`coordinateTapped` | Fires when coordinate is clicked on map
`coordinateLongPress` | Fires when coordinate is "long pressed"
`markerSelect` | Fires whenever a marker is selected
`shapeSelect` | Fires whenever a shape (`Circle`, `Polygon`, `Polyline`) is clicked.  You must explicity configure `shape.clickable = true;` on your shapes.
`markerBeginDragging` | Fires when a marker begins dragging
`markerDrag` | Fires repeatedly while a marker is being dragged
`markerEndDragging` | Fires when a marker ends dragging
`cameraChanged`| Fired on each camera change


The property `gMap` gives you access to the raw platform Map Object - see their SDK references for how to use them ( [iOS](https://developers.google.com/maps/documentation/ios-sdk/reference/interface_g_m_s_map_view) | [Android](https://developers.google.com/android/reference/com/google/android/gms/maps/GoogleMap) )

```
//  /app/main-page.js

var mapsModule = require("nativescript-google-maps-sdk");

function onMapReady(args) {
  var mapView = args.object;

  console.log("Setting a marker...");
  var marker = new mapsModule.Marker();
  marker.position = mapsModule.Position.positionFromLatLng(-33.86, 151.20);
  marker.title = "Sydney";
  marker.snippet = "Australia";
  marker.userData = { index : 1};
  mapView.addMarker(marker);
}

function onMarkerSelect(args) {
   console.log("Clicked on " +args.marker.title);
}

function onCameraChanged(args) {
    console.log("Camera changed: " + JSON.stringify(args.camera)); 
}

exports.onMapReady = onMapReady;
exports.onMarkerSelect = onMarkerSelect;
exports.onCameraChanged = onCameraChanged;
```

## Using with Angular

```
// /app/map-example.component.ts

import {Component, ElementRef, ViewChild} from '@angular/core';
import {registerElement} from "nativescript-angular/element-registry";

// Important - must register MapView plugin in order to use in Angular templates
registerElement("MapView", () => require("nativescript-google-maps-sdk").MapView);

@Component({
    selector: 'map-example-component',
    template: `
    <GridLayout>
        <MapView (mapReady)="onMapReady($event)"></MapView>
    </GridLayout>
    `
})
export class MapExampleComponent {

    @ViewChild("MapView") mapView: ElementRef;

    //Map events
    onMapReady = (event) => {
        console.log("Map Ready");
    };
}
```
