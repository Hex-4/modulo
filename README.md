<p align="center">
  <img src="https://hc-cdn.hel1.your-objectstorage.com/s/v3/852f4c65a182a407db39069d0cada34db79c612c_modulogo.png" />
  <br>
  <br>
  <i>time in perspective</i>
</p>

---

Modulo is a minimalist webapp that puts time into perspective. Choose a time range, and see how much of it is left in an intuitive grid of dots. Try out the live demo at [modulo-app.pages.dev](https://modulo-app.pages.dev).

## What

When you open Modulo, you will see:

- the dots
- a range selection bar
- save/reset buttons

Select a time range you'd like to see with the selection bar - the dots will update as you change the dropdowns. These dots also update automatically when time passes.

Click on any dot to open its customization menu. Here you can click on any of the colors on the left to apply it to the dot. The dashed, blank color resets it to whatever it was without customizations, also allowing the passage of time to modify it. On the right you can add a short note to the dot.

To save your color and note customizations, click the `save notes and colors to this view` button. This will persist your changes, even across refreshes. To reset your customizations (and the saved data), click the `reset saved data for this view` button.

## How

Modulo is made with Svelte and SvelteKit, with a library called lockr to interface with localStorage, and date-fns to help with time calculations. Once a time range is selected, Modulo calculates the amount of dots needed and creates them with Svelte {#each} blocks. Notes and colors are saved to an array, which is then put into localStorage when you click the save button, associated with the current view. Every time you change views, Modulo checks localStorage for saved data, and loads it if it exists.

## Why

Too often we take time for granted.
We say "I'll do that tomorrow" or "I should wait until I get a better opportunity". 
But a lot can happen in a week. Even more can happen in a month. You can change your life in a year. 

And yet — most of the time — we let it all slip by. 
Not intentionally. Just gradually. 
One “someday” at a time. 

This is why I built Modulo. To make time tangible. Intuitive.
To remind us to stop waiting - 
and start _making._

you got this <3
