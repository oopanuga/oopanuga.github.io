---
layout: post
title: A mini React workshop by Damjan Vujnovic
---

## ReactJS, yet another JS framework?

_Angular, Knockout, Ember, jQuery, Ext... React? This is going mad.
Why do I need to learn about another framework?_

That was my thinking when my colleague Richard Kotze told me about ReactJS for the first time.
He was telling so many great features about it and also we were possibly going to use it in the team, so that last Tuesday we went together to a React workshop by Damjan Vujnovic, all that hosted by the [Skills Matter](https://skillsmatter.com/meetups/7024-a-mini-hands-on-react-workshop).

This is the abstract of the talk:

> React is a Javascript library for building User Interfaces and in this workshop, we'll explore React, while building a simple app.

If you start reading about React first thing that you notice, it has been created by Facebook (now it is used in Instagram and few other places). Well, is that I reason to choose a framework or another?

Some of the key features of [React](http://facebook.github.io/react/) are:

- React is just the UI, it is about displaying and managing state on your view. Angular is an opinionated framework with controllers, models, modules, directives,...

- Updating state on the DOM. Yep, these guys are crazy, when something happens in the screen, they get the state of the DOM and they update it. It performs well because they created something called _Virtual DOM_.

- Can use JSX, XML systax in Javascript, what makes the code much simpler.

- It is totally possible to unit test, by using  [Jest](https://facebook.github.io/react/docs/test-utils.html).

Really interesting and a different approach to other frameworks. This is a talk by Facebook where they explain some of the differences and also the Flux architecture:

<iframe width="560" height="315" src="https://www.youtube.com/embed/nYkdrAPrdcw?list=PLb0IAmt7-GS188xDYE-u1ShQmFFGbrk0v" frameborder="0" allowfullscreen></iframe>

## The Game Of Life

During the workshop we used [The Game of Life](http://en.wikipedia.org/wiki/Conway%27s_Game_of_Life).
Damjan gave us the solution of the game in jquery and we have to implement it using React and TDD. For the unit tests we used Jasmine and the TestUtils class in React.

You can get the code in his [GitHub repository](https://github.com/SamuraiPrinciple/Game-Of-Life-React).

You need to install nodeJs. If you download the code you then need to do _npm install_ and you´ll get the node modules required to run it. You´ll see a gruntfile, so you just need to run _grunt_ and you should have a browser coming up. This displays the board and also runs the tests. The idea is that you don´t have to relaunch the application when doing some changes, just save the file and grunt is watching to update the files in the browser.

You should also see a bunch of failing specs, these are the React tests you need to implement. The idea is that you need to make these tests to pass while implementing another board of the game of life with React.

If you take a look at the code you should see:

- _src_ folder with complete implementation of the UI in jquery, almost empty file with react component and game of life file with the logic of the game (when does a cell die or live).

- _spec_ folder with the different unit test classes.

- _index.html_ is the view that loads the dependencies. 

Let´s go step by step and implement the game.

### Rendering the empty board

We need to make this test pass:

{% highlight javascript %}
it('should initially render all cells as dead', function () {
    expect(TestUtils.scryRenderedDOMComponentsWithClass(component, 'cell').length).toBe(100);
    expect(TestUtils.scryRenderedDOMComponentsWithClass(component, 'alive').length).toBe(0);
  });
{% endhighlight %}

Let´s see how this is done in jquery:

{% highlight javascript %}
Array.apply(null, {length: rows * columns})
      .forEach(function (element, index) {
        var row = Math.floor(index / columns), column = index % columns;
        jQuery('<div></div>')
          .appendTo(self.find('.grid'))
          .addClass('cell')
          .css({ top: 20 * row, left: 20 * column});
      });
{% endhighlight %}

We go through an array of rows*columns and add into the grid a cell in the appropiate coordinate.

Let´s try to do the same in React. All of the React components implement a _render_ function that is called when loading the component. 

Let´s create a new grid in there, we also add the button to do another iteration of the game of life:

{% highlight javascript %}
var GameOfLifeComponent = React.createClass({
  	render: function() {
    var self = this;
    return <div>
      <div className="grid" style={{width: 20 * this.props.rows, height: 20 * this.props.columns}}>
      </div>
      <button>Tick</button>
    </div>;
  }
});

{% endhighlight %}

There we go a 10*10 board. Let´s add the cells not alive in the begining. In React every component has a couple of interesting concepts:

#### state: This is the definition in the documentation.

> The state starts with a default value when a Component mounts and then suffers from mutations in time (mostly generated from user events). It's a serializable* representation of one point in time—a snapshot.

There we have properties representing the current state. You also have a bunch of method to manipulate the state, _setState_, _getCurrentState_, _getInitialState_,... The state is private for the component.

#### props: From the documentation:

> props (short for properties) are a Component's configuration, its options if you may. They are received from above and immutable as far as the Component receiving them is concerned. A Component cannot change its props, but it is responsible for putting together the props of its child Components.

I have a gut feeling that we need to add a couple of configuration props in our component, rows and columns. As we mentioned before the file _GameOfLife.js_ provides the logic for the game. It returns the cells that are alive.
With these ingredients, let´s set the initial state using those properties:

{% highlight javascript %}
var GameOfLifeComponent = React.createClass({
	getCurrentState: function () {
	  var self = this;
	  return {
	    cells: Array.apply(null, {length: self.props.rows * self.props.columns}).map(function (element, index) {
	      row = Math.floor(index / self.props.columns);
	      column = index % self.props.columns;
	      return {
	        row: row,
	        column: column,
	        isAlive: self.model.isCellAlive(row, column)
	      };
	    })
	  };
	},
	getInitialState: function () {
	  this.model = new SAMURAIPRINCIPLE.GameOfLife();
	  return this.getCurrentState();
	},
	render: function() {
	  var self = this;
	  return <div>
	    <div className="grid" style={{width: 20 * this.props.rows, height: 20 * this.props.columns}}>	      
	    </div>
	    <button>Tick</button>
	  </div>;
	}
});
{% endhighlight %}

Notice how we asign the model in the initial state and we use the props to know the number of rows and columns. For each of them we map to create a collection of cells with the row, column and isAlive property.

Now we now if a cell is alive in a given point in time. Let´s set all of the cells as dead in the beggining, we do that in the _render_ function:

{% highlight javascript %}
render: function() {
    var self = this;
    return <div>
      <div className="grid" style={{width: 20 * this.props.rows, height: 20 * this.props.columns}}>
        {
          this.state.cells.map(function (cell, index) {
            return <div key={index}
              className={'cell' + (cell.isAlive ? ' alive' : '')}
              style={{top: 20 * cell.row, left: 20 * cell.column}}
            />;
          })
        }
      </div>
      <button>Tick</button>
    </div>;
}
{% endhighlight %}

There we go our first test passes and we have a board full of dead cells. We go through every of cells in the state and we create a new div representing a cell. We add a key into every of the divs, this is to be with reordering in the [Virtual DOM](https://coderwall.com/p/jdybeq/the-importance-of-component-keys-in-react-js) that React uses.
Another interesting thing here about React is the style propery in the div class.

### Tick button

We need to implement the tick button, i.e. calculate a new iteration by triggering the GameOfLife logic.
Let´s see the test we need to pass:

{% highlight javascript %}
it('should invoke tick method when tick button is clicked', function () {
    var tickButton = TestUtils.findRenderedDOMComponentWithTag(component, 'button');

    TestUtils.Simulate.click(tickButton);

    expect(testGameOfLife.tick).toHaveBeenCalled();
});
{% endhighlight %}

We use the TestUtils to find the tick button and click in it. We use then jasmine´s _toHaveBeenCalled_.

Let´s add then a click event in the tick button, and let´s call the GameOfLogic business logic:

{% highlight javascript %}
<button onClick={this.model.tick}>Tick</button>
{% endhighlight %}

It is that simple. We have already a model wich points to GameOfLife. This later one exposes a tick method.

### A cell is alive when we click on it

When we click in a cell in the board, the cell should be alive. This is the next jasmine test:

{% highlight javascript %}
it('should invoke toggleCellState method when a cell is clicked', function () {
    var cell_3_4 = TestUtils.scryRenderedDOMComponentsWithClass(component, 'cell')[34];

    TestUtils.Simulate.click(cell_3_4);

    expect(testGameOfLife.toggleCellState).toHaveBeenCalledWith(3, 4, jasmine.any(Object), jasmine.any(String));
});
{% endhighlight %}

We take the cell in the position (3, 4), when we click on it, its state needs to be toggle. It should be so difficult, we already have a cell, let´s add the click event on it and delegate to the toggleCellState function when it is clicked:

{% highlight javascript %}
<div className="grid" style={{width: 20 * this.props.rows, height: 20 * this.props.columns}}>
{
  this.state.cells.map(function (cell, index) {
    return <div key={index}
      className={'cell' + (cell.isAlive ? ' alive' : '')}
      style={{top: 20 * cell.row, left: 20 * cell.column}}
      onClick={self.model.toggleCellState.bind(self, cell.row, cell.column)}
    />;
  })
}
{% endhighlight %}

We use _bind_ to be able to specify the scope (a cell) on which the toggleCellState function needs to be executed. 

There we go! Test passing, we are ready to click in a cell and it should be alive.

Ouch, it doesn´t... Why is that? Well, we are getting a new alive cell but we are not updating the state of the board.
This takes us to the last failing test.

### Updating board state

If we take a look at the GameOfLife.js business logic, it triggers a _boardStateChanged_ every time something changes, e.g. tick button or toggling the cell state (when clicking a cell in the board). 

{% highlight javascript %}
it('should render one cell as alive', function () {
	var cellElement;
	testGameOfLife.isCellAlive = function (row, column) {
	  return row === 3 && column === 4;
	};
	testGameOfLife.trigger('boardStateChanged');

	cellElement = TestUtils.scryRenderedDOMComponentsWithClass(component, 'alive');
	expect(cellElement.length).toBe(1);
});
{% endhighlight %}

In this test we trigger the event using the testGameOfLife spy.

We need to listen to that event in our board react component and update the state. To do that we use _componentDidMount_:

_Invoked once, only on the client (not on the server), immediately after the initial rendering occurs. At this point in the lifecycle, the component has a DOM representation which you can access via React.findDOMNode(this)._ React Documentation

Let´s use this, so that in the beggining we register this event and update state:

{% highlight javascript %}
componentDidMount: function () {
    var self = this;
    this.model.on('boardStateChanged', function () {
      self.setState(self.getCurrentState());
    });
}
{% endhighlight %}

When the _boardStateChanged_ is triggered in the model, we set the state based on the current state calculation.  
Voilá! All of the tests passing and we can have a beer while playing with our new Game of Life implemented using React.

## Recap

I hope you have enjoyed this as I did. After the talk I couldn´t help googling for things like: React vs Angular, React implementations, React in github...

As you see react is a really simple framework, it is based on a simple concept in my opinion, reacting to a change by updating the state of the DOM. Simple but new concept.

React is about the user interface, if you saw, in this exercise we were using jquery to drive the events, to define the model and also to implement the business logic. Therefore React gives freedom to have anything below the view. You can have an Angular router or a jquery component that does the Ajax requests to the server. 
As you probably are thinking this is, on the one hand more complicated than other frameworks like Angular where you get EVERYTHING packed in a nice MVC framework. On the other hand this is a much more flexible configuration and I really like the freedom you get with React. If you have a big ASP.net MVC application already using jQuery and you need to create a RIA component on top of it, React will be a great deal for sure.

We also found how easy is to do TDD and use the same Jasmine technology you are already used to, including a really useful TestUtils package.

If you feel curious (like I was) about how Facebook is using this and how it´s the overall architecture you need to read about the [Flux architecture](https://scotch.io/tutorials/getting-to-know-flux-the-react-js-architecture), it will be mind-blowing, I guarantee.

Looking forward to discuss about this with you! Cheers



