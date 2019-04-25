<script>
    import { onMount, tick } from 'svelte';

    export let component;

    let ready = false;
    let loading = false;
    let loaded = false;
    
    let buffer;
    let container;

    function load() {
        loading = true;
        console.log('loading component');

        const tree = new component({
            target: buffer,
            props: {}
        });
    }
    $: component && ready && load();

    onMount( () => {
        console.log('background loader mounted');
        ready = true;

        let observer = new MutationObserver(async function(mutations) {
            console.log('buffer mutated');
            console.log(loading);
            if (loaded && !loading) {
                console.log('emptied, ignoring event');
                return;
            }
            console.log('component mounted');
            let promisses = []
            mutations.forEach(function(mutation) {
                for (let i = 0; i < mutation.addedNodes.length; i++) {
                    let node = mutation.addedNodes[i];
                    if (node.tagName === 'IMG') {
                        let promise = new Promise( (resolve, reject) => {
                            node.onload = () => {
                                resolve();
                            }
                        });
                        promisses.push(promise);
                    }
                }
            })
            await Promise.all(promisses);
            console.log('component rendered');
            loaded = true;
            loading = false;
            await tick();
            let oldTree = [...container.childNodes];
            for (let i = 0; i <oldTree.length; i++) { 
                const n = oldTree[i];
                container.removeChild(n);
            }
            let tree = [...buffer.childNodes];
            for (let i = 0; i <tree.length; i++) { 
                const n = tree[i];
                buffer.removeChild(n);
                container.appendChild(n);
            }
            // binding to parent should occur here.
            console.log('component transferred');
        });
        observer.observe(buffer, { childList: true });
    });
</script>

<div bind:this={buffer} style='display:none' />
{#if !loaded}
    <slot/>
{:else}
    <div bind:this={container} />
{/if}
{#if loading}
    <p>Loading and prerendering next component...</p>
{/if}
