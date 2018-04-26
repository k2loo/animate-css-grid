# Animate CSS Grid

![demo of animate-css-grid in action](https://furtive-discussion.surge.sh/grid-1.gif)

This small script makes it easy to make sure your CSS grid transitions gracefully from one state to another.
If the content of the grid changes, or if the grid or one of its children is updated with the addition or removal of a class, the grid will automatically transition to its new configuration.

[Basic Codepen Example](https://codepen.io/aholachek/pen/VXjOPB)

[React Example](https://codepen.io/aholachek/pen/mxwvmV)

## How to use it

Just call the `wrapGrid` method on your grid container, and optionally provide a config object as a second argument.
If the grid is removed from the page, the animations will automatically be cleaned up as well.

ES6 Module:

`yarn add animate-css-grid`

```js
import { wrapGrid } from animateCSSGrid

const grid = document.querySelector(".grid");
wrapGrid(grid);
```

Or from a script tag:

```html
<script src="https://unpkg.com/animate-css-grid@latest"></script>

<script>
  const grid = document.querySelector(".grid");
  animateCSSGrid.wrapGrid(grid, {stagger: true, duration : 600});
</script>
```

Optional config object:

```js
{
  // default is false
  stagger: true,
  // default is 250 ms
  duration: 500
  // default is 'Quadratic.InOut'
  easing: 'Sinusoidal.InOut'
}
```
[Available easing functions](https://sole.github.io/tween.js/examples/03_graphs.html).

Two functions are returned by the `wrapGrid` call that you probably won't need to use:

```js
import { wrapGrid } from animateCSSGrid

const grid = document.querySelector(".grid");
const { unwrapGrid, forceGridAnimation } = wrapGrid(grid);

// if you want the grid to transition after updating an inline style
// you need to call forceGridAnimation
grid.style.width = '500px'
forceGridAnimation()

// if you want to remove animations but not the grid itself
unwrapGrid()

```


## Requirements

1.  The updates to the grid will have to come from addition or removal of a class or element. Currently, inline style updates will not trigger transitions. (Although you can manually trigger transitions in that case by calling `forceGridAnimation()`)
2.  If a grid item has children, they should be surrounded by a single container element.

Example:

```html
<!-- grid style applied via a class -->
<ul class="some-grid-class-that-changes">
  <!-- each grid item has a single direct child -->
  <li class="grid-item">
    <div>
      <h3>Item title</h3>
      <div>Item body</div>
    </div>
  </li>
<div>
```

## How it works

The script registers a `MutationObserver` that activates when the grid or one of its children adds or loses a class or element. That means there's no need to remove the animations before removing the grid, everything should be cleaned up automatically.
It uses the FLIP animation technique to smoothly update the grid, applying a counter transform to the children of each item so that they do not appear distorted while the transition occurs.

It should work on container elements without CSS grid applied as well, but was developed and tested with CSS grid in mind.
