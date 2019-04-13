---
layout: post
title:      "How to rely on a parent component’s state changes"
date:       2019-04-13 17:43:28 +0000
permalink:  how_to_rely_on_a_parent_component_s_state_changes
---


## The issue

In React, if you rely on a value from a parent component’s state in its child, e.g. you condition displaying some content in the child on the value of the parent’s state passed down via props, and the parent component’s state is updated, the parent component will re-render, but the child won’t.  

## How the issue presented itself to me

This week I was working on my new React app. It queries Eventbrite’s API for local events based on keywords and categories selected by the user and then displays them.  This week I was working on adding a map centered on the user’s location and which had a marker there, as well.  My goal is to eventually query Eventbrite’s API for the location of each event displayed to the user, so I can put markers on the map for each one. 

For now, I needed to decide how I would initially present the map because it would be on the page before the application was able to obtain the user’s location.  I decided that at first the map ought to be zoomed way out so that you couldn’t tell that it was centered over an arbitrary location and that it ought not to have a marker. Then, once the application had the user’s location, the map should zoom in, update to center itself on the user’s location, and add a marker there.  

I was getting the user’s location in my top most component, App, storing the retrieved location in App’s local state, updating a Boolean value indicating whether the user’s location had been retrieved and passing both down to the map component via props.  Inside the map component, I was storing that passed down location in the map’s local state, centering the map on it, and using the Boolean value to tell the map whether to zoom in and add a marker.  

However, when App’s state updated with the user’s location and the Boolean representing whether it had the user’s location was changed from false to true, the map component wasn’t changing as I expected.  It wasn’t centering itself on the newly retrieved location, zooming in and adding a marker.  This was because I had conditioned the re-centering, zooming, and adding a marker on the Boolean value passed from App and it wasn’t updating when App’s state was changed. 

## How to get around the issue

To fix this while still relying on the parent component’s state passed down via props, we’re going to use the lifecycle method `componentDidUpdate()`.  It is invoked immediately after the component updates and is a good place to compare current and previous props. 

Let’s look at a simplified version of my App component to get some context for why we’ll use `componentDidUpdate()` in App’s child component MyMap.

```
class App extends Component {
  constructor(props){
    super(props);
    this.state={
      location: {
        lat: 51.505,
        long: -0.09
      },
      hasUserLocation: false
    }
  }

  componentDidMount(){
  // try to get user’s location from the browser  
    navigator.geolocation.getCurrentPosition((position) =>{
      // if user allows, store location in state & update hasUserLocation
      this.setState({
        location:{
          lat: position.coords.latitude,
          long: position.coords.longitude
        },
        hasUserLocation: true
      })
    }, () =>{
      // if user refuses, get approx. location from IP address
      fetch('https://ipapi.co/json')
        .then(response => response.json())
        .then((location)=>{
          // store location in state & update hasUserLocation
          this.setState({
            location:{
              lat: location.latitude,
              long: location.longitude
            },
            hasUserLocation: true
          })
        })
    })
  }
  
  render() {
    return(
      <MyMap 
            lat={this.state.location.lat} 
            long={this.state.location.long} 
            hasUserLocation={this.state.hasUserLocation}
          />
    );
  }
}
```

We set App’s initial state to store an arbitrary location and set its hasUserLocation attribute to false. Then, once the component has mounted we get the user’s location and update App’s state to reflect the new information we have.  Lastly, we pass the location information and the Boolean value indicating whether we have the user’s location down to MyMap via props. 

Here’s the rub. When App’s state is updated to reflect having obtained the user’s location, its child component, MyMap, won’t be re-rendered.  As a result, it will rely on the old props values and not properly update to show the user’s location. 

Let’s use `componentDidUpdate()` in MyMap to ensure that MyMap re-renders and relies on the newly retrieved user’s location rather than the initial, arbitrary place first passed to it from App. 

```
class MyMap extends Component{
    constructor(props){
        super(props)
        this.state = {
            lat: this.props.lat,
            lng: this.props.long,
            zoom: 2
        }
    }

  componentDidUpdate(prevProps) {
        if (this.props.hasUserLocation !== prevProps.hasUserLocation) {
            this.setState({
                lat: this.props.lat,
                lng: this.props.long,
                zoom: 15
            });
        }
    }
}
``` 

For this example, I left out rendering a map centered on the location stored in state, because it’s not relevant to our discussion of `componentDidUpdate()`.

In MyMap, we used `componentDidUpdate()` to compare the current, updated props value to the previous value. Where they differ, we’ll update MyMap’s state to store the new props values and zoom in on the map. Using `setState()` will cause MyMap to re-render and display an updated map, now centered on the user’s location and zoomed in.  If you’re going to use `componentDidUpdate()` in your own app, make sure you condition any use of `setState()` inside `componentDidUpdate()` on a change in props or else your app will fall into an infinite loop of updating itself via `setState()` which will trigger `componentDidUpdate()` which will again call `setState()` over and over. 

## That’s it

In order to rely on a parent component’s state changes to trigger changes in a child, use `componentDidUpdate()` to compare the child’s current and previous props to catch the changes to the parent’s state. 
