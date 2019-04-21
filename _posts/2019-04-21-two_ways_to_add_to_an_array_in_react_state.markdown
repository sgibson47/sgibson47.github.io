---
layout: post
title:      "Two ways to add to an Array in React State"
date:       2019-04-21 19:07:40 +0000
permalink:  two_ways_to_add_to_an_array_in_react_state
---


## Why I needed to know
This week I was working on Find Some Fun, a React application that queries Eventbrite’s API for upcoming events near the user and displays those events to the user.  In particular, this week I was working on querying Eventbrite’s API for location information regarding each event’s venue so that I could display a marker on Find Some Fun’s map at each event’s venue. 

In order to keep track of the venues I decided to store them in an array in the App component’s state, just as I had the events I had collected from the API.  However, retrieving the venue locations involved a different process than retrieving the events. When I queried the API for the events, the response I received included a collection of events that I could then store in App’s state.  But to retrieve venue locations, I had to query the API for each venue’s location and then add each response to the collection stored in state. As a result, where I had written over the array stored at App.state.events with each new collection of events from Eventbrite’s API, I needed to preserve the venues in App.state.venues and add to that collection with each API request. 

Below, I walk through why the first tactic I tried didn’t work and two methods that will. 

## How to add to an Array in React State

### Why .push() won’t work

My first inclination was to simply push the venue object returned from my request to Eventbrite’s API into the collection stored in App’s state, like this:
```
addVenueToState(venue){
    this.setState(prevState => {
      const newVenues = prevState.venues.push(venue);

      return {venues: newVenues};
    });
  }
```
But, you can’t alter a component’s state directly in React so this won’t work because `.push()` is an impure function, i.e. it changes the array it’s called on in place.  In addition, `.push()` doesn’t return the array it altered. It returns the new length of the altered array.  As a result, using `.push()`  would not only break React rules about how one should modify state, but also, using it as I did above, wouldn’t result in App.state.venues holding the information I intended. 


### Use .concat() or the spread operator

Instead of using `.push()` to act on the array stored in my component’s state, I needed to add the new information I wanted in state to a copy of the existing collection with a method that didn’t alter the original and returned a new, modified array.  The `.concat()` method and the spread operator (`…`) both meet these needs. 

Here’s how I could re-write my function to add a venue to the collection in my component’s state with `.concat()`.
```
addVenueToState(venue){
    this.setState(prevState =>{
      const newVenues = prevState.venues.concat([venue])
      
      return {venues: newVenues}
    })
  }
```

And here’s how I could re-write the function to use the spread operator.
```
addVenueToState(venue){
    this.setState(prevState =>{ 
      // adding the new venue to the end of the collection
      const newVenues = [...prevState.venues, venue]
      // or, add the new venue to the beginning of the collection
      //  const newVenues = [venue, ...prevState.venues]
      return {venues: newVenues}
    })
  }
```

