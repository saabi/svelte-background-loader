<script>
    import { onMount, tick } from 'svelte';

    export let component;

    let ready = false;
    let loaded = false;
    let loadedComponent;
    let container;
    let iframe;
    let doc;

    function load() {
        console.log('loading component');

        iframe.onload = () => {
            console.log('empty doc loaded');

            const doc = iframe.contentWindow.document;
            const body = doc.body;

            let observer = new MutationObserver(async function(mutations) {
                if (loaded) return;
                let promisses = []
                mutations.forEach(function(mutation) {
                    for (var i = 0; i < mutation.addedNodes.length; i++) {
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
                await tick();
                let tree = body.childNodes;
                for (let i = 0; i <tree.length; i++) {
                    const n = tree[i];
                    body.removeChild(n);
                    container.appendChild(n);
                }
            });
            observer.observe(body, { childList: true });

            const tree = new component({
                target: body,
                props: {}
            });

        }
    }
    $: component && ready && load();

    onMount( () => {
        console.log('background loader mounted');
        ready = true;
    })
</script>

<iframe bind:this={iframe} title='' style='display:none'></iframe>
{#if !loaded}
<slot/>
{:else}
<div bind:this={container} />
{/if}