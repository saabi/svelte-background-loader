# sapper-background-loader
This is a WIP component testing a potential approach towards loading and fully rendering 
components in the background prior to displaying them live.

For the moment it is meant only as a test. Do not use in production as it might be completely discarded later!

It's surely not correct , as it will probably need some support from Svelte that does not exist now.

This is what I have so far:

* A BackgroundLoader component that accepts a prop referencing a Svelte component and a placeholder slot (for now)
* when the prop changes, it instantiates the component and renders inside a <iframe style='display:none'>
* upon a MutationEvent it installs load handlers for all <img>s (for now... missing are handlers for any other resources that it might need to load)
* When they're all loaded, it prunes the iframe and transfers the nodes to the live document. (This is where Svelte should finish tying up bindings and probably needs some core coding, missing for now)

Also missing right now is setting properties passed from the parent

