---
layout: post
title:      "Incorporating Feedback on my React Project"
date:       2019-01-28 19:03:37 -0500
permalink:  incorporating_feedback_on_my_react_project
---


The assessment for my final project happened yesterday, and I’m happy to report that I passed!  While positive feedback is my favorite, it’s also important to listen to criticism and learn from the suggestions of others.   In this post I want to talk about a change to my application that my assessor, Alice Balbuena, suggested and how I implemented that change. 

## The Suggestion
My project allowed users create and play instances of a game called, The Game.  I set up the application so that when a user navigated to “/new” a simple form to create a new game would be displayed.  The form took in a player name to be associated with the game, and when the form was submitted, the application made an asynchronous request to its backend.  

The backend would then create a new game in the database with the provided player name and get the game ready to play (give it a deck of cards, shuffle & deal). Once, the game was set up, the backend would respond to the request with the newly created game serialized with all the data the frontend would need about it display the current state of the game and allow a user to play. 

Then the application would use a small piece of the game data returned by the backend, the new game’s id, to redirect the user to the new game’s show page where they could play the game. 

After the component that held the show page loaded, my application would make another asynchronous request to its backend to retrieve data about the game and place that data in its Redux store so that it could present the game for play to the user. 

My assessor, Alice, noticed inefficiency in this process. I was requesting the same information twice.  My application’s backend had provided all the information I was requesting about the new game just after the show page loaded when it responded to the request to create a new game.  Alice suggested that instead of requesting the information twice, I ought to add the new game’s data to the Redux store when the backend provided it the first time and make the request that had been firing just after the show page loaded conditional on whether the correct game’s data had already been loaded.  

Alice was right.  Why make an unnecessary request to the application’s backend? It added unnecessary steps that could only slow my application down. 

## The Implementation
Having seen the error of my ways, I set about implementing Alice’s suggestion and improving my project. 

First, I updated the application to add the new game’s data to the Redux store when the backend provided it in response to the request creating a new game before redirecting to the new game's show page. 

```
export const newGame = (playerName, history) =>{
  return dispatch => {
    return fetch(`${API_URL}/games`, {
      method: 'POST',
      body: JSON.stringify({game:{ playerName: playerName }}),
      headers:{
        'Content-Type': 'application/json'
      }
    })
      .then(response => response.json())
      .then(game => {
        dispatch(setGame(game));
        history.push(`/games/${game.id}`);
      })
      .catch(error => console.log(error))
  }
}
```

Inside the action creator function newGame that is called whenver the form to create a new game is submitted, I added `dispatch(setGame(game));`.  set Game is a reducer that adds the data provided to it as an argument to the Redux store. 

Second, I made the show page’s request for game data contingent on whether the correct game’s data was already in the Redux store. 

This lifecycle method:

```
  componentDidMount(){
    const gameId = this.props.match.params.gameId
    this.props.getGame(gameId)
```

became this:

```
  componentDidMount(){
    const gameId = this.props.match.params.gameId
    if(this.props.currentGame.id === gameId){

    }else{
      this.props.getGame(gameId)
    }
  }
```

With these changes in place my application is more efficient!  Thanks to Alice for the feedback and thanks to you for reading. If you’d like to look at all the code for this project, check it out its GitHub repository [here](https://github.com/sgibson47/play-the-game). 


