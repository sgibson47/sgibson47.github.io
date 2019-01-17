---
layout: post
title:      "Lessons from the React Redux Portfolio Project "
date:       2019-01-17 23:46:56 +0000
permalink:  lessons_from_the_react_redux_portfolio_project
---

## 2 challenges and what I learned from them

Get excited for me strangers, I recently completed my final portfolio project! I made a web application called Play The Game with a Rails API backend and a React with Redux frontend that lets users play a game called The Game.  

The Game was developed by [IDW Games](https://idwgames.com). It challenges players to run out a deck of cards numbered 2-99 by playing each in ascending or descending order on 4 piles. 

For this blog post accompanying the project, I want to discuss a couple of the challenges I ran into while developing Play The Game.  In particular, I want discuss two of the times that I didn’t know how to solve a problem I had diagnosed and a Google search to for answers indicated that the solution was something I’d never heard of or didn’t know how to use. 

First, I ran into trouble when setting up the Active Record associations between models in the Rails API backend. I tried to give a model multiple belongs_to associations and learned about polymorphic associations when it didn’t work.
Second, I had difficulty figuring out how to redirect to a newly created game’s show page after submitting the form to create it.  Solving that puzzle taught me about withRouter and the history object. 

I’m hopeful that by examining these difficulties|hardships|challenges|hurdeles|failures|problems|puzzles I’ll crystalize these examples in my mind so I’ll have a better chance of applying their lessons in the future.  And, by publishing this, maybe other people can learn from my mistakes, as well. 

## polymorphic associations 
One of the first things I did after spinning up Play The Game’s backend with the ‘new rails <appname> --api’ command, was generate models and associate them with one another to keep track of rounds of The Game in a database.  My goal was to set up a structure that would enable me to retrieve a single game from my database and  render it as json in my backend’s response to the frontend with all the details I’d need to display the saved state of the game.

I came up with a structure of models and associations that I thought would get the job done.  I made Game, Deck, Hand, Pile & Card models and gave them the following associations.

```
class Game < ApplicationRecord
  has_many :piles
  has_one :deck
  has_one :hand
end

class Deck < ApplicationRecord
  belongs_to :game
  has_many :cards
end

class Hand < ApplicationRecord
  belongs_to :game
  has_many :cards
end

class Pile < ApplicationRecord
  belongs_to :game
  has_many :cards
end

class Card < ApplicationRecord
  belongs_to :pile
  belongs_to :deck
  belongs_to :hand
end
```

I thought this would let me give each card a pile_id or a deck_id or a hand_id, i.e. that I could create cards where only one of these attributes had a value. I also thought this set up would allow me to retrieve a game with its deck, hand, and piles each with their respective collection of cards.  

I found out that I was wrong when I was trying to walk through a game with just the database the Rails console.  I created a game, gave it a deck, hand, and piles.  I planned to create cards and then mimic game play by clearing the card’s deck_id and giving a value to its hand_id to deal a card, clearing the card’s hand_id and giving a value to its pile_id to play a card on a pile, . . . etc.  When I went to create cards in the deck I couldn’t.  The database wouldn’t save a card that didn’t have a value associated with the card’s pile_id, deck_id, and hand_id.  

I learned that the card wouldn’t save because Rails requires that the associated record be present, i.e. the foreign key column must have a value in it, by default.   Thus, my plan to leave 2 of 3 foreign keys on each card empty wasn’t working.  

After some Googling, I found polymorphic associations.  Polymorphic associations allow a model to belong to more than one other model on a single association.   Perfect! It was a way for my card model to belong to either a deck, hand, or pile.  Even better, when I searched for resources to learn about how to implement this new kind of association, there was [a section of a Rails Guide on polymorphic sssociations](https://guides.rubyonrails.org/association_basics.html#polymorphic-associations) that clearly explained what polymorphoic associations were and how to use them.

After reviewing that resource, I declared the associations like this:

```
class Card < ApplicationRecord
  belongs_to :whereIsCard, polymorphic: true
end

class Deck < ApplicationRecord
  belongs_to :game
  has_many :cards, as: :whereIsCard
end

class Hand < ApplicationRecord
  belongs_to :game
  has_many :cards, as: :whereIsCard
end

class Pile < ApplicationRecord
  belongs_to :game
  has_many :cards, as: :whereIsCard
end
```

To make this work, I also had to change the Cards database table to reflect the polymorphic association.  Declaring a polymporphic interface involves declaring both a foreign key column and a type column.  So I rolled back the migration that created my Cards table with 3 foreign key columns and created the following migration.

```
class CreateCards < ActiveRecord::Migration[5.2]
  def change
    create_table :cards do |t|
      t.integer :value
      t.references :whereIsCard, polymorphic: true, index: true

      t.timestamps
    end
  end
end
```

This created a Cards table that had, among others, a "whereIsCard_type" column and a "whereIsCard_id" column.  The combination of associations, type column and foreign key column gave me access to an interface to retrieve all the cards in a deck, hand, or pile (e.g. @deck.cards, @hand.cards, or @pile.cards) or to determine where a particular card was located (@card.whereIsCard). 

Now my models and associations were accurately representing that a card is either in (belongs_to) a deck, a hand, or a pile.  I was able to create a game in the Rails console, give it a deck, hand, and piles, then give the deck cards numbered 2-99, shuffle them, deal them to the game’s hand and play some of them by moving them from the hand to a pile.  In short, I was able to ‘play’ a game.


## withRouter and the history object

I also learned entirely new material when I was setting up a way to create a new game.  I created a component called NewGame that contained a form to create a new game that had an text field input to take in the name of the game’s player. Next, I set up the create action of my games controller to make a new game, set its playerName attribute to the name provided via params, set up the game up to play (create a deck, hand, piles, cards, shuffle & deal), and respond with the newly created game rendered as json. Then I set up an action creator function, newGame, that would take in the playerName and make the POST request to ‘/games’ with the playerName.  I imported that function into my App component, mapped it to props, and passed it down to the NewGame component via props.  Inside the NewGame component, I called newGame within the handleSubmit function so that it would be triggered when the form was submitted.  

With all this in place, I was creating a new game in the database with the data from the form I’d built in NewGame, but after the game was created nothing was happening on the client side.  After submitting the form displayed at ‘/new,’ the browser would remain at that url and the only way to see the new game existed was to navigate to ‘/games.’  Instead I wanted the application to redirect the browsers to the new game’s show page after it had been created.

When I Googled something like ‘redirect React router’ I found a wonderful resource of examples and explanations of the tools provided by React Router DOM, [React Training](https://reacttraining.com/react-router/web/ guides/quick-start).

Among the examples React Training provided was one titled [Redirects (Auth)](https://reacttraining.com/react-router/web/example/auth-workflow). ‘Redirects (Auth)’ was a simple example of how to protect a page by requiring a user to log in.  I thought it looked helpful because it was doing a lot of redirecting in different ways. 

I spent a while learning about each part and understanding how they all worked together.  It was a challenge but I learned a lot.  In particular, I learned about a tool that would allow me to create the functionality I was after, withRouter. 

withRouter is a higher-order component that can be imported from React Router DOM. When you wrap it around a component, it gives access to the history object's properties and the closest <Route>'s match. withRouter will pass updated match, location, and history props to the wrapped component whenever it renders.

For this application, access to the history object was what I needed.  The history object keeps track of all the urls visited.  It also comes with a push method that pushes a new entry onto the history stack.  Pushing a new entry onto the history stack causes the browser to navigate to the pushed url.  This is how I could ‘redirect’ after the request to the backend creating a new game resolved. 

Here’s how I used withRouter and the history object in my code.  First, I used them in the component that held the form to create a new game.  

```
import React, {Component} from 'react';
import { withRouter } from 'react-router-dom';

class NewGame extends Component {
  constructor(props){
    super(props);
    this.state={
      playerName: ''
    };

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event){
    this.setState({playerName: event.target.value})
  }

  handleSubmit(event){
    event.preventDefault();
    this.props.newGame(this.state.playerName, this.props.history)
  }

  render(){
    return(
      <div className='NewGame'>
        <form onSubmit={this.handleSubmit}>
          <label> Player Name:
            <input 
              type='text' 
              value={this.state.playerName}
              onChange={this.handleChange}
            />
          </label>
          <br/>
          <br/>
          <input type='submit' value='Start New Game'/>
        </form>
      </div>
    )
  }
}

export default withRouter(NewGame);
```

I imported withRouter and wrapped it around NewGame so that I would have access to the history object via NewGame’s props. Then I passed the history object to the newGame action creator function that I’d passed down to NewGame via props. 

I also used the history object in the action creator function, newGame. 

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
      .then(game => history.push(`/games/${game.id}`))
      .catch(error => console.log(error))
  }
}
```

newGame sends a POST request to the backend with the playerName data from the form.  Then, once the request resolves, newGame uses the history object’s push method to add the url to the newly created game’s show page to the history stack, effectively redirecting to the new game’s show page.  

With all this in place, I could submit a playerName into the form to create a new game and not only would a new game be created, but the browser would navigate to the new game’s show page after it had been created. 

## Reflections

The puzzle of how to associate the models in my backend was relatively easy to solve.  While I had never heard of polymorphoic associations before this, there was a section of a Rails Guide on Active Record Associations to explain them.  I just had to understand the example provided and apply the pattern in my own project.

Finding and properly using withRouter and the history object was a good deal more complicated.  I had to spend more time reading Stack Overflow threads to get a sense of what tools could help, and I spent a bunch more time reading the resources I found to understand those tools.  

While these challenges frustrated me, I gained valuable knowledge from each.  I not only learned about new tools that I can use in any future React apps I develop, but I also practiced teaching myself.  I think that that last bit, practicing teaching myself about a new area of code,  is the most valuable lesson from this project.  It reinforced to me that with time and effort I can figure out how to make whatever I set my sights on.  


If you’re curious about the rest of Play The Game, check out the code for this project at: https://github.com/sgibson47/play-the-game

