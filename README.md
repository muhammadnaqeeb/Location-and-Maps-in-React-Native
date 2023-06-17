# Location in React Native
### **Type of Location Access**
**1) Category**
- Foreground Location
- Background Location

**2) Accuracy**
- Approximate
- Precise

### **Foreground Location VS Background Location**

|**Foreground Location**|**Background Location**|
| :-: | :-: |
|Foreground location is when an app gets your location while you're using it.|Background location is when an app continues to access your location even when you're not using it.|
|This is when an app gets access to your location while you are actively using it.|This is when an app continues to access your location even when you are not actively using it or when the app is running in the background.|
|**Examples**|**Examples**|
|**Navigation App**: When you open a navigation app like Google Maps and use it to get directions, the app continuously updates your location in real-time to provide accurate guidance.|**Location-Based Reminder App**: An app that reminds you to buy groceries when you pass by a grocery store can utilize background location.|
|**Fitness Tracking App**: An app that tracks your outdoor activities, such as running or cycling, uses foreground location to monitor your route, speed, and distance as you exercise.|**Weather App**: Some weather apps offer location-based alerts for severe weather conditions. These apps may use background location to provide you with timely weather warnings based on your current location.|

### **Approximate VS Precise Location**

- **Approximate**

Provides a device location estimate. This estimate is accurate to within about 3 square kilometers (about 1.2 square miles). Your app can receive locations at this level of accuracy when you declare the **ACCESS\_COARSE\_LOCATION** permission.

- **Precise**

Provides a device location estimate that is as accurate as possible. This estimate is usually within about 50 meters (160 feet) and is sometimes as accurate as within a few meters (10 feet) or better. Your app can receive locations at this level of accuracy when you declare the **ACCESS\_FINE\_LOCATION** permission.
# Getting User Location 
- **Install**
```
expo install expo-location
```
- **Imports**
```
import {useState, useEffect } from 'react';
```
```
import * as Location from "expo-location";
```
- **Making use of useEffect and useState**

```
const [longitute, setLongitude] = useState();

const [latitude, setLatitude] = useState();

useEffect(()=>{

},[]);
```

- **Request for permission in useEffect**
```
useEffect(() => {
    const getPermissionAndLocation = async () => {
      let { status } = await Location.requestForegroundPermissionsAsync();
      if (status !== 'granted') {
        console.log('Premission Not Granted');
        return;
      }
      console.log("Premission Granted")
    }
    getPermissionAndLocation();

}, []);
```
- **Get user location**
```
let currentLocation = await Location.getCurrentPositionAsync({});
setLatitude(currentLocation.coords.latitude);
setLongitude(currentLocation.coords.longitude);
console.log(currentLocation);
```
Use can also add following inside `getCurrentPositionAsync`
```
accuracy: Location.Accuracy.High,
distanceInterval: 10,
```

- **Converting Longitude and Latitude into address (reverseGeocode)**
```
const [address, setAddress] = useState();
```
Outside the use effect make a function
```
async function reverseGeocode(){
    const reverseGeocodeAddress = await Location.reverseGeocodeAsync({
      longitude:longitute,
      latitude: latitude,
    });
    setAddress(reverseGeocodeAddress[0]['street']);
    console.log(reverseGeocodeAddress);
  }
```
On button click
```
<Button title='Address from L & L' onPress={reverseGeocode}></Button>
      <Text>{address}</Text>
```

- **Convert User Typed Address to geo-coordinates**
```
const [input,setInput] = useState();
```
Outside the use effect make a function
```
async function Geocode(){
    // input -> user typed address
    const GeocodeAddress = await Location.geocodeAsync(input);
    setLatitude(GeocodeAddress[0]['latitude']);
    setLongitude(GeocodeAddress[0]['longitude']);
    console.log(GeocodeAddress);
}
```
Make textfield and button
```
<TextInput placeholder='Enter Address' onChangeText={(v)=>{setInput(v)}}></TextInput>
<Button title='Get Coordinates' onPress={Geocode}></Button>
```
### Complete code
```
import { StatusBar } from 'expo-status-bar';
import { Button, StyleSheet, Text, TextInput, View } from 'react-native';
import { useState, useEffect } from 'react';
import * as Location from "expo-location";

export default function App() {
  const [longitute, setLongitude] = useState();
  const [latitude, setLatitude] = useState();
  const [address, setAddress] = useState();
  const [input,setInput] = useState();

  useEffect(() => {
    const getPermissionAndLocation = async () => {
      let { status } = await Location.requestForegroundPermissionsAsync();
      if (status !== 'granted') {
        console.log('Premission Not Granted');
        return;
      }
      let currentLocation = await Location.getCurrentPositionAsync({});
      setLatitude(currentLocation.coords.latitude);
      setLongitude(currentLocation.coords.longitude);
      console.log(currentLocation);
      
    }
    getPermissionAndLocation();

  }, []);
  
  async function reverseGeocode(){
    const reverseGeocodeAddress = await Location.reverseGeocodeAsync({
      longitude:longitute,
      latitude: latitude,
    });
    setAddress(reverseGeocodeAddress[0]['street']);
    console.log(reverseGeocodeAddress);
  }

  async function Geocode(){
    // input -> user typed address
    const GeocodeAddress = await Location.geocodeAsync(input);
    setLatitude(GeocodeAddress[0]['latitude']);
    setLongitude(GeocodeAddress[0]['longitude']);
    console.log(GeocodeAddress);
  }
  return (
  
    <View style={styles.container}>
      <Text>Latitude: {latitude}</Text>
      <Text>Longitute: {longitute}</Text>
      <Button title='Address from L & L' onPress={reverseGeocode}></Button>
      <Text>{address}</Text>
      <TextInput placeholder='Enter Address' onChangeText={(v)=>{setInput(v)}}></TextInput>
      <Button title='Get Coordinates' onPress={Geocode}></Button>
      <StatusBar style="auto" />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
});
```
![](https://github.com/muhammadnaqeeb/Location-and-Maps-in-React-Native/blob/main/images/001.jpeg)


# Map in React Native
- **Installation**
```
npx expo install react-native-maps
```
- **Import**
```
import MapView from 'react-native-maps';
```
- **Display simple map**
```
<MapView style={styles.map} />
```
```
const styles = StyleSheet.create({
  map: {
    width: '100%',
    height: '75%',
  },
}
```
- **Map with coordinates**
```
<MapView
   style={styles.map}
   //specify our coordinates.
   initialRegion={{
      latitude: latitude ? latitude : 33.5625885,
      longitude: longitute ? longitute : 72.6392806,
      latitudeDelta: 0.0922,
      longitudeDelta: 0.0421,
   }}
/>
```
- **Adding a marker in React Native Maps**
Start by importing Marker from `react-native-maps`.
```
import { Marker } from "react-native-maps";
```
```
<MapView
        style={styles.map}
        //specify our coordinates.
        initialRegion={{
          latitude: latitude ? latitude : 33.5625885,
          longitude: longitute ? longitute : 72.6392806,
          latitudeDelta: 0.0922,
          longitudeDelta: 0.0921,
}}>
<Marker coordinate={{ // <-- See here
          latitude: latitude ? latitude : 33.5625885,
          longitude: longitute ? longitute : 72.6392806,
          latitudeDelta: 0.0922,
          longitudeDelta: 0.0921,
}} />
</MapView>
```
As latitude, longitude, latitudeDelta and longitudeDelta is using two times and having same values so, make a separate object. You can also put these into a state variable.

🔑 **Note:** 

You can also add multiple markers like this
```
<MapView
        style={styles.map}
        //specify our coordinates.
        initialRegion={userCoordinate}>
        <Marker coordinate={userCoordinate} />
        <Marker coordinate={{
          latitude: 33.67714827145542,
          longitude: 72.6551462687416,
        }} />
</MapView>
```
- **Changing the color of marker**
```
<Marker coordinate={{
    latitude: 33.67714827145542,
    longitude: 72.6551462687416,
  }}
  pinColor="green"
/>
```
- **Changing the marker image**

You can also add a custom marker image by passing the image prop to the <Marker /> component.
```
<Marker
  coordinate={{ latitude: 52.5200066, longitude: 13.404954 }}
  image={require("./Flag.png")} //uses relative file path. 
/>
```
- **Using `<polyline />` in React Native Maps**

You can use the `<Polyline />` component from the react-native-maps library to create lines between multiple coordinates. It accepts an array of coordinates in its coordinates prop. You can also specify additional props for styling purposes, such as `strokeWidth`, `strokeColor`, etc.
```
import { Polyline } from "react-native-maps";
```
```
  const userCoordinate = {
    latitude: latitude ? latitude : 33.5625885,
    longitude: longitute ? longitute : 72.6392806,
    latitudeDelta: 0.01,
    longitudeDelta: 0.01,
  }
  const userCoordinate2 = {
    latitude: 33.50,
    longitude: 72.60,
    latitudeDelta:0.01,
    longitudeDelta: 0.01,
  }
```
Inside MapView
```
        <Polyline
        coordinates={[userCoordinate, userCoordinate2]} //specify our coordinates
        strokeColor={"#000"}
        strokeWidth={3}
        lineDashPattern={[1]}
      />
```
### Complete code of location and maps
```
import { StatusBar } from 'expo-status-bar';
import { Button, StyleSheet, Text, TextInput, View } from 'react-native';
import { useState, useEffect } from 'react';
import * as Location from "expo-location";
import MapView, { Callout } from 'react-native-maps';
import { Marker } from "react-native-maps";
import { Polyline } from "react-native-maps";

export default function App() {
  const [longitute, setLongitude] = useState();
  const [latitude, setLatitude] = useState();
  const [address, setAddress] = useState();
  const [input, setInput] = useState();

  useEffect(() => {
    const getPermissionAndLocation = async () => {
      let { status } = await Location.requestForegroundPermissionsAsync();
      if (status !== 'granted') {
        console.log('Premission Not Granted');
        return;
      }
      let currentLocation = await Location.getCurrentPositionAsync({});
      setLatitude(currentLocation.coords.latitude);
      setLongitude(currentLocation.coords.longitude);
      console.log(currentLocation);

    }
    getPermissionAndLocation();

  }, []);

  async function reverseGeocode() {
    const reverseGeocodeAddress = await Location.reverseGeocodeAsync({
      longitude: longitute,
      latitude: latitude,
    });
    setAddress(reverseGeocodeAddress[0]['street']);
    console.log(reverseGeocodeAddress);
  }

  async function Geocode() {
    // input -> user typed address
    const GeocodeAddress = await Location.geocodeAsync(input);
    setLatitude(GeocodeAddress[0]['latitude']);
    setLongitude(GeocodeAddress[0]['longitude']);
    console.log(GeocodeAddress);
  }
  const userCoordinate = {
    latitude: latitude ? latitude : 33.5625885,
    longitude: longitute ? longitute : 72.6392806,
    latitudeDelta: 0.01,
    longitudeDelta: 0.01,
  }
  const userCoordinate2 = {
    latitude: 33.50,
    longitude: 72.60,
    latitudeDelta:0.01,
    longitudeDelta: 0.01,
  }
  return (

    <View style={styles.container}>
      <Text>Latitude: {latitude}</Text>
      <Text>Longitute: {longitute}</Text>
      <Button title='Address from L & L' onPress={reverseGeocode}></Button>
      <Text>{address}</Text>
      <TextInput placeholder='Enter Address' onChangeText={(v) => { setInput(v) }}></TextInput>
      <Button title='Get Coordinates' onPress={Geocode}></Button>

      <MapView
        style={styles.map}
        //specify our coordinates.
        initialRegion={userCoordinate}>
        <Marker coordinate={userCoordinate} />
        <Marker coordinate={userCoordinate2}
        pinColor="green"
        />
        <Polyline
        coordinates={[userCoordinate, userCoordinate2]} //specify our coordinates
        strokeColor={"#000"}
        strokeWidth={3}
        lineDashPattern={[1]}
      />
      <Callout>
        
      </Callout>
      </MapView>
      <StatusBar style="auto" />
    </View>
  );
}

const styles = StyleSheet.create({
  map: {
    width: '100%',
    height: '75%',
  },
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center',
  },
});

```
![](https://github.com/muhammadnaqeeb/Location-and-Maps-in-React-Native/blob/main/images/002.jpeg)

